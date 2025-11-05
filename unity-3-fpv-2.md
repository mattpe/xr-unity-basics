# Unity assignment 3: First Person View (FPV) with models and a skybox

## Idea

- Use your 360 HDR image as a skybox in your scene, 1 point
- Explore and interact with your own 3D models, 1 point
  - Add hotspots/POIs to the scene
    - something happens when you point to a model by looking at it
    - show info box etc.
    - move to other scene or 360 image
- Add your own twist to the viewer app (something extra, use your imagination), max. 1 extra point
- Submit your Unity project to the related Oma assignment **before deadline**: Create a **zip**-file of your project folder containing only `Assets` and `ProjectSettings` subfolders (_NOTE:_ arj, rar, etc. other formats are not a zip file)
  - Present your application live to teacher to get the points.

Grading 2 + 1 points.

## Instructions

1. Continue with your previous Unity project or create a new project (you can create a new one by copying the previous one: copy the project folder on you hard drive, rename it and open in Unity Hub).
1. Prepare 360 image(s): equirectangular projection, 2:1 ratio
   - if possible, using your own panorama images is preferred
   - there is an example image for temporary use available in _Oma documents_, just in case
1. Import HDR image(s) to unity:
   - change image texture shape to _Cube_ in import settings
   - create a new material and set [shader](https://www.raywenderlich.com/5671826-introduction-to-shaders-in-unity#toc-anchor-001) to: _[Skybox/Cubemap](https://docs.unity3d.com/Manual/shader-skybox-cubemap.html)_
   - Set _Cubemap (HDR)_: imported image
1. Add photo material to scene as a background (skybox): _Window -> Rendering -> Lighting -> Environment tab -> Skybox material_ (or just drag the image file to scene), review the _Lighting_ settings
1. If you want to add reflections to your scene, you can use [_Reflection Probes_](https://docs.unity3d.com/Manual/ReflectionProbes.html)
1. Adapt the `CameraController.cs` script from previous assignment (this is used to simulate head tracking available in real VR)
1. Add some 3D objects (models) acting as hotspots/POIs when you look directly at them
   1. Importing 3D models, study material in Unity docs:
      - [Supported model file formats](https://docs.unity3d.com/Manual/3D-formats.html)
      - [Importing model files](https://docs.unity3d.com/Manual/ImportingModelFiles.html)
      - [Meshes in Unity](https://docs.unity3d.com/Manual/mesh.html)
   1. Import some of your own 3D models and place them into scene (products of another course assignment)
      - when exporting from Blender, choose _Include modifiers_ in file settings
      - use of `.fbx` format is preferred
      - if having performance issues when importing/using models in Unity check that your model is not too complex
      - if your model includes a camera, disable it to use the Unity's Main Camera game object
   1. Import your photogrammetry model if available (result/product of another assignment)
   1. Import your 3D scanned model if available (result/product of another assignment)
1. Implement two types of hotspots:
   - _Info_: display some info about the object
   - _Teleport_: move to another room/scene (where you have another 360 image as a skybox)
   - set the type of hotspots using e.g. [tags](https://docs.unity3d.com/Manual/Tags.html)
   - use _Text_ gameobject on _Canvas_ to show info text (_GameObject -> UI -> Text_) when pointing/looking at the hotspot
1. Create a `CameraRayControl.cs` script and attach it to _Main Camera_ and add the functionality for interacting with hotspots
   - camera should [cast a ray](https://docs.unity3d.com/ScriptReference/Physics.Raycast.html) forward and react when the ray hits to other game object's collider
   - when pointig to info object/hotspot: show/hide the infobox
   - when pointing to change scene object/hotspot for couple of seconds: move to another room (_scene_)
1. You should have at least two scenes and 360 images and user should be able to switch between the scenes using the hotspots
1. Develop further: design and implement your own features

### Code examples

_CameraRayController.cs_

```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;
using UnityEngine.UI;

public class CameraRayController : MonoBehaviour
{
    Text infoText;
    Slider delaySlider;
    float delayTimer = 0f;

    void Start()
    {
        infoText = GameObject.Find("InfoText").GetComponent<Text>();
        infoText.enabled = false;
        delaySlider = GameObject.Find("DelaySlider").GetComponent<Slider>();
        delaySlider.gameObject.SetActive(false);
    }

    // Update is called once per frame
    void Update()
    {
        Vector3 rayDirection = transform.TransformDirection(Vector3.forward) * 20;
        Debug.DrawRay(transform.position, rayDirection, Color.green);
        RaycastHit hit;

        if (Physics.Raycast(transform.position, rayDirection, out hit, 50))
        {
            //Debug.Log("hitting something: " + hit.collider.gameObject.name);
            if (hit.collider.CompareTag("InfoSpot"))
            {
                DisplayInfoText(hit.collider.gameObject.name);
            }
            if (hit.collider.CompareTag("TeleportSpot"))
            {
                MoveToScene(hit.collider.gameObject.name);
            }
        } else
        {
            ResetHit();
        }
    }

    void DisplayInfoText(string gameObjectName)
    {
        infoText.enabled = true;
        if (gameObjectName == "InfoCapsule")
        {
            infoText.text = "This is a capsule on a table";
        } else
        {
            infoText.text = "This is a default message.";
        }
    }

    void MoveToScene(string sceneName) {
        delayTimer = delayTimer + Time.deltaTime;
        delaySlider.gameObject.SetActive(true);
        delaySlider.value = delayTimer;
        if (delayTimer > 2)
        {
            SceneManager.LoadScene(sceneName);
        }
    }

    void ResetHit() {
        infoText.enabled = false;
        delaySlider.gameObject.SetActive(false);
        delayTimer = 0f;
    }

}
```

_CameraMouseControl.cs_

```cs
using UnityEngine;

public class CameraMouseControl : MonoBehaviour
{
    public float sensitivyX;
    public float sensitivyY;
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
    }
}
```

## Next Steps: Preparing for VR development in Unity

You need to sign up for Oculus developers account (and have an Facebook account) to activate the developer mode in Oculus Quest 2 device:

- [create](https://developer.oculus.com/manage/organizations/create) a new organization or join to an existing one (ask teacher for a link, if needed)
- [verify](https://developer.oculus.com/manage/verify) your developer account
- Install Meta Horizon (Quest) mobile app [android](https://play.google.com/store/apps/details?id=com.oculus.twilight) or [ios](https://apps.apple.com/us/app/meta-horizon/id1366478176) for linking the VR headset to your account and to your mobile device
  - activate headset's _developer mode_ in the app

**Remember to bring your VR headset to the next class!**
