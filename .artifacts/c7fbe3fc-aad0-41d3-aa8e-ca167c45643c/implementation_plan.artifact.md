# Implementation Plan - Export Enhancements and Cleanup

This plan addresses three requests: translating the export mode menu, fixing PDF export failures for single-file (concatenated) mode, and removing the "DEBUG" suffix from filenames.

## Proposed Changes

### 1. Translation Enhancements
Add missing Chinese translations (CN and TW) for export-related settings.

#### [MODIFY] [strings.xml (zh-rCN)](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/res/values-zh-rCN/strings.xml)
- Add translations for `export_mode`, `single_files`, `single_file_concatenated`, `include_separator`, and `include_timestamp`.

#### [MODIFY] [strings.xml (zh-rTW)](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/res/values-zh-rTW/strings.xml)
- Add translations for `export_mode`, `single_files`, `single_file_concatenated`, `include_separator`, and `include_timestamp`.

### 2. PDF Export Fixes
Fix the issue where selecting PDF in "Single File" (concatenated) export mode results in a text file instead of a valid PDF. Also, improve the robustness of the PDF printing logic.

#### [MODIFY] [BaseNoteModel.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/presentation/viewmodel/BaseNoteModel.kt)
- Update `exportNotesToSingleFile` to handle `ExportMimeType.PDF`.
- When PDF is selected, generate a combined HTML string from all notes and call a new `exportPdfFile` overload.

#### [MODIFY] [ExportExtensions.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/utils/backup/ExportExtensions.kt)
- Add an overload for `exportPdfFile` that accepts a pre-generated HTML string instead of a single `BaseNote`.
- Refactor the existing `exportPdfFile` to use this new overload.

#### [MODIFY] [PdfExtensions.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/android/print/PdfExtensions.kt)
- Change `openFileDescriptor` mode from `"rw"` to `"w"` in `writeToFile`. This is safer for Storage Access Framework (SAF) URIs where reading might not be supported or necessary for printing.
- (Optional) Investigate if `file://` URIs need special handling for `openFileDescriptor` on modern Android versions.

### 3. Remove "DEBUG" from Filenames
Remove the `-DEBUG` suffix from the version name and app name in debug builds to satisfy the user's request for cleaner filenames.

#### [MODIFY] [build.gradle.kts](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/build.gradle.kts)
- In the `debug` build type:
    - Set `versionNameSuffix = ""` (or remove it).
    - Change `resValue("string", "app_name", "NotallyX DEBUG")` to `"NotallyX"`.
- This ensures that `BuildConfig.VERSION_NAME` and `R.string.app_name` (often used in filenames) do not contain "DEBUG".

## Verification Plan

### Automated Tests
- I'll rely on the existing build process to ensure no regressions in other areas.
- PDF export cannot be easily unit-tested as it involves `WebView` and the Android `Print` system, but the logic changes can be verified by code review.

### Manual Verification
- Deploy a debug build of the app.
- Go to Settings -> Export Mode and verify translations for "Export Mode" and its options in Chinese (CN and TW).
- Select multiple notes and export them as a "Single File" in PDF format. Verify that a valid PDF is created.
- Export a single note as PDF and verify it still works.
- Verify the APK filename and the app name in the launcher no longer contain "DEBUG".
- Verify any exported backup or file does not contain "DEBUG" if it was previously present.
