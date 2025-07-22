# Beat Saber Emulator Guide
A (hopefully) simple guide to running Beat Saber on an emulator with hardware acceleration.

## Installing the Android SDK / Emulator
Download the command line tools from the bottom of the [Android Studio Downloads page](https://developer.android.com/studio)

Extract them to the following folder structure then add the bin folder to path:
- Linux/MacOs: `~/Android/Sdk/cmdline-tools/latest/bin`
- Windows: `%USERPROFILE%/Android/Sdk/cmdline-tools/latest/bin`

Open a terminal and run the following command to install the emulator versions

```
sdkmanager emulator 'system-images;android-33;android-desktop;x86_64'
```
- You can experiment with different images, but I found issues with other versions such as ndk translation either not being present or crashing with beat saber, or just other android changes

Now add the `emulator` folder inside your Android SDK to path.

## Creating the emulator
Run the following command to create the emulator, you can change the name if you'd like

```
avdmanager create avd -n android13desktop -k 'system-images;android-33;android-desktop;x86_64'
```
- By default avds will be created in `~/.android/avd`, if you would like to move this elsewhere, say to an external drive, set the `ANDROID_AVD_HOME` environment variable to the containing folder.

When asked if you would like a custom hardware profile, enter no

## Running the emulator
Running the emulator is a single command, be sure to substitute the name if you did so when creating thw avd
```
emulator @android13desktop -selinux permissive -feature QtRawKeyboardInput
```
- SELinux must be disabled otherwise the modloader will be denied permission to execute
- `QtRawInputKeyboard` makes the input smooth like on PC

## Installing Beat Saber
Before installing Beat Saber we first need to install Horizon, otherwise the game will immediately crash.
You will need to install an older version of Horizon as recent ones will also crash.
You can download it [here](https://www.mediafire.com/file/t6w909bnp4vfefd/Horizon.apk-signed-aligned.apk/file), otherwise you can extract the (unsigned) APK yourself from the system partition of old OS versions via https://cocaine.trade/Quest_2_firmware. The version I used is `51062580065800150`

After obtaining the Horizon apk, simply install it via adb

```
adb install <horizon apk>
```

Before installing Beat Saber, you will need to edit the manifest in order for it to access Horizon APIs. The easiest way to do this is using [APKToolGUI](https://github.com/AndnixSH/APKToolGUI).

Decompile the APK and open the `AndroidManifest.xml` file in a text editor and add the following `<queries>` tag at the end of the `<manifest>`

```xml
<manifest ...>
  ...

  <queries>
    <package android:name="com.oculus.horizon"/>
  </queries>
</manifest>
```

Now in APKToolGUI Press the compile button (and sign and align if it doesnt automatically) and install the outputted Beat Saber apk and your obb.

```
adb install <beatsaber apk>
adb shell mkdir /sdcard/Android/obb/com.beatgames.beatsaber
adb push <obbfile> /sdcard/Android/obb/com.beatgames.beatsaber
```

Now you can patch your game with questpatcher and everything should work as expected.

## Installing Mods

Now you can install mods as normal, just be sure to install AndroidHelper, and you will also need to build custom types with [the following hooks](https://github.com/QuestPackageManager/Il2CppQuestTypePatching/blob/57ce4d6a8e0c7a1b847483d0a90f18196e72deb2/src/register.cpp#L658-L660) commented out as they will falsely trigger on emulators. Make sure you edit the version in qpm.json beforehand to the currently used custom types version to ensure it doesnt get overwritten as an update.

## Running the game
You can either use ADB or the `Restart App` button in QuestPatcher to restart the game

## FPFC

You can use my [FPFC Mod](https://github.com/kodenamekrak/Emulator-FPFC/) in order to control the game.

First you will need to enable the keyboard and change the resolution of the emulator.
Open `~/.android/avd/android13desktop/config.ini`
- Set `hw.keyboard` to `yes`
- set `hw.lcd.height` and `hw.lcd.width`

You can find a settings menu to change the camera's resolution and FOV
