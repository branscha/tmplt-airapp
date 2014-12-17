# Sample Apache Adobe AIR/Apache Flex project
## 1. Description

A template AIR project that can be built using Maven, it creates a Desktop AIR applcation.
The Flexmojos currently cannot produce an APK for Android (or another mobile platform) automatically.
The build makes use of a private Maven repository containing the Mavenized Flex SDK.

## 2. Additional Information
### 2.1. Generate a keyfile to sign the air

The adt tool can be found in the Flex SDK distribution. The location might vary for differenet SDK versions. Make sure that the .bat exists in that location.

    SET ADT=[FLEX-SDK]\in\airsdk\bin\adt.bat

Copy this file to the src/resources directory. I generated a self signed code signing certificate which is valid for 10 yrs. Only use this for test purposes,
you need to buy an official certificate if you want to distribute your application.

    %ADT% -certificate -cn "Bruno Ranschaert" -validityPeriod 10 2048-RSA sign.p12 changeit

### 2.2.Create Android APK from AIR

Run this command in the project home after the .air was created. The AIR application can be converted to an Android APK.

    %ADT% -package -target apk | apk-debug
          -storetype pkcs12 -keystore src/main/resources/sign.p12 -storepass changeit 
          target/HelloWorld.apk target/HelloWorld.air

### 2.3. Install the APK on an Android device

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

### 2.4. IntelliJ Configuration

* Create a Flex/Air project in the source directory, it will see the POM.
* Dont let IntelliJ remove the module structure, leave it intact.
* Adjust the .idea/flexmojos/[DERIVED-NAME].templates.xml, add the 'path-element' src/main/resources as a source folder.
This is necessary to run/debug the application from within the IDE. You have to tweak this manually, currently I am not aware of a workaround.`