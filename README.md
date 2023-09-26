# HW3: Building an AR Board Game

In this homework you will be deploying the game that you built in HW2, to a mobile device. You are welcome to improvise the game you built earlier or build an entirely new one. This document will help you set up an AR mobile device application using Unity and AR Foundation. It will guide you through the steps of: finding planes in the scene, selecting your game board location, and creating basic interaction elements. 

Note that just like in HW2, your game must have an end condition and indicate to the user the result of the game (ie. whether they have won or lost, or what their final score is). 

## Logistics

Use the repository created when you accept the assignment on GitHub classrooms (this repo). The course staff will look at this repo to evaluate your code for this assignment, so please make sure you submit your code into this repository when you turn in your assignment.

### Deadline

Please see bCourses for HW deadline. Videos and code should be uploaded before the deadline. Any homeworks submitted after the deadline will automatically use slip days as per the course guidelines.

### Academic honesty
Please do not post code to a public GitHub repository, even after the class is finished, since these HWs will be reused both  in the future.

This HW is to be completed individually. You are welcome to discuss the various parts of the HWs with your classmates, but you must implement the HWs yourself -- you should never look at anyone else's code.

## Deliverables

### 1. Video
You will make a 2 minute video showing off the features of your game. The video must include a verbal description of your project and it’s features. You must also include captions corresponding to the audio. This will be an important component of all your homework assignments and your final project so it is best you get this set up early. 

We recommend using a screen recorder to record video of your game in action. 
For iOS you can use the built in screen recorder: https://support.apple.com/en-us/HT207935 
For Android devices you will likely want to install a 3rd party screen recorder. AZ Screen Recorder works well and is easy to use: https://play.google.com/store/apps/details?id=com.hecorat.screenrecorder.free&hl=en_US 

Please see this guide for proper video submissions:

https://docs.google.com/document/d/1cEpDe3CDDrIXoO1ljZLEz-Bpvs9N-DbY89AtO_-Ku5Q/edit?usp=sharing

### 2. Code
You will also need to push your project folder to this repo. 

**Submit a link to your repo and your video on bCourses.** Do not modify your repo after the submission deadline.



## Before You Start

Make sure you have a compatible AR device - that is, a device that supports ARKit on iOS or ARCore on Android. 
A list of supported Android devices can be found here:
https://developers.google.com/ar/discover/supported-devices 
A list of supported iOS devices is at the very bottom of this page: https://www.apple.com/ios/augmented-reality/ 

Note: For ios devices you will also need to be developing on an Apple computer.
If you do not have a supported device combination (either a supported Android device or an iOS device + an Apple computer), you may borrow an Android phone from us. Our supply of Android phones is limited so please use your own device if possible. Plus if you use your own device you will always be able to show off your app to friends and family.  

### For Android users
You will need to download and install Android Studio to deploy to your device.
You will need to include Android Build Support in your Unity install
To do this: In Unity Hub go to Installs->...->Add Modules and make sure Android Build Support is checked.

### For iOS users
You will need to download and install XCode to deploy to your device.
You will need to include iOS Build Support in your Unity install
To do this: In Unity Hub go to Installs->...->Add Modules and make sure iOS Build Support is checked.


## Setting Up Your Project

We recommend working in your hw2 Unity project. Make a manual backup to use as a reference point later.

### For Android users:
* Go to Window -> Package Manager 
    * Select Packages -> Unity registry
    * Select ARCore XR Plugin and install it.
    * Select AR Foundation and install it. 
* Go to File -> Build Settings
   * Select Android and press “Switch Platform” (this may take about a minute to complete)
   * Go to Player Settings -> Other Settings
      * Change Package Name to: com.<your name>.<your project> example: com.johndoe.arboardgame
      * Set Minimum API Level to Android 7.0
   * Select Vulkan in Graphic APIs, and remove it by pressing the '-' button
 
 ![](/Instructions/vulkan.PNG)

### For iOS users:
* Go to Window -> Package Manager 
   * Select Packages -> Unity registry
   * Select ARKit XR Plugin, ensure the version is set to 2.1.1, and install it.
   * Select AR Foundation and install it. 
