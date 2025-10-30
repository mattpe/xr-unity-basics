# Unity assignment 2: 3D First Person View

1. Create a 3D scene containing (teacher's lecture example, 2 points):
    - ground plane
    - player object controlled by user input (horizontal movement, turning)
    - some other objects
1. Add your own twist to the scene (some extra object & features, use your imagination, 1 points), some ideas:
    - solid obstacles that prevent moving objects falling from the plane
    - add materials to all game objects
    - goal for the player: what the player should do to finish the "game"
    - interactions with objects: what happens when player touches some other game object
    - player jump
1. Submit your Unity project to the related Oma assignment **before deadline**: Create a **zip**-file of your project folder containing only `Assets` and `ProjectSettings` subfolders (_NOTE:_ arj, rar, etc. other formats are not a zip file)
   - Present your application live to teacher to get the points.

Grading 2 + 1 points.

## Instructions for getting started (teacher's example)

1. Create a new 3D project
    - rename the default `SampleScene` to something more describtive
    - remember to save the changes you made to the scene on regular basis (_ctrl/cmd-s_)
1. Add ground (_3D object -> plane_) to the scene
    - use provided [`ground.png`](./assets/ground.png) or similar as material
    - check the position (should be placed at origin in scene) and set the size larger (_Inspector -> Transform component_)
1. Add Player (an empty game object) to the scene
    - add _Rigidbody_ component, set _Interpolate: interpolate_ and _Collision Detection: continuous_
    - add visible 3D object as a child of Player (this acts as the player model, use e.g. _capsule_)
    - add Orientation (empty GameObject) as a child of Player for keeping track of orientation
    - add CameraPosition (empty GameObject) as a child of Player for linking with the Main Camera's position
1. Panning and tilting the camera view: Add `CameraController` script component to _Main Camera_
    - attach Player's _CameraPosition_ object to script's _CameraPosition_ property in _Inspector_
    - attach Player's _Orientation_ object to script's _Orientation_ property
    - set the _Sensitivy_ values (something between 100-1000 should be ok) in _Inspector_
1. Player movement: Add `PlayerController` script component to your _Player_ game object
    - attach Player's _Orientation_ object to script's _Orientation_ property
    - set the speed of movement to something like 50
    - add some _drag_ to Rigidbody

### Example scripts

Check Unity scripting reference for more details about the methods and properties used in these scripts: [Unity Scripting Reference](https://docs.unity3d.com/ScriptReference/).

Also check documentation about Unity's input system and event handling:

- [Input Manager](https://docs.unity3d.com/Manual/class-InputManager.html)
- [Event functions](https://docs.unity3d.com/Documentation/Manual/event-functions.html)
- [Event function execution order](https://docs.unity3d.com/Manual/execution-order.html)

#### CameraController.cs

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class CameraController : MonoBehaviour
{
    public Transform cameraPosition;
    public float sensitivyX;
    public float sensitivyY;
    public Transform orientation;
    private float xRotation;
    private float yRotation;

    // Start is called before the first frame update
    void Start()
    {
        // lock & hide hardware pointer
        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
    }

    // Update is called once per frame
    void Update()
    {
        // move camera according the player
        transform.position = cameraPosition.position;

        // read mouse input
        float mouseX = Input.GetAxisRaw("Mouse X") * Time.deltaTime * sensitivyX;
        float mouseY = Input.GetAxisRaw("Mouse Y") * Time.deltaTime * sensitivyY;
        // update rotation values
        yRotation = yRotation + mouseX;
        xRotation = xRotation + mouseY;
        // limit tilting
        xRotation = Mathf.Clamp(xRotation, -90f, 90f);
        // rotate camera & orientation of object
        transform.rotation = Quaternion.Euler(xRotation, yRotation, 0);
        orientation.rotation = Quaternion.Euler(0, yRotation, 0);
    }
}
```

#### PlayerController.cs

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float speed;
    public Transform orientation;
    private float xInput;
    private float yInput;
    private Vector3 movingDirection;
    private Rigidbody rb;

    // Start is called before the first frame update
    void Start()
    {
        rb = GetComponent<Rigidbody>();
        rb.freezeRotation = true;
    }

    // Update is called once per frame
    void Update()
    {
        // Read input (keyboard)
        xInput = Input.GetAxisRaw("Horizontal");
        yInput = Input.GetAxisRaw("Vertical");
    }
    void FixedUpdate()
    {
        // Calculate correct direction
        movingDirection = orientation.forward * yInput + orientation.right * xInput;
        // Move to correct direction
        rb.AddForce(movingDirection.normalized * speed, ForceMode.Force);
        // Do not exceed maximum speed
        Vector3 groundVelocity = new Vector3(rb.velocity.x, 0f, rb.velocity.z);
        if (groundVelocity.magnitude > speed)
        {
            Vector3 maxVelocity = groundVelocity.normalized * speed;
            rb.velocity = new Vector3(maxVelocity.x, rb.velocity.y, maxVelocity.z);
        }
    }
}
```
