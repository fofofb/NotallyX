# Implementation Plan - Fix Build Error and Add "Copy to Clipboard" for Notes

The goal is twofold:
1. Fix a Gradle build error caused by missing signing properties in `gradle.properties`.
2. Add a "Copy" option to the action mode menu in the main interface to copy note contents to the system clipboard.

## Proposed Changes

### [Component] Build Configuration

#### [MODIFY] [build.gradle.kts](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/build.gradle.kts)
- Update `signingConfigs` to use `getOrElse` instead of `get()` when accessing `RELEASE_STORE_FILE` and other related properties. This prevents the build from failing when these properties are not defined (e.g., in a fresh development environment).

### [Component] UI Strings

#### [MODIFY] [strings.xml](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/res/values/strings.xml)
- Add a new string resource `copied_to_clipboard` to provide feedback to the user after copying.

### [Component] Presentation Logic

#### [MODIFY] [ModelFolderObserver.kt](file:///D:/Users/A/Desktop/notallyX/NotallyX/app/src/main/java/com/philkes/notallyx/presentation/activity/main/ModelFolderObserver.kt)
- Add a `copyToClipboard()` function to extract note content and copy it using the `copyToClipBoard` extension from `AndroidExtensions.kt`.
- Update `initNotesFolderMenu`, `initArchivedFolderMenu`, and `initDeletedFolderMenu` to include the "Copy" menu item.
- The "Copy" item will be available even when multiple notes are selected, in which case it will concatenate their contents with a separator.

## Verification Plan

### Manual Verification
1. Open the app and create a text note and a list note.
2. Long-press a note to enter selection mode.
3. Verify that the "Copy" option appears in the top menu.
4. Click "Copy" and verify that a toast message "Copied to clipboard" appears.
5. Paste the content into another app (e.g., a messaging app or a text editor) to verify the content (title + body/list items).
6. Select multiple notes and verify that clicking "Copy" copies the concatenated content of all selected notes.
7. Verify that the "Copy" option is also available in the Archived and Deleted folders.
