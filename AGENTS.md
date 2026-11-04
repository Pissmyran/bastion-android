# AGENTS.md – bastion-android

Bastion for Android: the SSH client for Android, built with Kotlin, Gradle and Apache MINA SSHD.

Read the shared rules at the start of every session: [GOVERNANCE.md](https://raw.githubusercontent.com/Myrpisset/.github/main/GOVERNANCE.md) and [BASTION.md](https://raw.githubusercontent.com/Myrpisset/.github/main/BASTION.md).

## Read first

1. [docs/architecture.md](docs/architecture.md): components and security boundaries.
2. [docs/operations.md](docs/operations.md): verification before pushing.
3. [docs/sync-format.md](docs/sync-format.md): when the host list or sync is involved.
4. [docs/release.md](docs/release.md): when a pull request title or the release process is involved.
5. [docs/agents/issue-tracker.md](docs/agents/issue-tracker.md) and [docs/agents/domain.md](docs/agents/domain.md): before working with issues or the domain.

## Commands

```bash
./gradlew test
./gradlew assembleDebug
```

Run from the repository root; the root `gradlew` forwards to `Android/`. You need a JDK from 17 to 27 (the range Gradle 9.8.2 runs on) and the Android SDK with platform `android-35`, with `ANDROID_HOME` set or `sdk.dir` in `Android/local.properties`.

When `docs/`, `zensical.toml` or `overrides/` change, also build the documentation:

```bash
zensical build --strict
```

When `.github/workflows/wiki-sync.yml` changes, also run:

```bash
python3 -m unittest tests/test_wiki_sync.py
```

## Rules

- **Build scripts and runtime stay apart** in Gradle. Build-only transitives are patched where they resolve: on the buildscript classpath with `resolutionStrategy.force`, and on the lint tool classpath (`androidLintTool`) with dependency constraints. See `docs/operations.md`.
- **Language:** the app's UI text is Swedish (see [docs/architecture.md](docs/architecture.md#what-the-app-does)).
- **Extra care:** host key verification and Gradle dependencies.
