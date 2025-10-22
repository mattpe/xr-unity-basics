# Unity assignment 5: Small VR project

This course assignment is about creating a small VR project in Unity. The project should be a simple VR experience that demonstrates your skills in VR development. The topic is free. Project can be a game, a simulation, a training application, or any other type of VR experience you can think of.

This can be done individually or in pairs.

## Objectives

- Works in VR headset (Oculus Quest 2, or similar)
- Use of VR controllers: 1 point
- Some movement system implemented: 1 point
- Interactions with different kind of objects implemented: 1 point 
- Use of your own 3D models or assets and other features implemented: max. 3 points

## Grading

Maximum points from this assignment is 6 points. Check criteria above.

---

## Getting started

These instructions can be use as a starting point for your project development. All the steps are not mandatory, but you should have at least the basic movement and interaction features implemented in your project. You should also use your own ideas and features in your project.

You can use one of the basic FPV apps (assignment 2 or 3) as a starting point for this example. You can also start from scratch if you like.

Remember to save the scene and test your app (_Build and run_) all the time when doing the development or modifications!

### Step 1: Prepare project for Oculus device

1. Create a new Unity 6 project: choose _3D (Built-in Render Pipeline) core_ template
   - Add ground plane (e.g. _3D -> Plane_) and some material to it
   - (or continue a previous FPV project)  
1. _Edit -> Project Settings... -> XR Plugin management -> Install_ -> Choose _Oculus_ plugin for desktop & Android platforms
1. _File -> Build Profiles_ -> Check [previous instructions](unity-4-vr-start.md) for Android platform build settings
1. Go to _Window -> Package Manager_, choose _Packages: Unity Registry_ and install:
    - _XR Interaction Toolkit_, (tested with version 3.0.7), restart if prompted
    - _XR Interaction Toolkit_ -> _Samples_ tab -> import _Starter Assets_
    - If project validation screen appears, click _Fix All_

### Step 2: Basic movement in VR

1. Find default input actions in _Starter assets/Presets_ folder
    - Select _XRI Default Left Controller InputActionManager_
      - In Inspector: Click _Add to Controller Input Action Manager default_
    - Do same for the right controller
1. _Edit -> Project Settings -> Preset Manager_
    - Check/add filters for left and right controller
1. Add to scene: _GameObject -> XR -> Interaction Manager_
1. Add to scene: _XR Origin (XR Rig)_ prefab, found in _Starter Assets -> Prefabs_
    - Check _XR Origin_, it should have _Input Action Manager_ script component
    - _Action Assets_ list should have one element: _XRI Default Input Actions_
    - _XR Origin_ comes with a new _Main Camera_ as a child game object and replaces the original _Player_ game object -> deactive/delete existing _Player_ and _Main Camera_
    - Both hand controllers are also children of _XR Origin_ object
1. Add default interactions. From _Starter Assets/Presets_ folder, find
    - _XRI Default Continuous Move_
    - _XRI Default Continuous Turn_
    - _XRI Default Snap Turn_ 
    - Choose all presets and add them (by clicking _Add ..._ in Inspector).
1. Edit _XROrigin_ object: XROrigin (script)
    - _Tracking Origin Mode_: Floor.
1. Movement setup is found under _XR origin -> Locomotion_ game object
    - Choose movement objects and see inspector for more details
    - For turning, you can use _Continuous Turn Provider (Action-Based)_ or _Snap Turn Provider (Action-Based)_ (snap is the default in Oculus Quest 2)
1. If you want more control over physical appereance of the player, edit _XR Origin_:
    - Disable _Character Controller_ component
    - Add _Rigidbody_ component
      - _Mass_ e.g.: 1000
      - _Collision Detection_: Continous Speculative
    - Add Box/Cylinder Collider
      - _Size_ e.g.: 2, 2, 2
    - Check the initial _position_ of the _XR Origin_ ("player") object, it should be on top of the floor level
    - Edit _Locomotion_ game object: untick _Use character controller_
1. Add a _Sphere_ to scene
    - _Scale_: .5,.5,.5
    - Add _Rigidbody_ component
1. Test movement by using controller joysticks

**Try to implement your own ideas and search for tutorials on the internet (note that many of those might be outdated and need some adjustments with new Unity 6 version).**

---

### Interaction examples

(Might need some adjustments with the Unity 6 version.) 

#### Teleport movement

1. Check that _Teleportation_ game object inside _XR Origin_ has a _Teleportation Provider_ component
1. Add _XR -> Teleportation Area_ on top of the floor level
    - Create a _material_ for it, to easily see where the area is
    - Test by pointing areas with controllers and using the teleportation action
