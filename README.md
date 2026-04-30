# Nibrāsk AR Navigation System

## Project Description

Nibrāsk AR Navigation System is a Unity-based augmented reality navigation prototype for indoor spaces such as airport terminals. The application uses the Android device camera, Unity AR Foundation, and ARCore plane tracking to place a virtual terminal map into the real world. After the user scans the floor and places the terminal origin, the system displays destination options, calculates a route through a waypoint graph, and renders camera-based AR path guidance using floor-aligned paths, directional arrows, distance updates, and arrival feedback.

The project is designed as an academic AR submission that demonstrates mobile AR interaction, spatial anchoring, pathfinding, real-time visual guidance, and user feedback through UI, audio, and haptics.

## Project Information

| Item | Details |
| --- | --- |
| Project name | Nibrāsk AR Navigation System |
| Engine | Unity |
| Unity version | 6000.3.2f1 |
| Platform | Android |
| Build artifact | `Build_13.apk` |
| AR framework | Unity AR Foundation with ARCore |
| Main use case | Camera-based AR navigation and indoor path guidance |

## Repository Contents

| Path | Purpose |
| --- | --- |
| `Assets/` | Unity scenes, scripts, prefabs, materials, UI, AR assets, and project content |
| `Packages/` | Unity package manifest and package lock files |
| `ProjectSettings/` | Unity project configuration, Android player settings, quality settings, and XR settings |
| `Documents/` | Supporting project documentation |
| `Build_13.apk` | Android APK build available in the project root |
| `README.md` | Project setup, build, usage, and technical documentation |

## APK Location

The submitted Android build is available at:

```text
Build_13.apk
```

Absolute path in the current project copy:

```text
/Users/fahaad77/Downloads/Nibrāsk AR Navigation System/Build_13.apk
```

> Note: The folder name on disk may contain the accented project name `Nibrāsk`. The APK itself is located in the project root.

## Requirements

### Android Device Requirements

- Android phone or tablet with ARCore support.
- Camera permission enabled.
- Sufficient lighting and a visible textured floor or flat surface for plane detection.
- Android version compatible with the project settings. The project is configured with Android minimum SDK version 30.

### Development Requirements

- Unity `6000.3.2f1` or a compatible Unity 6 editor version.
- Android Build Support installed through Unity Hub.
- Android SDK and NDK modules installed with Unity.
- ARCore-compatible Android device for real-device testing.

## How to Run the APK

1. Connect an ARCore-compatible Android device to the computer, or transfer `Build_13.apk` to the device.
2. Enable installation from unknown sources if Android blocks direct APK installation.
3. Install the APK:

   ```bash
   adb install Build_13.apk
   ```

   If an older version is already installed, use:

   ```bash
   adb install -r Build_13.apk
   ```

4. Open the installed application on the Android device.
5. Grant camera permission when prompted.
6. Move the device slowly so ARCore can detect the floor.
7. Tap a detected floor plane to place the terminal origin.
8. Select a destination from the floating AR menu.
9. Follow the rendered path, arrows, and on-screen distance/ETA feedback until arrival.

## Controls and Interaction

| Action | Control |
| --- | --- |
| Scan environment | Move the Android device camera slowly across the floor |
| Place terminal origin | Tap on a detected horizontal plane |
| Select destination | Tap a destination button in the floating AR menu |
| Navigate | Physically walk while keeping the camera pointed toward the path |
| Track progress | Follow the AR path line, arrows, checkpoint progress, distance, and ETA |
| Reroute | Move back toward the visible path; the app can recalculate after off-route detection |
| Navigate again | Use the arrival panel after reaching a destination |

The application is intended for handheld mobile AR. The camera view is the primary interface, and the user interacts by physically moving the device and tapping AR UI elements.

## Build Instructions from Unity

1. Open Unity Hub.
2. Add this project folder:

   ```text
   /Users/fahaad77/Downloads/Nibrāsk AR Navigation System
   ```

3. Open the project with Unity `6000.3.2f1` or a compatible Unity 6 editor.
4. Make sure Android Build Support, Android SDK, and Android NDK are installed.
5. In Unity, open:

   ```text
   File > Build Profiles
   ```

   or, in older Unity layouts:

   ```text
   File > Build Settings
   ```

6. Select `Android` as the target platform.
7. Click `Switch Platform` if Android is not already active.
8. Confirm that the main scene is included in the build scenes list.
9. Confirm Player Settings:
   - Package name: `com.nibrask.arnav`
   - Minimum SDK: Android API 30
   - Target architecture: ARM64
   - XR provider: ARCore
10. Connect an Android device and choose `Build And Run`, or choose `Build` to generate an APK.
11. Save the output APK with a clear name such as:

   ```text
   Build_13.apk
   ```

Recommended build output folder for future builds:

```text
Builds/Android/
```

## Packages Used

The project uses the following main Unity packages:

