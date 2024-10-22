---
layout: post
title: Tips & Tricks
categories:
    - Android
description: Short and sweet tips, tricks and best practices for Android development.
---

Some short and sweet tips, tricks and best practices for Android development:

<details markdown="1">
<summary>Using the Command Line</summary>

Some examples:
```sh
# Build release APK
./gradlew assembleRelease
# Build release AAB
./gradlew bundleRelease
# Run tests
./gradlew testReleaseUnitTest
# Install APK on device
abd install my_app.apk
# List connected devices
adb devices
# Connect over wifi (enabled in developer menu)
adb pair <ip>:<port>
# Open shell on device
adb shell
# Enter text
adb shell input text foo
# Press "enter" key
adb shell input keyevent 66
# Simulate process death (app must be in background)
adb shell am kill my.application.id
# Take screenshot
adb exec-out screencap -p > ./screen.png
# Test deep link
adb shell am start -a android.intent.action.VIEW -d https://my.url.io/my_file
# Show log cat output
adb logcat -v color,brief --pid=$(adb shell pidof my.application.id)
# Open a Kotlin REPL
kotlin
# Run a Kotlin script
kotlinc -script my_script.kts
```

Development on command line requires a JDK (Java Development Kit) and Android SDK in your `$PATH`. 

Android Studio contains an embedded JDK (and a Kotlin compiler), no need to install it separately. Using the embedded JDK also has some other advantages (e.g. command line and Android Studio use the same Gradle daemon). 

On MacOS just add this to your `~/.zshrc`:

```sh
# Android
export ANDROID_HOME="/Users/<YOUR USER NAME HERE>/Library/Android/sdk"
export PATH="${PATH}:${ANDROID_HOME}/tools"
export PATH="${PATH}:${ANDROID_HOME}/tools/bin"
export PATH="${PATH}:${ANDROID_HOME}/platform-tools"

# Kotlin
# Note: You may have to give the binaries execution permission: chmod +x kotlinc
export KOTLIN_HOME="/Applications/Android Studio.app/Contents/plugins/Kotlin/kotlinc"
export PATH="${PATH}:${KOTLIN_HOME}/bin"

# Java (no need to export to $PATH)
export JAVA_HOME="/Applications/Android Studio.app/Contents/jre/Contents/Home"
```
</details>


<details markdown="1">
<summary>Screen Mirroring</summary>

