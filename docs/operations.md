# Operations and verification

## Before every push

CI has no dedicated build or test job. CodeQL compiles the app during its analysis but runs no tests, so the commands below are the only test gate and run locally before every push:

Test:

```bash
./gradlew test
```

Build a debug APK:

```bash
./gradlew assembleDebug
```

When `docs/`, `zensical.toml` or `overrides/` change, also build the documentation:

```bash
pip install --upgrade zensical
```

```bash
zensical build --strict
```

When `.github/workflows/wiki-sync.yml` changes, also run the tests for its embedded Python script:

```bash
python3 -m unittest tests/test_wiki_sync.py
```

## In CI

| Check | When | What it does |
| --- | --- | --- |
| **CodeQL** (GitHub's default setup) | Every pull request and `main` | Analyzes `java-kotlin` and `actions`. For Kotlin it runs Gradle autobuild, which calls the root `testClasses` task; `Android/build.gradle.kts` maps that task to `:app:assembleDebug`. Errors and high or critical security alerts block merging. |
| **Automatic Dependency Submission (Gradle)** | Every pull request and `main` | GitHub resolves the Gradle build and submits it to the dependency graph (`submit-gradle`), which feeds Dependabot alerts. |
| **Documentation** (`.github/workflows/docs.yml`) | Pull requests that change `docs/`, `zensical.toml` or the docs and release workflows | Builds the site strictly with Zensical. Changes to `overrides/` alone do not trigger it, so build locally. |
| **Release** (`.github/workflows/release.yml`) | Every push to `main` | Computes the next version and waits for `.github/release-required-checks`; see [Releases](release.md). |
| **Wiki sync** (`.github/workflows/wiki-sync.yml`) | Pushes to `main` that change `README.md` or `docs/` | Regenerates the Wiki's Home, Documentation and sidebar pages as links to the documentation in the repo. Link extraction from `docs/index.md` is a linear scan, so large or malformed input cannot stall the job. `tests/test_wiki_sync.py` covers it, but CI does not run it. |

The documentation workflow also runs every hour and on each published release, and republishes the site from the latest stable release tag.

`main` is protected by a ruleset: changes go through a squash or rebase merged pull request with linear history, signed commits, resolved review conversations and CodeQL without errors or high-severity alerts.

## The dependency graph

Keep the build script's `classpath` apart from the app's runtime dependencies. Android Gradle Plugin pulls in build-only libraries, and some of them can have known vulnerabilities. `Android/build.gradle.kts` handles that on the build script's classpath only:

- `classpath(...)` declares Kotlin Gradle Plugin and Commons Lang explicitly, so Renovate and Dependabot can update them.
- `configurations.classpath { resolutionStrategy.force(...) }` selects patched versions of the remaining build-only transitives (Bouncy Castle, jose4j, JDOM).

If GitHub flags a vulnerable build tool dependency that only exists transitively, select the fixed version in the configuration that actually resolves it when Gradle and AGP support it, instead of filtering it out or moving it into the app. None of these dependencies may end up in the app's runtime.

## Security rules

- Private keys, tokens and passwords never go into the repo, logs or test data.
- Values that reach a shell are quoted and preferably passed through `env:`.
- A dependency is updated in the manifest that actually owns it.
