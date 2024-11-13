# Unity assignment 4: Getting started with Unity & Oculus (Meta) Quest VR apps

## Step 1: Preparing for VR development in Unity

- [Getting started with VR development in Unity](https://docs.unity3d.com/Manual/VROverview.html)
- [Get Started with Oculus in Unity](https://developer.oculus.com/documentation/unity/unity-gs-overview/)

You need to sign up for Oculus developers account (and have an Facebook account) to activate the developer mode in Oculus Quest 2 device:

- [create](https://developer.oculus.com/manage/organizations/create) a new organization or join to an existing one (ask teacher for a link, if needed)
- [verify](https://developer.oculus.com/manage/verify) your developer account
- install Meta Horizon (Quest) mobile app [android](https://play.google.com/store/apps/details?id=com.oculus.twilight) or [ios](https://apps.apple.com/us/app/meta-horizon/id1366478176) for linking the device to your account and activating the developer mode

## Step 2: Add Oculus support to your Unity app and test building

Grading: 0-2 points.

**NOTE:** These are Unity 6 (6000.0.26) version of instructions, build might fail with some project setups.

1. You can make a copy of your previous app (with 360 skybox and some POI) and try to convert it to VR app:
    - Can bed used as a starting point for the first VR app
    - Disable the _Camera Controller_ script component in _Main Camera_ game object
    - Move your player near to some POI object so that you can hit it with the _Raycast_ from the camera just by rotating the camera (no need to move player)
    - Fix the _Canvas_ game object to work in VR, set in Inspector:
      1. Change _Render Mode_ to _Screen Space - Camera_
      1. Choose _Render Camera_: _Main Camera_
      1. Set distance of Canvas closer to the camera (_Plane Distance_ value e.g. to 8 units)
1. Or create a new project/scene with some 3D objects and a skybox, check settings for _Canvas_ above if needed
1. _Edit -> Project Settings... -> XR Plugin management -> Install_ -> Choose _Oculus_ plugin for desktop & Android platforms
    - If Android platform is not available, you need to install it via Unity Hub to your Unity editor  
1. _File -> Build Profiles_ -> Choose Android platform
    - _Texture Compression_: ASTC
    - _Switch platform_ to Android
1. (Optional, default setting are ok in Unity 6, SKIP this step for now) _Add Build Profile_ and click _Customize Player Settings.._ on the new profile:
    - _Minimum API level_: must be at least 29 (Android 10)
    - _Target API level_: 32 (Android 12.0, 12L)
    - _Scipting Backend_: IL2CPP
    - _Target Architectures_: only ARM64
    - _Active Input Handling_: Input System X
    - Click _Switch profile_
1. Add _Tracked Pose Driver_ component to _Main Camera_ to enable head tracking features
1. Prepare your Oculus headset for development:
    - On Windows, you need to download and install [Oculus ADB Drivers](https://developer.oculus.com/downloads/package/oculus-adb-drivers/)
    - Activate developer mode in Oculus Quest device using the Meta Quest mobile app
      - After enabling developer mode, you should be able to see Developer menu in the Quest device's settings
    - Connect Oculus Quest device via USB cable and grant access for USB debugging / File access
1. _File -> Build Profiles_
    - Add your scenes to the _Scene List_
    - Set _Run Device_ to _Oculus Quest x (serial)_
    - Enable _Development Build_ & _Script Debugging_ options
    - Click _Build And Run_ and set name for the build file, _Save_. (Update Android SDK if prompted)
    - First build might take a while, but after that, it should be faster
1. Wear the goggles & try it out!
1. Debugging via USB connection:
   - On Windows, it's possible to use Unity's ["play mode"](https://developer.oculus.com/blog/debugging-unity-apps-for-oculus-quest-in-headset/) for debugging
   - On MacOS (and Windows) you can access Unity's logging on Android device via terminal command `adb logcat -s Unity`
1. Submit an image (photo or screenshot from the device) to the related Oma assignment **before deadline**.
