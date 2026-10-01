# Hourly Spin Kings

A Kotlin + Jetpack Compose Android Studio project for a user-controlled AccessibilityService workflow against an installed app labelled **AceWin**.

## What this project does

- Lets you select an installed launcher app whose label is exactly `AceWin`.
- Uses Android `AlarmManager` for the scheduler and exact alarms when permitted.
- Uses an Android `AccessibilityService` to locate intended UI elements by visible text/content description instead of relying on screen coordinates.
- Includes a **Test Now** mode. Test mode stops before logout unless **Test logout step** is explicitly enabled.
- Logs each state/action locally without storing passwords, OTPs, payment data, or other credentials.
- Includes a boot receiver to restore scheduling after reboot when Android permits it.
- Uses a short wake lock only when the display is off; it does not keep a permanent wake lock.

## Important Android limitations

Android controls background activity launches, exact alarms, accessibility, battery optimization, and lock-screen behavior. The app therefore does **not** guarantee unattended 24/7 operation. On some phones/manufacturers, background execution may be delayed or blocked. If another app appears to be actively in use when a scheduled cycle arrives, this project postpones the cycle rather than forcibly taking over the screen.

The app never attempts to bypass a lock screen, CAPTCHA, OTP, password, biometric prompt, or payment/security control.

## Build in Android Studio

1. Install the current stable Android Studio.
2. Open this folder as an existing project.
3. Let Android Studio download the Gradle/Android dependencies.
4. Use JDK 17 for Gradle if Android Studio asks.
5. Connect your Android phone and enable Developer Options + USB debugging, or use Android Studio's wireless debugging.
6. Select the `app` run configuration and build/install.
7. To make an APK: **Build → Build APK(s)**. The debug APK will normally be under `app/build/outputs/apk/debug/`.

This environment does not include a local Android SDK/Gradle installation, so the APK cannot be compiled here. The project source is provided so Android Studio can resolve the official Android/Compose dependencies and build it.

## Phone setup

1. Install the generated APK.
2. Open **Hourly Spin Kings**.
3. Install **AceWin** separately.
4. Tap **Select AceWin** and choose the detected app package.
5. Tap **Enable Accessibility** and enable `Hourly Spin Kings` under Android Accessibility settings.
6. Allow notifications.
7. On Android 12+, allow **Exact alarms** if you want exact scheduled times.
8. If your phone aggressively restricts background apps, review battery optimization settings for Hourly Spin Kings. Some manufacturers also have their own Auto-start/background-activity controls.
9. Keep the phone configured so Android can display AceWin when a cycle is allowed to run.

## First test

1. Leave **Test logout step** OFF.
2. Tap **Test Now**.
3. The expected flow is:
   `AceWin → safe popup dismissal → Home → Hourly Spin → Tap to Spin once → wait → Back → Profile`
4. The app stops the test before logout by default.
5. Read **Current automation state**, **Last run**, **Error**, and **Event log** if anything fails.

## Real schedule

- Test interval field defaults to 1 minute for configuration/testing.
- Real interval defaults to 65 minutes.
- Initial delay defaults to 1 minute.
- When an automatic cycle completes, the next cycle is scheduled relative to the recorded start time of the current cycle, with a minimum 5-second safety margin if the target time has already passed.

## Safety boundaries

The AccessibilityService only targets exact visible strings/content descriptions such as `Close`, `Hourly Spin`, `Tap to Spin`, `Home`, `Profile`, and `Log out`. It does not use hard-coded coordinates and does not click payment, recharge, withdrawal, betting, or deposit controls.

Google Password Manager/security warnings are handled only by clicking a visible `Close` when the warning is recognized; password content is never accessed.

Logout confirmation is intentionally not auto-confirmed. If the app presents a credential/OTP/CAPTCHA requirement, the automation stops instead of attempting to bypass it.

## Cloud build (phone-only option)

This project now includes a GitHub Actions workflow at:
`.github/workflows/build-apk.yml`

You do **not** need Android Studio to build the debug APK.

### Easiest method

1. Create/sign in to a GitHub account.
2. Create a **new repository** (a private repository is recommended).
3. Upload the **contents of this project folder** to the repository. The `.github/workflows/build-apk.yml` file must be included.
4. Open the repository on GitHub.
5. Tap **Actions**.
6. Select **Build Hourly Spin Kings APK**.
7. Tap **Run workflow**.
8. Wait for the workflow to finish with a green check.
9. Open the completed workflow run.
10. Under **Artifacts**, download `HourlySpinKings-debug-apk`.
11. Extract the downloaded artifact ZIP. Inside is `app-debug.apk`.
12. Open the APK on your Android phone and allow installation from that source if Android asks.

### Important

The GitHub workflow builds a **debug APK**. It does not publish the app to Google Play.

The APK contains the accessibility-service functionality described by the project, but Android/AceWin UI differences can still require testing and adjustment.

Do not upload passwords, OTPs, signing keys, or other secrets to the repository.
