# Sample Apache AIR project
## Description

A template AIR project that can be built using Maven, it creates a Desktop AIR applcation.
The Flexmojos currently cannot produce an APK for Android (or another mobile platform) automatically.
The build makes use of a private maven repository containing the Mavenized Flex SDK.

## Generate a keyfile to sign the air

The adt tool can be found in the Flex SDK distribution.

    SET ADT=[FLEX-SDK]\in\airsdk\bin\adt.bat

Copy this file to the src/resources directory.

    %ADT% -certificate -cn "Bruno Ranschaert" -validityPeriod 10 2048-RSA sign.p12 changeit

## Create Android APK from AIR

Run this command in the project home after the .air was created.

    %ADT% -package -target apk | apk-debug
          -storetype pkcs12 -keystore src/main/resources/sign.p12 -storepass changeit 
          target/HelloWorld.apk target/HelloWorld.air

## Install the APK on an Android device

First start an emulator or connect your Android device (with debug mode enabled).

Install the AIR runtime in the emulator with this command. This is only necessary on the emulator because there is no Google Play in the emulator, so you cannot download it from the store.
It is not necessary on a real device if you installed this previously.

    %ADT% -installRuntime -platform android

The 'adb' command is in the Android SDK

    SET ADB=[ANDROID-SDK]\platform-tools\adb.exe

Then use it to deploy the apk to the device from within the target/ directory.

    %ADB% install -r HelloWorld.apk

You can see the Android log using

     %ADB% logcat