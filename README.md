# PlayTrigger — Android Studio project

Native Kotlin + Jetpack Compose application. Minimum Android 8.0 (API 26), compile/target SDK 36. Uses YouTube's official IFrame Player API in a WebView; it does not download or extract video streams.

## Open and build

1. Install the current stable Android Studio and Android SDK Platform 36.
2. Open this `PlayTrigger` folder in Android Studio.
3. Allow Gradle sync. Use JDK 17 when prompted.
4. Connect an Android 8.0+ device or start an emulator and press **Run**.
5. Debug APK: **Build → Build Bundle(s) / APK(s) → Build APK(s)**. Output: `app/build/outputs/apk/debug/app-debug.apk`.
6. Release APK: **Build → Generate Signed Bundle / APK → APK**, create/select a keystore, choose the `release` variant, and finish. Output: `app/build/outputs/apk/release/app-release.apk`.

The release build must be signed before distribution. Keep the keystore and passwords private.

**Wrapper note:** this archive includes `gradle-wrapper.properties` but not the wrapper bootstrap JAR/scripts. If you have Gradle 8.13 installed locally, run `gradle wrapper --gradle-version 8.13` from this project folder, then open the project in Android Studio. Alternatively, configure Android Studio to use a local Gradle 8.13 installation.

## Playback safeguards

- The embedded player is initialized with `autoplay=0` and uses `cueVideoById` when a URL is loaded; cueing prepares the video without starting playback.
- The app's PLAY button calls `playVideo()`.
- Activity `onPause()` and `onStop()` pause the IFrame player and suspend the WebView.
- Screen-off and screen-on broadcasts are observed; screen-off pauses and marks the status, while screen-on never resumes.
- `onResume()` also issues pause and never calls play.
- The WebView requires a user gesture for media playback.

## Important YouTube/API limitations

YouTube's IFrame API is a web embed API, not a native Android player SDK. The embedded YouTube player may expose its own playback controls (required for a normal user-facing YouTube embed), so a viewer may also use the YouTube player's own play control. The application itself never invokes play except from its dedicated PLAY button. This project does not attempt to hide or intercept YouTube branding, controls, ads, or restrictions.

Android lifecycle callbacks and screen-state broadcasts are best-effort OS signals. The app uses multiple pause safeguards, but no third-party app can honestly guarantee an instantaneous stop under every device crash, OS scheduling delay, WebView failure, or vendor-specific behavior. Test on the target device before relying on it. No background service or audio-focus bypass is implemented.

## Project notes

- Package/application ID: `com.playtrigger.app`
- App name: `PlayTrigger`
- App icon: vector drawable `ic_playtrigger.xml`
- YouTube API reference: https://developers.google.com/youtube/iframe_api_reference
