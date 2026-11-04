# Architecture

Bastion for Android is a standalone Kotlin app at an early stage. It shares no code with the other platforms. The app has a single activity, no persistence beyond `known_hosts` and no sync.

```text
        +-------------------------------------------+
        |   MainActivity (UI, worker threads)       |
        +------+----------------+-------------------+
               |                |
   ConnectionRequest /    SystemProbe
   ConnectionCredentials  (server overview)
   (input validation)           |
               |                |
        +------+----------------+-------------------+
        |   BastionSshSession                       |
        |   exec, interactive shell, heartbeats     |
        |   FailClosedKnownHostsServerKeyVerifier   |
        +---------------------+---------------------+
                              |
                       Apache MINA SSHD
```

## Parts

All source lives under `Android/app/src/main/kotlin/se/denied/bastion/`.

| File | Responsibility |
| --- | --- |
| `MainActivity.kt` | The only screen, built in code without XML layouts. It runs every blocking SSH call on a worker thread and posts results back to the UI thread. |
| `ConnectionRequest.kt` | `ConnectionCredentials` and `ConnectionRequest` validate host, port (1–65535), user, password and command before anything connects. |
| `ssh/BastionSshSession.kt` | The SSH core: password authentication, one-shot exec, one interactive shell with a PTY at a time, heartbeats and host key verification. |
| `ssh/SystemProbe.kt` | Collects an agentless server overview (load, uptime, memory, disks, OS, kernel, hostname, CPU count, running Docker containers) with one read-only command and parses the output. |

## What the app does

The screen has three actions, all using the same host, port, user and password fields:

- **Connect and run** opens a session, runs one command and shows its output.
- **Fetch server overview** runs `SystemProbe` and shows the parsed snapshot.
- **Open terminal** opens a persistent interactive shell. Lines are sent with **Send line**, output is streamed back and capped at 200,000 characters.

The UI text is in Swedish.

## SSH

- **Library:** Apache MINA SSHD (`sshd-core` and `sshd-common`).
- **Authentication:** password only. Key-based authentication, jump hosts, SFTP and agent forwarding are not implemented.
- **Heartbeats:** an authenticated session sends heartbeats every 15 seconds and is closed after 3 unanswered ones, so a dead connection does not hang silently.
- **Shell:** one interactive shell per session at a time. Its close callback fires exactly once, whether the shell is closed locally, reaches EOF or fails.

## Security boundary

- **Host keys** use persistent trust on first use (TOFU) against `known_hosts` in the app's private files directory. An unknown host is accepted on first connection and written to the file; a changed key for the same host and port is rejected.
- **Fail closed:** read, parse and write errors in `known_hosts` reject the connection and never fall back to `AcceptAllServerKeyVerifier`.
- **One writer at a time:** every session has its own verifier, but all of them share one process-wide lock around host key verification and always reload `known_hosts`. Concurrent first connections therefore cannot overwrite each other's pins, and a key enrolled by one session is seen by the next.
- **Credentials** are never stored. Passwords live only in the input field and in memory for the duration of a connection, and `ConnectionCredentials.toString()` masks the password.
- **Backups:** `android:allowBackup="false"` turns off Auto Backup and `adb backup`. The manifest has no `dataExtractionRules`, so on Android 12 and later a device-to-device transfer can still copy app data, including `known_hosts`. The file holds only public host keys.
- **Repo and tests:** real keys and passwords are never stored in the repo or in test data. The tests use placeholder passwords against their own in-process server.

## Tests

Unit tests under `Android/app/src/test/kotlin/se/denied/bastion/` run on the JVM:

- `ssh/BastionSshSessionTest.kt` starts a real Apache MINA SSHD server in the process and verifies authentication, exec, the interactive shell, heartbeats and host key handling without the system's own sshd.
- `ssh/FailClosedKnownHostsServerKeyVerifierTest.kt` releases many verifiers into `verifyServerKey` at once and checks that every concurrent first enrollment is kept and every replacement key rejected.
- `ConnectionRequestTest.kt` covers input validation.
- `ssh/SystemProbeTest.kt` covers snapshot parsing.

## Build

- **Build scripts and app dependencies** are separate dependency surfaces in Gradle. A build tool dependency must not be moved into the app's runtime to turn a check green; see [Operations](operations.md#the-dependency-graph).