* Go to File -> Build Settings
   * Select iOS and press “Switch Platform” (this may take about a minute to complete)
   * Go to Player Settings -> Other Settings
      * Change Package Name to: com.<your name>.<your project> example: com.johndoe.arboardgame
      * Set Signing Team ID to the ID for your Apple account. To find this open Keychain Access application (Applications > Utilities > Keychain Access on your Apple computer) under My Certificates double click on your iPhone Developer certificate. Your Signing Team ID is the value listed under Organization Unit.
         * If this is not present, sign in to developer.apple.com with your Apple ID, you may be asked to accept the Developer Agreement. Do this, but do NOT sign up for a paid account. 
         * Open XCode->Preferences->Accounts and add your Apple ID. You will then need to create an XCode project, XCode->File->New->Project and create any iOS project, to initialize a certificate.
      * Check: Automatically Sign
      * Set target minimum iOS Version to: 11.0
      * Select: Requires ARKit support 
      * Set Architecture to: ARM64
   * Add camera usage description. Something along the lines of "Uses camera to render room for AR game"

If you are asked to update your project settings to support the new input system, select Yes.

At this point you should be able to build a blank API by pressing File-> Build Settings -> Build

## Identifying Planar Surfaces in AR

Now you are ready to build your first AR App. The first thing we will do is create a visualizer for planar surfaces detected by your device. Later you will use these planes as locations where you can place your game board. 

Create a new scene in the Scenes folder.

Delete the main camera that is placed in the scene by default by unity. We will replace this by it’s AR Equivalent. This requires two objects: AR Session and XR Origin. 

In the newest version of AR Foundation these will be in GameObject (or '+' or Right Click in 'Hierarchy') -> XR -> AR Session. Also add an XR Origin (same menu)

Select XR Origin. In the inspection tab select Add Component and in the search box type “AR Plane Manager” and add it. **Make sure you select the UnityEngine.XR version!** You will notice that the plane prefab field is empty. We will fill this field by creating our own plane prefab. 

In you scene hierarchy create an empty game object and name it “AR Plane Visualization”. Select this object. We are going to add several components to this: 
AR Plane, AR Plane Mesh Visualizer, Mesh Renderer, Mesh Collider and Mesh Filter

![image15.png](/Instructions/image15.png)

This handles all the aspects for modifying and visualizing the plane, however it is currently using the default material for visualization which is opaque and not great for visualization. So let’s create our own material.  

In your project window, in the Assets folder, create a new folder and name it Materials. In this folder, Right Click->Create->Material and name it whatever you like. Select this material, set the rendering mode to: Transparent and click on the color selector next to Albedo. You can choose whatever color you like but set the Alpha Channel (the slider labeled A) to 75. Lastly change the source from “Metallic Alpha” to “Albedo Alpha”. This allows whatever object we attach this material to to appear semi-transparent. 

![image7.png](/Instructions/image7.png)

Select our AR Plane Visualization Object and drag your new material into its component list. This completes our object. To turn it into a prefab, first create a Prefabs folder under Assets. Then simply drag our AR Plane Visualization Object from the hierarchy into this folder. **You can now delete the object from the scene hierarchy. **

Lastly drag your new prefab over to the Plane Prefab section of AR Plane Manager.

![image10.png](/Instructions/image10.png) 

Now it is time to build and run your application. Simply plug in your Mobile Device via USB and in Unity select File->Build And Run. You will likely get an error. 

### For Android Users:
You will likely need to enable developer options on your device. On your phone go to Settings -> System -> About Phone and tap the Build Number repeatedly until you get the message “You are a Developer” 
### For iOS Users: 
You will need to tell your device to trust your developer certificate. On your phone go to Settings -> General -> Device Management->Your Apple ID->Trust your Apple ID

Either redo “Build and Run”, or at this point you can just open the installed application on your device. If you move your device slowly around your area you should see semi-transparent planes overlaid over planar regions of the scene such as the floor, your desk, or your keyboard. Note that it may take a few seconds for planes to be detected and they will only appear in regions that have visible texture (sorry no playing AR games in the dark or on white walls). 


### Possible Issues

 - If you see a black screen: Select Edit-> Project Settings from Menu and make sure ARCore/ARKit is checked

![image20.PNG](/Instructions/image20.PNG) 

## Updating HW2 Code

You will need to update your code from HW2 to work with ARFoundation. Here we provide an example of how to convert various code snippets from the HW2 walkthrough. If you wrote any custom code that uses these same snippets, or developed new features that uses the same functions, you will need to adapt these snippets to your specific code.

### Using directives

Convert any using directives from using the ARFoundationSim code to using Unity's AR Foundation

```C++
using cs294_137.hw2;
```

turns into

```C++
using UnityEngine.XR.ARFoundation;
using UnityEngine.XR.ARSubsystems;
```

### Raycast Manager