I highly recommend [scrcpy](https://github.com/Genymobile/scrcpy), if you prefer working with a real device or can't use an emulator (e.g. for Bluetooth apps).

Features:
- Mirror your device’s screen on your desktop: `scrcpy`
- Take recordings: `scrcpy --record recording.mp4`
- You can use your keyboard to type on your device or click anywhere with your mouse. 
- Shared clipboard, so you can copy and past from and to you device.
- Drag and drop to transfer files or install APKs.
</details>


<details markdown="1">
<summary>Signing Keys</summary>

You can use `keytool` for creating a keystore and adding signing keys to it. It is part of the JDK. This can also also be done in Android Studio (Build &rarr; Generate Signed APK).

```sh
# Create keystore or add key to existing keystore
keytool -v -genkey -keystore ./keystore.jks -keyalg RSA -keysize 2048 -validity $((365 * 50)) -alias MyKey -deststoretype jks
# List keys in keystore
keytool -v -list -keystore ./keystore.jks
# Move key from one keystore to another (creates other if it does not exist)
keytool -v -importkeystore -srckeystore keystore.jks -destkeystore new_keystore.jks -srcalias MyKey -destalias MyKey -deststoretype jks
```
</details>


<details markdown="1">
<summary>Drawable Resources</summary>

Use the `drawable-nodpi` resource directory if you only have one size of an image. The `drawable` resource directory is the same as `drawable-mdpi` and will scale images up on devices with a higher pixel density. A 1600 x 1000 image [will be scaled up](https://medium.com/@oronno/android-drawable-outofmemoryerror-ebe2995760b6) to 6400 x 4000 on an xxxhdpi device. This can quickly lead to `OutOfMemoryError`.

For vector assets this is not a problem. However you can use the `drawable-anydpi` resource directory, which will only be used if no resource is defined for any other density.
</details>


<details markdown="1">
<summary>Splash Screen</summary>

You can add a simple splash-screen to you app, by adding a splash theme, which will be shown until the launched activity is fully loaded:

```xml
<!-- styles.xml -->
<style name="MyAppTheme.Splash">
    <item name="android:windowBackground">@drawable/splash</item>
</style>
```

```xml
<!-- splash.xml -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" android:opacity="opaque">
    <item android:drawable="?android:colorBackground" />
    <item android:drawable="@drawable/ic_launcher_foreground" android:gravity="center" />
</layer-list>
```

```xml
<!-- AndroidManifest.xml -->
<activity
    android:name=".MainActivity"
    android:theme="@style/MyAppTheme.Splash" />
```

```kotlin
// MainActivity.kt
override fun onCreate(savedInstanceState: Bundle?) {
    setTheme(R.style.MyAppTheme) // app is started, so we can now remove the splash screen theme
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
}
```
</details>


<details markdown="1">
<summary>Formatting (Dates, Currency, etc.)</summary>

You should use the user's locale specific date (`12/31/22` vs. `31.12.22`), time, currency and number (`10,000.00` vs `10.000,00`, [Eastern Arabic numerals](https://en.wikipedia.org/wiki/Eastern_Arabic_numerals)) formats. 

- Dates: 
    - Short: `DateFormat.getDateFormat(context).format(date)`
    - Medium: `DateFormat.getMediumDateFormat(context).format(date)`
    - Long: `DateFormat.getLongDateFormat(context).format(date)`
- Time: `DateFormat.getTimeFormat(context).format(date)`
- Percentage: `NumberFormat.getPercentInstance().format(percentage)`
- Numbers: `NumberFormat.getNumberInstance().format(number)`
- Currency:
    - German (Euro): `NumberFormat.getCurrencyInstance(Locale.GERMANY).format(amount)`
    - US (Dollar): `NumberFormat.getCurrencyInstance(Locale.US).format(amount)`
    - User locale (Euro): `NumberFormat.getCurrencyInstance().apply { setCurrency(Currency.getInstance("EUR")) }.format(amount)`
</details>


<details markdown="1">
<summary>System Windows</summary>

You can draw the app's background behind system windows (status bar, camera notches, keyboard) by setting `android:windowTranslucentStatus="true"` on your app's theme.

To prevent elements like text or buttons from also drawing behind the status bar, they need to receive some padding. This padding is called "window insets". It usually has different values for all 4 sides is provided by the system at runtime, e.g. when the keyboard is expanded or when device has a notch (at the top in portrait or at the side in landscape mode). 

To automatically apply this padding you can set `android:fitsSystemWindows="true"` on a view or use `Modifier.windowInsetsPadding(WindowInsets.systemBars)` in Jetpack Compose. The insets will be consumed by the view and no other will receive it, so it is best applied to a view group, like your root layout. These insets will override any other paddings you have defined on the view.
</details>


<details markdown="1">
<summary>Right-to-Left Locales</summary>

Used for Arabic and Hebrew.
- Make sure you have `android:supportsRtl="true"` in your Manifest.
- Always use "start" and "end" instead of "left" and "right", e.g `layout_marginStart`, `layout_constraintStart_toEndOf`, `layout_gravity="end"`. If you do this consistently, your layouts should look good on RTL locales without much extra work.
- You can automatically mirror vector assets (e.g. left/right arrows) for RTL locales by using `android:autoMirrored="true"`. No logic or separate drawable in `drawable-ldrtl` needed.
- Use [ViewPager2](https://developer.android.com/jetpack/androidx/releases/viewpager2), which automatically switches scroll direction for RTL locales.
- You can quickly check your XML layouts in Android Studio by selecting "Preview Right to Left" under the locale selection in the layout preview.
</details>


<details markdown="1">
<summary>XML Layouts</summary>

These tips have become obsolete with Jetpack Compose, but may be helpful for existing apps:
- For some easy animations add `android:animateLayoutChanges="true"` on parent layouts, so changes to their children (e.g. visibility) are animated.
- You can add fading edges on scrollable views, so content disappears smoothly at the top and bottom with `android:requiresFadingEdge="vertical"` and `android:fadingEdgeLength="8dp"`.
- You can add dividers between `LinearLayout` children with `android:showDividers="middle"` and `android:divider="?dividerHorizontal"`.
- You can set a TextView to [automatically](https://developer.android.com/develop/ui/views/text-and-emoji/autosizing-textview) shrink its font size, so the text will shrink to always fit with `android:autoSizeTextType="uniform"`. Do not use `wrap_content` for width or height or it will not work correctly.
</details>
