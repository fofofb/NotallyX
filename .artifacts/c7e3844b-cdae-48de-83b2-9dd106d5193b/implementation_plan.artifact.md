# Implementation Plan - Remove "DEBUG" from app name and add Chinese translations for Export settings

This plan addresses two requests:
1.  Remove "DEBUG" from the app name in the debug build configuration.
2.  Add missing Chinese (Simplified) translations for the "Export" settings menu.

## User Review Required

> [!NOTE]
> I will add translations for the "Export Mode" settings. The actual export formats (TXT, MD, PDF, etc.) are currently displayed using their enum names (e.g., "TXT", "MD") which are universal abbreviations. I will focus on the settings menu items first as requested.

## Proposed Changes

### [Component] Build Configuration

#### [MODIFY] [app/build.gradle.kts](file:///D:/Users/A/Desktop/NotallyX/app/build.gradle.kts)
- Remove " DEBUG" from the `app_name` `resValue` in the `debug` build type.

### [Component] Resources

#### [MODIFY] [values-zh-rCN/strings.xml](file:///D:/Users/A/Desktop/NotallyX/app/src/main/res/values-zh-rCN/strings.xml)
- Add Chinese (Simplified) translations for:
    - `export_mode`
    - `single_files`
    - `single_file_concatenated`
    - `include_separator`
    - `include_timestamp`

## Verification Plan

### Automated Tests
- Run `./gradlew assembleDebug` to ensure the project still builds.

### Manual Verification
- Deploy the debug build to a device/emulator.
- Verify that the app name in the launcher is "NotallyX" (without "DEBUG").
- Switch system language to Chinese (Simplified) and verify that the "Export" section in Settings is correctly translated.