Any code that uses the ARRaycastManager to select objects in the scene need to be updated to use **Touch** instead of Mouse. Also note that the ref keyword is removed from the second parameter to Raycast
```C++
Vector2 touchPosition = Input.mousePosition;
if (raycastManager.Raycast(touchPosition, ref hits, TrackableType.PlaneWithinPolygon))
```

turns into

```C++
Vector2 touchPosition = Input.GetTouch(0).position;
if (raycastManager.Raycast(touchPosition, hits, TrackableType.PlaneWithinPolygon))
```

### Plane Types

If your code uses the planeType property of ARPlane, it will need to be converted to use plane classification

```C++
plane.planeType == PlaneType.HORIZONTAL
```

turns into

```C++
plane.classification == PlaneClassification.Table
```

### Detection Mode

When you manually change the detection mode of the ARPlaneManager, you need to use requestedDetectionMode instead

```C++
planeManager.detectionMode = PlaneDetectionMode.Horizontal;
```

turns into

```C++
planeManager.requestedDetectionMode = PlaneDetectionMode.Horizontal;
```

## Using your game from HW2

You can continue to work in the same Unity project from HW2. Just keep in mind, you will need to submit the code in this new repo.

Make sure to complete the steps in the setting up your project section (of this Readme)

Delete "Miniworld_FloorPlan229_physics" gameobject from the scene (we no longer require this simulated environment), and delete the folders - "ARFoundationSim" and ":"SimEnvironments".  

Delete the "ARCamera" GameObject and Follow the steps outlined in this readme. When making changes to your HW2 code, make sure to reference the section about updating HW2 code.

The image below indicated the stuff that need to be deleted from your project.

![image16.PNG](/Instructions/image16.PNG)


## Adding physically tracked virtual objects into the scene
In this section, we will learn how to track images in a physical scene and use that for controlling virtual elements in the scene. To do this, we will leverage ARFoundation's image tracking ability. 

First we will setup the image that we need AR kit to track. 

We will add all image(s) we want our AR applicaiton to track. We will first add images to the project. In the project window right click -> Import new assets. Now add all the images that you want to track.

In the project window, right click -> Create -> XR -> ReferenceImageLibrary

![image17.PNG](/Instructions/image17.PNG)

Now select the Reference Image Library and in the inspector, use 'Add Image' to add images that you would like to track. Select the texture and choose the image(s) that you imported.

Next check 'specify size' and type in the physical size of your printed image. Note that, aspect ratio is determined based on the texture dimensions. So make sure to type in the physical measurements on the printout that corresponds to the image you uploaded. Note: cropping any unneccssary white borders on the uploaded image could help you get this right.

![image18.PNG](/Instructions/image18.PNG)

Now select the XR Origin Game Object, and through the inspector, Add 'AR Tracked Image Manager' component. Drag your ReferenceImageLibrary from the project window to `SerializedLibrary' variable of the manager. This ensures that the AR application tracks the images that we have in the referece image library. Make sure to set the maximum number of moving components as the number of images that you want to track.

![image19.PNG](/Instructions/image19.PNG)

Now we will need a script that will help us figure out which images are currently being tracked in the scene. Below is a template script that needs to be attached to the XR Origin game object.  Note that this is only a template script, and we use it to attach cubes to every uniquely tracked images. You  will certainly need to modify this to suit the needs of your game. 

```C++
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.XR.ARSubsystems;
using UnityEngine.XR.ARFoundation;

// This component listens for images detected by the <c>XRImageTrackingSubsystem</c>
// and overlays a cube on each of the tracked image
// Note that this code assumes that all tracked images are unique and named differently

[RequireComponent(typeof(ARTrackedImageManager))]
public class TrackedImageInfoManager : MonoBehaviour
{
    

    ARTrackedImageManager m_TrackedImageManager;
    Dictionary<string,GameObject> gameobjectDict = new Dictionary<string,GameObject>();
    private TrackingState prevTrackingState = TrackingState.None; //For newer versions of ARFoundation
    void Awake()
    {
        //This gets a reference to the AR Tracked Image Manager attached to the 'XR Origin' gameobject
        m_TrackedImageManager = GetComponent<ARTrackedImageManager>();
    }

    void OnEnable()
    {
        m_TrackedImageManager.trackedImagesChanged += OnTrackedImagesChanged;
    }

    void OnDisable()
    {
        m_TrackedImageManager.trackedImagesChanged -= OnTrackedImagesChanged;
    }


