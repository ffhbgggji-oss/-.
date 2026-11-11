# CartoonStudio repair status

## Included in this repair pass

- Replaced duplicated/corrupted skeletal editor, bone controller, camera controller, mobile UI and serializer scripts with single-definition implementations.
- Activated the skeletal editor module in the main scene. It expects a GLB containing a glTF skin/armature that Godot imports as `Skeleton3D`.
- Migrated Android plugin packaging from the obsolete `.gdap` descriptor to the Godot 4.2+ Android plugin v2 `EditorExportPlugin` packaging. GitHub Actions builds the AAR, stages it under `addons/`, installs the Gradle Android build template and then exports. The picker grants access only to the selected document; no broad Downloads permission is requested.
- Android import is intentionally limited to self-contained `.glb` in this pass. `.gltf` often references external `.bin` and texture files; selecting it now produces a clear message rather than a confusing partial import.
- MainStudio's legacy part animation playback now interpolates between adjacent authored/tween frames on every render update. The skeletal editor uses the same idea during playback. This reduces visible stepping on high-refresh devices while retaining editable frame records.
- Added a reusable 2D pose interpolation/tween utility in `scripts/Tween2D.gd`.

## Still pending / needs device verification

- A complete 2D drawing and cut-out puppet editor UI (layers, image-part import, 2D bone hierarchy and frame strip) is not implemented yet; only its interpolation core is present.
- 3D bone posing requires a skinned/rigged GLB. The editor does not create a skeleton or skin weights from an unrigged mesh.
- GitHub Actions must be run to validate the Android Gradle plugin dependency against the Godot 4.2.1 Android template and produce the APK. This workspace cannot claim a successful Android build until that workflow completes.
- Test the APK on Android 15: choose a small `.glb` from Downloads, pose at least two bones, generate 12+ tween frames, and play at 24 fps. Confirm the rig has no constraints/animation tracks overriding the imported Skeleton3D pose.

## Why motion could look jerky

The old app stepped through stored frames at 8 fps. A small number of generated tween frames therefore looked like distinct jumps. Playback now evaluates the pose between neighboring stored frames continuously at render rate, with a 24 fps authored-frame rate by default. For a long movement, generate more tween frames (for example 12–24); the frame strip still contains real editable records.
