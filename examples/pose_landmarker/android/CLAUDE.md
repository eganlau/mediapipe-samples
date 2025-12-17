# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

Build and run using Android Studio or via command line:
```bash
./gradlew assembleDebug          # Build debug APK
./gradlew assembleRelease        # Build release APK
./gradlew installDebug           # Install debug build on connected device
./gradlew test                   # Run unit tests
./gradlew connectedAndroidTest   # Run instrumented tests (requires device)
```

Model files (pose_landmarker_*.task) are automatically downloaded during build via `download_tasks.gradle`.

## Architecture Overview

This is a MediaPipe Pose Landmarker Android demo app using Kotlin, CameraX, and Navigation component.

### Key Components

**PoseLandmarkerHelper** (`app/src/main/java/.../PoseLandmarkerHelper.kt`)
- Central wrapper around MediaPipe's `PoseLandmarker` API
- Supports three running modes: `IMAGE`, `VIDEO`, `LIVE_STREAM`
- Configurable model variants: LITE, FULL, HEAVY
- Configurable delegates: CPU, GPU
- Returns results via `LandmarkerListener` callback interface

**OverlayView** (`app/src/main/java/.../OverlayView.kt`)
- Custom View that draws detected pose landmarks and skeleton connections
- Handles coordinate scaling based on running mode (different scaling for live stream vs static images)

**Fragment Structure**
- `CameraFragment`: Live camera feed with real-time pose detection using CameraX
- `GalleryFragment`: Image/video selection from device gallery
- `PermissionsFragment`: Camera permission handling

**MainViewModel**: Persists detection settings (confidence thresholds, delegate, model) across configuration changes

### Data Flow (Live Stream)
1. CameraX `ImageAnalysis` captures frames on background executor
2. `PoseLandmarkerHelper.detectLiveStream()` converts ImageProxy to MPImage
3. MediaPipe runs async detection, results return via `returnLivestreamResult()`
4. `LandmarkerListener.onResults()` callback triggers UI update
5. `OverlayView.setResults()` receives landmarks and redraws

### Configuration
- Min SDK: 24 (Android 7.0)
- Target SDK: 34
- MediaPipe Tasks Vision: 0.10.29
- Uses ViewBinding for UI