    void OnTrackedImagesChanged(ARTrackedImagesChangedEventArgs eventArgs)
    {
        
        //eventArgs.added contains all the newly trackedImages that were found this frame
        foreach (var trackedImage in eventArgs.added)
        {

            //Write code here to deploy stuff whenever a new image is found in the tracking
            //e.g. Create a new virtual object and/or attach it to the tracked image
            //trackedImage.referenceImage.name -> Name of the tracked image
            //trackedImage.transform.position -> Position of the tracked image in the real world 
            //trackedImage.transform.rotation -> Rotation of the tracked image in the real world 

            //For example, here when we find a new tracked Image, we create a cube, and place it at the location of the tracked image. We add the gameobject to a dictionary, using the name of the image as a key.
            GameObject cube = GameObject.CreatePrimitive(PrimitiveType.Cube);
            cube.transform.localScale = new Vector3(0.1f,0.1f,0.1f);
            cube.transform.position = trackedImage.transform.position;
            cube.transform.rotation = trackedImage.transform.rotation;
            gameobjectDict.Add(trackedImage.referenceImage.name,cube);

            prevTrackingState = trackedImage.trackingState; //For newer versions of ARFoundation
        }

        //eventArgs.removed contains all the trackedImages that were not found by the AR camera, either because it was removed from the camera's views or becuase the camera could not detect it.
        foreach (var trackedImage in eventArgs.removed)
        {
            //If we loose tracking of the image, we destroy the conrresponding cube and remove the image name's entry from the dictionary
            Destroy(gameobjectDict[trackedImage.referenceImage.name]);
            gameobjectDict.Remove(trackedImage.referenceImage.name);
           
            prevTrackingState = trackedImage.trackingState; //For newer versions of ARFoundation
        }
   
        //eventArgs.updated contains all the trackedImages which are currently being tracked, but its position and/or rotation changed
        foreach (var trackedImage in eventArgs.updated)
        {
            //trackedImage.transform.position -> Updated Position of the tracked image in the real world 
            //trackedImage.transform.rotation -> Updated Rotation of the tracked image in the real world 

            //if tracked image moves, we move the corresponding gameobject to match it's position.
            gameobjectDict[trackedImage.referenceImage.name].transform.position = trackedImage.transform.position;
            gameobjectDict[trackedImage.referenceImage.name].transform.rotation = trackedImage.transform.rotation;
  
            //Add the Below if else code  block For newer versions of ARFoundation
            if(prevTrackingState == TrackingState.Tracking && trackedImage.trackingState!= prevTrackingState)
            {
                //We lost and then regained tracking so we add cube again
                
                GameObject cube = GameObject.CreatePrimitive(PrimitiveType.Cube);
               cube.transform.localScale = new Vector3(0.1f,0.1f,0.1f);
               cube.transform.position = trackedImage.transform.position;
               cube.transform.rotation = trackedImage.transform.rotation;
               gameobjectDict.Add(trackedImage.referenceImage.name,cube);

            }
            else if(prevTrackingState != TrackingState.Tracking && trackedImage.trackingState != prevTrackingState)
            {
                //We lost tracking so we remove the cube
                Destroy(gameobjectDict[trackedImage.referenceImage.name]);
                gameobjectDict.Remove(trackedImage.referenceImage.name);
            }
   
            prevTrackingState = trackedImage.trackingState; //For newer versions of ARFoundation
            
        }
    }
}

```

In this HW, you will need to use these tracked images to add to the gameplay of games that you developed in HW2. We look for creative game interaction designs by which you will use these tracked images to add to the experience of your game and make it more enjoyable. Try to think of ways in which these tracked images can intercat with the different elements in your game. A couple of examples, include:

- In a pokemon game you may track images of pokemon (cards). When a player pushes a pokemon card into the view of the camera, the corresponding pokemon would be spawned into the scene over the card. E.g. - https://www.youtube.com/watch?v=3ziNTo-BCT0&ab_channel=Fyone . If required, this may also activate the fire arena (P.S. Charizard is a fire type pokemon). 

- In a battle game, you may track images of a sword. When a player brings in the image of a sword into the scene, a virtual sword will spawn at the location of the image. The player can use the virtual sword to attack virtual enemies or virtual objects in the scene. 

VERY IMPORTANT: In all these interactions, design your on-screen and tracked object interactions keeping in mind that the player will have to use one hand to hold the mobile and see the scene. So, only one hand might be free to interact using tracked objects. It important to ensure that these interactions are feasible, ergonomic and enjoyable.

