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

### 2.4. Create Native Windows .exe

     %ADT% -package -target native target/HelloWorld.exe target/HelloWorld.air

### 2.5. IntelliJ Configuration
#### 2.5.1. Prerequisites

You need to have the flex SDK on your development machine for IntelliJ development for AIR.
IntelliJ knows about AIR, but not about FlexMojos/AIR.
Note that Maven does not need the locally installed SDK.

#### 2.5.2. General Setup

* File > New Project 
* New Project Dialog
   * Panel 1
       * Target platform: Desktop
       * Output type: Application
       * Flex/Air SDK: Select the SDK, not the flexmojos.
       * UNCHECK 'create sample' apps or the sources will be overwritten.
   * Panel 2
       * Select the source folder where the pom.xml resides.
       * Finish
* Add the pom manually to the 'Maven Projects' sidebar (with the '+').

#### 2.5.3. Resource Folder Trick

The FlexMojos can find the resources in src/main/resources, but the built-in compiler in IntelliJ does not add the Maven resource folder to the path.
This issue is situated in the IntelliJ-FlexMojos integration for AIR applications. We can solve this issue with this hack.
You have to do this if you get errors that the Flex compiler cannot find/compile/encode the embedded resources.

* Adjust the .idea/flexmojos/[artifactId]-[groupId].xml, add the 'path-element' src/main/resources as a source folder.
