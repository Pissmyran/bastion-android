# Bastion for Android

**Documentation:** [myrpisset.github.io/bastion-android](https://myrpisset.github.io/bastion-android/)

Bastion is an SSH client. This repo holds the version for Android, built with Kotlin, Gradle and Apache MINA SSHD. The other platforms have their own repos: [Bastion for Apple](https://github.com/Myrpisset/bastion-apple), [Bastion for Linux](https://github.com/Myrpisset/bastion-linux), [Bastion for Windows](https://github.com/Myrpisset/bastion-windows). The [Bastion hub](https://github.com/Myrpisset/bastion) ties them together.

## Contents

| Path | Contents |
| --- | --- |
| `Android/app/` | The app: connection, SSH session and system information |
| `Android/` | The Gradle project with its own wrapper |
| `gradlew`, `gradlew.bat` | Root scripts that run `Android/gradlew` with `--project-dir Android`, so tools run from the root find the project |
| `gradle/` | A copy of the wrapper files in `Android/gradle/wrapper/` |
| `docs/`, `zensical.toml`, `overrides/` | The documentation site |

## Getting started

Test:

```bash
./gradlew test
```

Build a debug APK:

```bash
./gradlew assembleDebug
```

Run the commands from the repo root; the root `gradlew` forwards to `Android/`. You need JDK 17 or later and the Android SDK with platform `android-35`, with `ANDROID_HOME` set. See [Getting started](https://myrpisset.github.io/bastion-android/getting-started/) for details.

## More

- [The documentation](https://myrpisset.github.io/bastion-android/): getting started, architecture, operations, sync format and releases
- [SECURITY.md](SECURITY.md): report a vulnerability
- [CONTRIBUTING.md](CONTRIBUTING.md): contribute