1. Add platforms (using _3D Cube_ game objects) to different levels that you can teleport to
1. Add _XR -> Teleportation Anchor_
    - Place _Teleportation Anchor_ under the platform, scale appropriately
    - Place _Anchor_ child object on platform where you want to teleport
1. For Teleportation Area and Anchors, under Teleportation Configuration, add _Teleportation Provider -> XR Origin_

#### Haptic feedback and teleportation

1. Find _Left Controllers_ object's _XR Ray Interactor_ (you can do the same for the right controller too if needed)
    - Change _Line Type_: Projectile Curve
    - _Velocity_: 15
    - _Enable interaction with UI GameObject_: disabled
    - Add haptic feedback (_Haptic Events_), choose:
      - On Select Entered
      - On Select Exited
      - On Hover Entered
      - On Hover Exited
    - set intensity and duration as you like  
    - _XR Interactor Line Visual_ changes
      - Change color
      - _Line length_ 20
1. Edit _XR Ray Interactor_
    - _Rotation Mode_: Match Direction
1. And Teleport Area
    - _Match Directional Input_: True
1. Now you place the _Teleportation Anchor/Area_ on top of the platform too and access it with the controller

#### Mapping controller buttons to actions

1. Select _XRI Default Input Actions_ (in _Starter Assets_ folder) -> Edit asset
1. Add new Action Map
    - First action -> 'Toggle'
      - Action Type -> Button
      - Edit Binding: Path -> Choose controller -> Optional Controls -> primaryButton
        - Check: <https://docs.unity3d.com/Manual/xr_input.html>
        - Table has mappings for controllers
    - Save Asset
1. Add a _Cylinder_ to scene and material for it
1. Create a `ToggleObject` script component for the cylinder:

    ```csharp
    using UnityEngine;
    using UnityEngine.InputSystem;

    public class ToggleObject : MonoBehaviour
    {
        public InputActionReference toggleReference = null;
        // Start is called before the first frame update

        private void Awake() {
            toggleReference.action.started += Toggle;
        }
        private void OnDestroy() {
            toggleReference.action.started -= Toggle;
        }

        private void Toggle(InputAction.CallbackContext context) {
            Debug.Log("Cylinder toggled!");
            bool isActive = !gameObject.activeSelf;
            gameObject.SetActive(isActive);
        }
    }
    ````

1. Add created 'Toggle' action to _InputActionReference_ property of the script
1. Build and test by pressing the controller's **X** button

#### Grabbing objects

1. Add _XR Grab Interactable_ component to sphere object
1. Remove all _XR Ray Interactor_, _XR Interactor Line Visual_ and _Line Renderer_ components from right controller
1. Add _XR Direct Interactor_ component
    - _Select Action Trigger_: State Change
1. Add _Sphere Collider_
    - _Is Trigger_: yes
1. Add some bowling pins to the scene (small cylinders)
    - _Scale_: 0.1, 0.2, 0.1
    - Add _Rigidbody_, _mass_: 0.2
1. Test: Grab the ball (sphere) with the controller and try to bowl the pins

#### Example: A Bowling game

1. Add empty GameObject, rename to _BallReturner_
    - Add _BoxCollider_
    - Place it behind the pins, and make size a little bit wider than the pins
    - _IsTrigger_: true
1. Add _ReturnBall_ script component to _BallReturner_ object:

    ```csharp
    using UnityEngine;

    public class ReturnBall : MonoBehaviour
    {
        public GameObject trackableObject = null;
        private bool stopBall = false;
        private void OnTriggerEnter(Collider other)
        {
            if (other.gameObject.Equals(trackableObject))
            {
                // Move the ball back to the starting position coords
                trackableObject.transform.position = new Vector3(-13, 2, -1);
                stopBall = true;
            }
        }
        private void FixedUpdate()
        {
            if (stopBall)
            {
                Rigidbody trackableBody = trackableObject.GetComponent<Rigidbody>();
                trackableBody.velocity = Vector3.zero;
                trackableBody.angularVelocity = Vector3.zero;
                stopBall = false;
            }
        }
    }
    ```

    - Set _Trackable Object_ to the sphere object

1. Create a _PinCheck_ script for checking if a pin is hit and falled down:

    - `private bool pinIsHit = false;`
    - In `Update` method:

        ```csharp
        if(!pinIsHit){
            if(this.gameObject.transform.rotation.x != 0 || z != 0) {
                pinIsHit = true;
                // TODO: add some mechanisn for counting the score
            }
        }
        ```

    - Add to all pins

1. Calculate the score and display it on the wall (or on _Canvas_)
    - Add e.g. _3D -> Text_
    - Import TMP Essentials
    - Place text on wall next to pins
    - Add _UpdateScore_ script to the text object
      - Connect with _PinCheck_ script
      - Update score when a pin is hit

---
