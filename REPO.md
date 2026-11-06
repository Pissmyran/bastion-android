# REPO.md

Bastion for Android: an SSH client for Android written in Kotlin. The other platforms and the shared hub live in separate repos.

## Stack

- Kotlin, Gradle (Android Gradle Plugin), Apache MINA SSHD
- Zensical for the documentation site
- GitHub Actions, CodeQL, Renovate

## Commands

Run from the repo root. The root `gradlew` forwards to `Android/`.

- Test the app: `./gradlew test`
- Build a debug APK: `./gradlew assembleDebug`
- Documentation: `zensical build --strict`
- Workflow script tests: `python3 -m unittest tests/test_wiki_sync.py`

There is no lint command. The Gradle commands need the Android SDK (`ANDROID_HOME` or `Android/local.properties`) and access to Google's Maven repository; see `docs/getting-started.md`.

## Layout

- `Android/` is the Gradle project; the root `gradle/` only links to its wrapper.
- `.github/scripts/` holds the release logic; `tests/` covers the embedded script in `wiki-sync.yml`.
- `.github/release-baseline` and `.github/release-required-checks` steer the release.

## Release

Merges to `main` are released by `release.yml` as a SemVer tag and GitHub Release, once the required checks are green. The PR title (Conventional Commits) sets the version bump. Nothing is published to an app store. Details in `docs/release.md`.

## Rules for this repo

- CI has no build or test job, so run the Gradle commands locally before every push; run the documentation build too when `docs/`, `zensical.toml` or `overrides/` change.
- Sync format changes are made in Bastion for Apple, not here.
