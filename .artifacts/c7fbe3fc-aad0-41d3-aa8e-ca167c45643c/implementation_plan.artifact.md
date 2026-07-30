# Implementation Plan - Enhanced Export and Copy Options

This plan outlines the changes required to add flexible export and copy options to NotallyX, including the ability to export all notes into a single file and customize the export style (separators and timestamps). It also includes adding a GitHub Action for automatic builds.

## User Review Required

> [!IMPORTANT]
> - New preferences will be added under a new "Export" section in Settings.
> - The "Copy to Clipboard" feature for multiple notes will now respect the "Include Separator" and "Include Timestamp" settings.
> - Exporting multiple notes to a "Single File" will prompt the user to choose a destination file instead of a folder.

## Proposed Changes

### [Settings & Preferences]

#### [MODIFY] [Preference.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/presentation/viewmodel/preference/Preference.kt)
- Add `ExportMode` enum with `SINGLE_FILES` and `SINGLE_FILE`.

#### [MODIFY] [NotallyXPreferences.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/presentation/viewmodel/preference/NotallyXPreferences.kt)
- Add `exportMode` preference.
- Add `exportIncludeSeparator` preference (default: true).
- Add `exportIncludeTimestamp` preference (default: false).

#### [MODIFY] [strings.xml](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/res/values/strings.xml)
- Add strings for the new export settings and options.

#### [MODIFY] [fragment_settings.xml](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/res/layout/fragment_settings.xml)
- Add a new "Export" section with the new settings.

#### [MODIFY] [SettingsFragment.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/presentation/activity/main/fragment/settings/SettingsFragment.kt)
- Implement UI logic to observe and save the new export preferences.

---

### [Export & Copy Logic]

#### [MODIFY] [ModelExtensions.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/data/model/ModelExtensions.kt)
- Update `toTxt` to support custom timestamp inclusion.

#### [MODIFY] [ModelFolderObserver.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/presentation/activity/main/ModelFolderObserver.kt)
- Update `copyToClipboard` to use the new preferences for separators and timestamps.

#### [MODIFY] [ExportExtensions.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/utils/backup/ExportExtensions.kt)
- Update `exportPlainTextFile` to respect the `exportIncludeTimestamp` preference.
- Modify `exportNotes` to handle `SINGLE_FILE` mode:
    - If `SINGLE_FILE` and multiple notes are selected, launch `ACTION_CREATE_DOCUMENT` instead of `ACTION_OPEN_DOCUMENT_TREE`.
- Add a new helper `exportSelectedNotesToSingleFile` in `BaseNoteModel` (or update existing) to handle concatenation of notes with separators.

#### [MODIFY] [BaseNoteModel.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/presentation/viewmodel/BaseNoteModel.kt)
- Add `exportSelectedNotesToFile` (for multiple notes to single file) and update `exportNotesToFolder` if needed.

---

### [Infrastructure]

#### [NEW] [android-build.yml](file:///D:/Users/A/Desktop/notallyX/NotallyX/.github/workflows/android-build.yml)
- Add a GitHub Action workflow to build the project (APK/Bundle) on push to `main` or manual trigger.

## Verification Plan

### Automated Tests
- Build the project using `./gradlew assembleDebug` to ensure no regressions.
- (Optional) Add unit tests for `toTxt` and note concatenation logic.

### Manual Verification
1. Open Settings and verify the new "Export" section.
2. Change "Export Mode" to "Single File".
3. Select multiple notes in the main list and click "Export" (TXT).
4. Verify that it prompts for a file location and the resulting file contains all notes.
5. Toggle "Include Separator" and "Include Timestamp" and verify the exported file content.
6. Verify the "Copy to Clipboard" behavior respects the new settings.
7. Push changes to GitHub and verify that the "Android Build" action runs successfully.
