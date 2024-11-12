# Unity assignment 4: Getting started with Unity & Oculus (Meta) Quest VR apps

## Step 1: Preparing for VR development in Unity

- [Getting started with VR development in Unity](https://docs.unity3d.com/Manual/VROverview.html)
- [Get Started with Oculus in Unity](https://developer.oculus.com/documentation/unity/unity-gs-overview/)

You need to sign up for Oculus developers account (and have an Facebook account) to activate the developer mode in Oculus Quest 2 device:

- [create](https://developer.oculus.com/manage/organizations/create) a new organization or join to an existing one (ask teacher for a link, if needed)
- [verify](https://developer.oculus.com/manage/verify) your developer account
- [install Meta Quest mobile app](https://www.meta.com/en-gb/help/quest/articles/getting-started/getting-started-with-quest-2/install-meta-quest-mobile-app/) for linking the device to your account and activating the developer mode

## Step 2: Add Oculus support to your Unity app and test building

Grading: 0-2 points.

(Tested with Unity LTS version 2022.3 & Oculus Quest 2 device)

1. **Make a copy** of your previous app (with 360 skybox and some POI) and open it in Unity
    - It's used as a starting point for the first VR app
    - Disable the _Camera Controller_ script component in _Main Camera_ game object
    - Move your player near to some POI object so that you can hit it with the _Raycast_ from the camera just by rotating the camera (no need to move player)
    - Fix the _Canvas_ game object to work in VR, set in Inspector:
      1. Change _Render Mode_ to _Screen Space - Camera_
      1. Choose _Render Camera_: _Main Camera_
      1. Set distance of Canvas closer to the camera (_Plane Distance_ value e.g. to 8 units)
1. _Edit -> Project Settings... -> XR Plugin management -> Install_ -> Choose _Oculus_ plugin for desktop & Android platforms
    - If Android platform is not available, you need to install it via Unity Hub to your Unity editor  
1. _File -> Build Settings_ -> Choose Android platform
    - _Texture Compression_: ASTC
    - _Switch platform_ to Android
1. Click _Player Settings.._:
    - _Minimum API level_: must be at least 29 (Android 10) for Oculus Quest 1 compatibility
    - _Target API level_: 32 (Android 12.0, 12L)
    - _Scipting Backend_: IL2CPP
    - _Target Architectures_: only ARM64
1. Add _Tracked Pose Driver_ component to _Main Camera_ to enable head tracking features
1. Prepare your Oculus headset for development:
    - On Windows, you need to download and install [Oculus ADB Drivers](https://developer.oculus.com/downloads/package/oculus-adb-drivers/)
    - Activate developer mode in Oculus Quest device using the Meta Quest mobile app
      - After enabling developer mode, you should be able to see Developer menu in the Quest device's settings
    - Connect Oculus Quest device via USB cable and grant access for USB debugging / File access
1. _File -> Build Settings..._
    - Add your scenes to the _Scene List_
    - Set _Run Device_ to _Oculus Quest x (serial)_
    - Enable _Development Build_ & _Script Debugging_ options
    - Click _Build And Run_ and set name for the build file, _Save_. (Update Android SDK if prompted)
    - First build might take a while, but after that, it should be faster
1. Wear the goggles & try it out!
1. Debugging via USB connection:
   - On Windows, it's possible to use Unity's ["play mode"](https://developer.oculus.com/blog/debugging-unity-apps-for-oculus-quest-in-headset/) for debugging
   - On MacOS (and Windows) you can access Unity's logging on Android device via terminal command `adb logcat -s Unity`
1. Submit to the related Oma assignment **before deadline** as instructed there.
