# Getting started

## Prerequisites

- A JDK, version 17 or later. The app is compiled to Java 17 bytecode, and the build does not require an exact JDK version.
- The Android SDK with platform `android-35`, which `compileSdk` names. Set `ANDROID_HOME` to the SDK, or put `sdk.dir=<path>` in `Android/local.properties`, which Git ignores.

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

The APK ends up in `Android/app/build/outputs/apk/debug/app-debug.apk`. Install it on a device or emulator running Android 8.0 (API 26) or later with `adb install`.

## Contents

| Path | Contents |
| --- | --- |
| `Android/app/` | The app: connection, SSH session and system information |
| `Android/` | The Gradle project with its own wrapper |
| `gradlew`, `gradlew.bat` | Root scripts that run `Android/gradlew` with `--project-dir Android`, so tools run from the root find the project |
| `gradle/` | A copy of the wrapper files in `Android/gradle/wrapper/` |
| `docs/`, `zensical.toml`, `overrides/` | This documentation site |