| Package | Version | Purpose |
| --- | --- | --- |
| `com.unity.xr.arfoundation` | 6.3.1 | Cross-platform AR session, plane tracking, raycasting, anchors, and AR camera support |
| `com.unity.xr.arcore` | 6.3.1 | Android ARCore provider |
| `com.unity.xr.arkit` | 6.3.1 | iOS AR provider package included in the manifest, although the submitted platform is Android |
| `com.unity.xr.interaction.toolkit` | 3.3.0 | XR interaction components and samples |
| `com.unity.xr.management` | 4.5.3 | XR plug-in management |
| `com.unity.inputsystem` | 1.17.0 | Input handling |
| `com.unity.render-pipelines.universal` | 17.3.0 | Universal Render Pipeline |
| `com.unity.mobile.android-logcat` | 1.4.6 | Android device logging during development |
| `com.unity.timeline` | 1.8.9 | Timeline support |

## Known Issues and Limitations

- The app requires a real ARCore-compatible Android device. It is not intended to be fully tested in the Unity editor alone.
- Plane detection depends on lighting, floor texture, camera quality, and available tracking features.
- The indoor terminal map is represented by predefined waypoint and destination data rather than live building-map import.
- The route is aligned relative to the user-placed terminal origin, so inaccurate placement can shift the navigation path.
- GPS is not used. The system is designed for local AR spatial guidance after the user places the origin.
- AR tracking drift can occur during long sessions or in visually repetitive environments.
- The APK is a prototype build for academic demonstration and is not a production airport-navigation deployment.
- Some development packages and sample assets may remain in the project for demonstration and testing purposes.

## Assets and Credits

- Developed as the Nibrāsk AR Navigation System academic project.
- Built with Unity and Unity AR Foundation.
- Android AR functionality uses Google ARCore through Unity's ARCore XR provider.
- XR interaction support uses Unity XR Interaction Toolkit.
- Rendering uses Unity Universal Render Pipeline.
- UI text rendering uses TextMesh Pro where applicable.
- Template and sample resources may include Unity Mobile AR Template assets and XR Interaction Toolkit sample assets.
- All custom scripts, waypoint logic, navigation systems, and project-specific AR UI behavior are part of this project unless otherwise noted in source files or Unity package metadata.

## Technical Architecture

The following sections describe the internal system design and implementation details.

### System Architecture

The project is built on a highly decoupled, state-driven architecture. Systems communicate with each other exclusively through a centralized event bus (`AppEvents`) and state machine (`AppStateManager`).

#### 1. State Management (`AppStateManager.cs`)

The application is strictly driven by an `AppState` enum:

- **`Onboarding`**: Initial instructions display.
- **`Scanning`**: Floor detection and terminal anchor placement.
- **`DestinationSelection`**: Display floating 3D menu.
- **`Navigating`**: Active traversal mode with path rendering and distance tracking.
- **`Arrival`**: User has reached their destination.

#### 2. Event Bus (`AppEvents.cs`)

All cross-component communication happens here.

- **AR Events:** `OnFloorDetected`, `OnTerminalAnchorPlaced`
- **Navigation Actions:** `OnDestinationSelected`, `OnNavigationStarted`, `OnArrived`, `OnNavigateAgain`
- **Tracking Updates:** `OnDistanceUpdated`, `OnCheckpointReached`, `OnOffRoute`, `OnBackOnRoute`, `OnRouteRecalculated`

This architecture allows the UI, Audio, Haptics, and AR visualizers to react to events without tightly coupling their references to the Navigation engine.

## Data and Graph Logic (`Nibrask.Data` and `Nibrask.Navigation`)

### Terminal Map Data

The layout of the environment is defined via ScriptableObjects.

- **`TerminalMapData.cs`**: Contains the full network of abstract waypoints (nodes and relative positions) and references to all destinations. It also features a `scaleFactor` to expand or shrink the entire terminal without modifying individual coordinates.
- **`DestinationData.cs`**: Represents specific POIs (Gates, Services, Exits, Restrooms). Contains UI metadata (Icons, Flight Numbers, Names) and hooks into the waypoint graph via a `nearestWaypointIndex`.

### Graph Building (`WaypointGraph.cs` and `WaypointNode.cs`)

When `OnTerminalAnchorPlaced` is fired by the AR system:

1. `WaypointGraph.BuildGraph()` runs.
2. It spawns unseen `WaypointNode` GameObjects in the scene.
3. Each node's position is calculated **relative to the terminal AR anchor**, making the entire graph shift and rotate perfectly to match wherever the user tapped on the floor.
4. Nodes parse their `connectedNodeIds` to create bidirectional edges for traversal.

### Pathfinding (`PathFinder.cs`)

An optimized **A* (A-Star) search algorithm** takes a start node and an end node, using the world-space distance between nodes as both the *g-cost* and *h-cost* heuristic. It returns a `List<WaypointNode>` representing the shortest path.

## Navigation Engine (`Nibrask.Navigation`)

The navigation engine orchestrates the journey from point A to point B.

### `NavigationManager.cs`

When a destination is selected:

1. Projects the user's `Camera.main` position down to the floor (Y = Terminal Anchor Y) to eliminate eye-height bias.
2. Queries `WaypointGraph` for the closest starting node based purely on XZ (horizontal) distance.
3. Fetches the destination node and asks `PathFinder` for the route.
4. Hands the resulting `List<WaypointNode>` to the visualizers and `DistanceTracker`.
5. If an `OnOffRoute` event fires, it automatically initiates a `RecalculateRoute` loop with a 1.5 second cooldown.

### `DistanceTracker.cs`

Runs on an `InvokeRepeating` loop every 0.2 seconds to monitor user progress.

- **Checkpoint Parsing:** Checks if the user is within `checkpointReachDistance` (1.2 m) of their next target `WaypointNode`. If so, advances the index, fires `OnCheckpointReached`, and trims the path line.
- **Distance to Path:** Uses geometric point-to-line-segment math horizontally on the XZ plane to calculate how far the user has strayed from the active line segment. If distance exceeds `offRouteThreshold` (3.0 m), fires `OnOffRoute`.
- **Arrival Detection:** Checks distance directly against the absolute final destination. If within `arrivalDistance` (1.5 m), fires `OnArrived`.
- **Time Calculation:** Yields `distanceMeters` and `estimatedTimeSeconds`, assuming 1.4 m/s walking speed, through `OnDistanceUpdated`.

## Visuals and UI

### Navigation Renderers

- **`PathRenderer.cs`**: Uses a `LineRenderer` to draw a flowing line connecting all path nodes. Trims itself backwards natively as the user passes checkpoints. Uses a cloned runtime material instance to animate UV offsets, creating a forward-flowing chevron effect on the line without corrupting the material asset in the Editor.
- **`ArrowGenerator.cs`**: Spawns physical 3D arrow prefabs along the path segments. Arrow spacing is configured to approximately 1.2 m. Arrows are managed through an object pool. As the user passes checkpoints, arrows behind the user are disabled to keep the AR view clean.

### User Interface (`Nibrask.UI`)

- **`OnboardingUI`**: Manages the start screen, transitions, and loading bar during floor detection. Shuts off `Update` loop calculations when invisible.
- **`DestinationSelectionMenu`**: A `RenderMode.WorldSpace` floating canvas. Dynamically instantiates categorized buttons based on the `TerminalMapData`. Uses the `Billboard.cs` component to face the camera while locking pitch and roll so it stays upright.
- **`GateInfoPanel`**: Persistent floating UI panel anchored safely in the user's view, displaying current destination name, active ETA, and distance updates.
- **`FeedbackPanel`**: Handles the top-of-screen progress bar, showing `Checkpoints: X/Y`.
- **`ArrivalPanel`**: Spawns 1.2 m in front of the user when `OnArrived` fires, confirms arrival, and provides a `Navigate Again` button that loops the state back to `DestinationSelection`.

## AR Subsystems (`AREnvironmentManager.cs`)

Manages the core AR Foundation components: `ARPlaneManager`, `ARRaycastManager`, and `ARAnchorManager`.

- **Scanning State:** Enables plane visuals so the user can see horizontal planes being tracked. Touch input runs raycasts against planes.
- **Anchor Placement:** When the user taps a valid plane, an `ARAnchor` is spawned. This becomes the terminal origin.
- **Navigation State:** Disables the plane visualizers. Raycasting remains mathematically functional, but the floor grid disappears to provide an uncluttered experience while following the navigation line.

## Feedback Managers (`Nibrask.Feedback`)

- **`HapticFeedbackManager.cs`**: Uses `AndroidJavaClass` to access native Android vibrator systems. Triggers varied pulse durations based on events, including light pulses for checkpoints and longer vibration for off-route or arrival feedback.
- **`AudioFeedbackManager.cs`**: Uses pooled `AudioSource` components with cooldown mechanisms to play spatial and UI sound effects without excessive overlap.

## Debug and Utilities

- **`CanvasDebugger.cs`**: An invisible GUI element that overlays raw manual force-transitions for `AppStateManager`, supporting editor and device testing without requiring physical space.
- **`DestinationMarkerSpawner.cs`**: Tooling to spawn generic physical markers such as cubes or specific prefabs mapped to `DestinationTypes` such as Gates and Restrooms. It also spawns TextMesh Pro world-space labels above markers and dynamically adjusts backplate width and scale for readability.

## Important Edge Cases Addressed

1. **3D Math in a 2D Floorplan:** Because a user holds a phone at approximately 1.6 m high, regular `Vector3.Distance` checks between the camera and a node on the floor inflate the result. `DistanceTracker` and `WaypointGraph.FindNearestNode` project camera positions down to the floor's Y-level before proximity matching.
2. **Start == End Reroutes:** If a user reroutes while physically standing on their destination, the path generated is a zero-length segment (`List<WaypointNode> {start, start}`). Handling this prevents `LineRenderer.SetPositions` from crashing due to requiring at least two nodes, allowing the system to trigger `OnArrived` on the immediate frame tick.
