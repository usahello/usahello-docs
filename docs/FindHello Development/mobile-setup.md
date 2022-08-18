# Mobile Setup

At a high level we will be using the Ionic CLIs `cordova` command to build test and release versions of the iOS and Android app. For example:

```sh
> ionic cordova build android --prod --release
```

You can use this command to create builds for development testing or production, to run the app locally on a connected phone or to run on either the Android or iOS emulator.

**The following instructions are for MacOS M1.**

## Node

Make sure you have a recent version of node installed. I'm using 16. You can use
`nvm` to manage Node versions:

https://github.com/nvm-sh/nvm

## Cordova

Make sure Cordova is installed:

```sh
npm install -g cordova cordova-res native-run
```

## Android

Here is the official guide:

https://ionicframework.com/docs/developing/android

We need a few things for Android, on M1 via Cordova:

- Java8: this is an issue as Oracle haven't made any M1-compatible releases for 8
- Gradle:
- Maven: 

### Build Tools

My understanding is that you have to install Gradle, Ant and Maven separately to Android Studio.

```sh
brew install gradle ant maven
```

Now add to the `~/.zshrc`:

```
export GRADLE_HOME=/opt/homebrew/opt/gradle
export ANT_HOME=/opt/homebrew/opt/ant
export MAVEN_HOME=/opt/homebrew/opt/maven
```

### Java8

You can get a version via Azul (a separate company to Oracle). See this guide:

https://dev.to/shane/configure-m1-mac-to-use-jdk8-with-maven-4b4g

Download Java8 here:

https://www.azul.com/downloads/?version=java-8-lts&os=macos&architecture=arm-64-bit&package=jdk#download-openjdk

You need then add the Java Home to your `~/.bashrc`:

```sh
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre
```

### Android Studio

Install the `arm64` version of Android Studio here:

https://developer.android.com/studio#downloads

Add the following to your `~/.bashrc`, updating your home directory:

```sh
# Android SDK
export ANDROID_SDK_ROOT=/Users/timmyomahony/Library/Android/sdk
export PATH=$PATH:$ANDROID_SDK_ROOT/emulator
export PATH=$PATH:$ANDROID_SDK_ROOT/tools/bin
export PATH=$PATH:$ANDROID_SDK_ROOT/platform-tools
```

Run:

```sh
source ~/.bashrc
```

### Android SDK Platform

We need the Android SDK Platform 28 installed. Go to `Preferences > Appearance and Behaviour > System Settings > Android SDK` and select Android 9.0 (API level 28) and download

### Android Emulator

First you need to download an Android image. On the same dialog as above, click 
"Show Package Details" and select `Google APIs ARM 64 v8a System Image`.

Hit `Apply` to download the system image.

#### Device Manager

On the "Welcome to Android Studio" screen, hit the "More Actions" and "Virtual Device Manager".

Create a new device:

- Choose Pixel 5
- Choose "Pie" (this is the image downloaded previously)

### Cordova Platform

You should be able to add Android as a platform now:

```sh
ionic cordova platform add android
```

### Check Requirements

You can check the requirements for Ionic now:

```sh
ionic cordova requirements
```

It should look like:

```sh
Requirements check results for android:
Java JDK: installed 1.8.0
Android SDK: installed true
Android target: installed android-30,android-28
Gradle: installed /opt/homebrew/Cellar/gradle/7.4.2/bin/gradle
```

### Run via Emulator

You should be able to test on the emulator now.

First make sure the emulator is running (i.e. manually start it in Android Studio)
then run:

```sh
ionic cordova emulate android
```

#### Troubleshooting

- Make sure you have the right environment variables set
- Make sure nothing is listening on port 5555 or it will appear as a device

## Apple

On an M1 machine, I haven't been able to the app to run on a simulator, I've only
been able to run it on a connected iPhone. See below for more details.

In general, the approach here is to build within XCode (as opposed to building
via the Cordova CLI).

### CocoaPods

Install CocoaPods:

```sh
sudo gem install cocoapods
```

You might need `rvm` installed to get a newer version of Ruby than the MacOS
system version.

### Cordova Platform

You should be able to add iOS as a platform now:

```sh
ionic cordova platform add ios
```

### Configure Xcode

Open Xcode, `Preferences > Locations` and make sure "Command Line Tools" is set
to the installed version of Xcode

### Run via iPhone

Connect iPhone via USB and open `platforms/ios/FindHello.xcworkspace` (not `FindHello.xcodeproj`) in Xcode.

Click the project in the sidebar and hit "Signing & Capabilities". Sign in and
make sure the right team is selected.

Select the iPhone and build to it.

### Attempting to Run via Simulator

If you try build for a simulator on an M1, you get a build error in XCode,
something like:

> in /Users/timmyomahony/Repositories/find-hello-app/platforms/ios/Pods/FirebaseAnalytics/Frameworks/FIRAnalyticsConnector.framework/FIRAnalyticsConnector(FIRAnalyticsConnector_e321ed8e3db06efc9803f6c008e67a34.o), building for iOS Simulator, but linking in object file built for iOS, file '/Users/timmyomahony/Repositories/find-hello-app/platforms/ios/Pods/FirebaseAnalytics/Frameworks/FIRAnalyticsConnector.framework/FIRAnalyticsConnector' for architecture arm64

https://stackoverflow.com/questions/63607158/xcode-building-for-ios-simulator-but-linking-in-an-object-file-built-for-ios-f

The fix in the above link may work, but it causes a huge number of additional errors that I can't fix.

https://stackoverflow.com/questions/65287834/tons-of-errors-due-to-set-arm64-in-excluded-architectures-in-ios-14-2-simulators
