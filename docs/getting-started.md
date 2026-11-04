# Getting started

## Prerequisites

- A JDK from 17 to 27. The app is compiled to Java 17 bytecode, and Gradle 9.8.2, the wrapper's version, runs on JVM 17 to 27. When Renovate updates the wrapper, check the new release's [compatibility matrix](https://docs.gradle.org/current/userguide/compatibility.html).
- The Android SDK with platform `android-35`, which `compileSdk` names. Point the build at the SDK in one of two ways: set `ANDROID_HOME`, or put `sdk.dir=<path>` in `Android/local.properties`, which Git ignores. Android Gradle Plugin accepts either.

Gradle is downloaded by the wrapper; its version is set in `Android/gradle/wrapper/gradle-wrapper.properties`.

## Build and test

Test:

```bash
./gradlew test
```

Build a debug APK:

```bash
./gradlew assembleDebug
```

Run the commands from the repo root; the root `gradlew` forwards to `Android/`.

The APK ends up in `Android/app/build/outputs/apk/debug/app-debug.apk`. Install it on a device or emulator running Android 8.0 (API 26) or later:

```bash
adb install Android/app/build/outputs/apk/debug/app-debug.apk
```

## Contents

| Path | Contents |
| --- | --- |
| `Android/app/` | The app: connection, SSH session and system information |
| `Android/` | The Gradle project with its own wrapper |
| `gradlew`, `gradlew.bat` | Root scripts that run `Android/gradlew` with `--project-dir Android`, so tools run from the root find the project |
| `gradle/` | Symbolic links to the wrapper files in `Android/gradle/wrapper/` |
| `docs/`, `zensical.toml`, `overrides/` | This documentation site |
