# Android SAF file-picker plugin template (Godot 4.2)

This adds the **source template** for a Godot Android plugin that launches Android's Storage Access Framework document picker, copies the selected document into the app's private files directory, and emits the resulting local path. It requests no broad storage permission.

## Repository files

- `android-plugin/` — Java plugin source and Gradle Android library project.
- `android/plugins/GodotAndroidFilePicker.gdap` — Godot plugin descriptor.
- `scripts/AndroidModelImporter.gd` — small GDScript bridge; desktop fallback uses Godot `FileDialog`.

## Important: source is not an APK or an AAR

Godot cannot package Java source directly. Build the Android library to create `GodotAndroidFilePicker.aar`, then copy that AAR to `android/plugins/GodotAndroidFilePicker.aar`. Enable the plugin in **Project > Export > Android > Plugins**, enable Android custom build if required by the selected export template, and export the APK. The AAR is a build artifact and normally should be produced by CI or a local Android/Gradle build; don't commit a generated AAR unless your project deliberately vendors binaries.

The Maven coordinate for the Godot Android plugin API must match the installed Godot Android export template. Confirm that `org.godotengine:godot:4.2.1.stable` resolves in your Android build environment before using the AAR. If it does not, use the `godot-lib` AAR shipped with the matching Godot 4.2.1 Android custom build template as the compile dependency. The plugin/API coordinate and exported APK should use the same Godot version.

## Use from GDScript

1. Add `AndroidModelImporter.gd` to a Node under the scene tree (or attach it to the model-import controller).
2. Connect `model_path_selected(path)` to the existing GLTF import method.
3. Connect `import_failed(message)` to the visible status label.
4. Call `open_model_picker()` when the user taps Import.
5. Load the emitted absolute app-private path with `GLTFDocument.append_from_file(path, state)`.

Do **not** request `READ_EXTERNAL_STORAGE` or `MANAGE_EXTERNAL_STORAGE` for this flow. SAF grants access only to the file the user selected, and the plugin immediately copies that document into private app storage.

## Validation still required

This source template has not been built with the project's Android toolchain or tested on a device in this session. Build the AAR and APK in CI, install the APK, select a `.glb` from Downloads, and verify import. A `.gltf` with external texture/buffer sidecars is not self-contained; prefer a `.glb` for a single-file Android import. A model must also contain a skinned armature imported as `Skeleton3D` before bone posing works.
