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

## Deliverables:

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



## Before You Start:

Make sure you have a compatible AR device - that is, a device that supports ARKit on iOS or ARCore on Android. 
A list of supported Android devices can be found here:
https://developers.google.com/ar/discover/supported-devices 
A list of supported iOS devices is at the very bottom of this page: https://www.apple.com/ios/augmented-reality/ 

Note: For ios devices you will also need to be developing on an Apple computer.
If you do not have a supported device combination (either a supported Android device or an iOS device + an Apple computer), you may borrow an Android phone from us. Our supply of Android phones is limited so please use your own device if possible. Plus if you use your own device you will always be able to show off your app to friends and family.  

### For Android users: 
You will need to download and install Android Studio to deploy to your device.
You will need to include Android Build Support in your Unity install
To do this: In Unity Hub go to Installs->...->Add Modules and make sure Android Build Support is checked.

### For iOS users:
You will need to download and install XCode to deploy to your device.
You will need to include iOS Build Support in your Unity install
To do this: In Unity Hub go to Installs->...->Add Modules and make sure iOS Build Support is checked.


## Setting Up Your Project:

In Unity Hub create a new 3D Project.

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


## Identifying Planar Surfaces in AR:

Now you are ready to build your first AR App. The first thing we will do is create a visualizer for planar surfaces detected by your device. Later you will use these planes as locations where you can place your game board. 

Create a new scene.

Delete the main camera that is placed in the scene by default by unity. We will replace this by it’s AR Equivalent. This requires two objects: AR Session and AR Session Origin. 

In the newest version of AR Foundation these will be in GameObject (or '+' or Right Click in 'Hierarchy') -> XR -> ARSession / XR Origin

Select XR Origin. In the inspection tab select Add Component and in the search box type “AR Plane Manager” and add it. **Make sure you select the UnityEngine.XR version!** You will notice that the plane prefab field is empty. We will fill this field by creating our own plane prefab. 

In you scene hierarchy create an empty game object and name it “Plane Visualization Object”. Select this object. We are going to add several components to this: 
AR Plane, AR Plane Mesh Visualizer, Mesh Renderer, Mesh Collider and Mesh Filter

![image15.png](/Instructions/image15.png)

This handles all the aspects for modifying and visualizing the plane, however it is currently using the default material for visualization which is opaque and not great for visualization. So let’s create our own material.  

In your project window, in the Assets folder, create a new folder and name it Materials. In this folder, Right Click->Create->Material and name it whatever you like. Select this material, set the rendering mode to: Transparent and click on the color selector next to Albedo. You can choose whatever color you like but set the Alpha Channel (the slider labeled A) to 75. Lastly change the source from “Metallic Alpha” to “Albedo Alpha”. This allows whatever object we attach this material to to appear semi-transparent. 

![image7.png](/Instructions/image7.png)

Select our Plane Visualization Object and drag your new material into its component list. This completes our object. To turn it into a prefab, first create a Prefabs folder under Assets. Then simply drag our Plane Visualization Object from the hierarchy into this folder. You can now delete the object from the scene hierarchy. 

Lastly drag your new prefab over to the Plane Prefab section of AR Session Origin-> AR Plane Manager.

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

## Changes from HW2:

The following sections will walk you through code snippets for developing AR applications for mobile devices. Most of the code snippets is similar to the corresponding counter parts in hw2. Wherever, a change of code is present, it is marked either with a `TO_COMMENT' or 'TO_ADD' tag. 
'TO_COMMENT' indicates the line/block of code that needs to be commented out of scripts that you used in HW2
'TO_ADD' marks the line/block of code that needs to be added.


## Placing Your Game Board:

Go to GameObject -> 3D Object -> Cube and name your new cube, “Game Board”. Lets change this into more of a game board shape. Select the Game Board and in the inspector set the scale x,y and z values to 0.6, 0.02, and 0.6 respectively. Unity is set up such that the values of 1 unit in game coordinates corresponds to 1 meter in physical coordinates. Since the cube model is a 1 unit cube, these scale parameters correspond to a game board that is 60cm width and height with a 2cm thickness. 

For now, we will also deactivate our game board so that will not be present at the start of our game. To do this, deselect the checkbox at the very top of the inspector tab for your game board (right above the word “tag”).

![image14.png](/Instructions/image14.png)

Now we need to set up the code that allows the user to choose a position for the game board. Since we don’t know what the area will look like in advance, we will let the user choose where they want to place the game board. To the AR Session Origin we will add the component ARRaycastManager. Raycasting is how we convert a 2D position on the screen to a 3D position in world space. The ARRaycastManager lets us raycast to the planar regions detected by the Plane Manager we created in the previous step. 

Next we will add the script to actually place the game board. To your AR Session Origin select Add Component -> New Script and name it PlaceGameBoard. Double click on the script (in the box next to the word script, not the component itself) to open it. 

Set up your script as shown below. For all code is this document, be sure to read the code and comments to understand what the code is doing. 
```C++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
// These allow us to use the ARFoundation API.
//using cs294_137.hw2; //TO_COMMENT
using UnityEngine.XR.ARFoundation; //TO_ADD
using UnityEngine.XR.ARSubsystems; //TO_ADD

public class PlaceGameBoard : MonoBehaviour
{
    // Public variables can be set from the unity UI.
    // We will set this to our Game Board object.
    public GameObject gameBoard;
    // These will store references to our other components.
    private ARRaycastManager raycastManager;
    private ARPlaneManager planeManager;
    // This will indicate whether the game board is set.
    private bool placed = false;

    // Start is called before the first frame update.
    void Start()
    {
        // GetComponent allows us to reference other parts of this game object.
        raycastManager = GetComponent<ARRaycastManager>();
        planeManager = GetComponent<ARPlaneManager>();
    }

    // Update is called once per frame.
    void Update()
    {
        if (!placed)
        {
            if (Input.touchCount > 0)
            {
                Vector2 touchPosition = Input.GetTouch(0).position;

                // Raycast will return a list of all planes intersected by the
                // ray as well as the intersection point.
                List<ARRaycastHit> hits = new List<ARRaycastHit>();
                //if (raycastManager.Raycast(//TO_COMMENT
                //    touchPosition, ref hits, TrackableType.PlaneWithinPolygon)) //TO_COMMENT
                 if (raycastManager.Raycast(//TO_ADD
                    touchPosition, hits, TrackableType.PlaneWithinPolygon)) //TO_ADD
                {
                    // The list is sorted by distance so to get the location
                    // of the closest intersection we simply reference hits[0].
                    //var hitPose = hits[0].pose; //TO_COMMENT
                    var hitPosition = hits[0].pose.position; //TO_ADD
                    // Now we will activate our game board and place it at the
                    // chosen location.
                    gameBoard.SetActive(true);
                    //gameBoard.transform.position = hitPose.position;//TO_COMMENT
                    gameBoard.transform.position = hitPosition; //TO_ADD
                    placed = true;
                    // After we have placed the game board we will disable the
                    // planes in the scene as we no longer need them.
                    //planeManager.SetTrackablesActive(false); //For older versions of AR foundation
                    planeManager.detectionMode = PlaneDetectionMode.None;

                }
            }
        }
        else
        {
            // The plane manager will set newly detected planes to active by 
            // default so we will continue to disable these.
            //planeManager.SetTrackablesActive(false); //For older versions of AR foundation
            planeManager.detectionMode = PlaneDetectionMode.None;
        }
    }

    // If the user places the game board at an undesirable location we 
    // would like to allow the user to move the game board to a new location.
    public void AllowMoveGameBoard()
    {
        placed = false;
        //planeManager.SetTrackablesActive(true);
        planeManager.detectionMode = PlaneDetectionMode.Horizontal;
    }

    // Lastly we will later need to allow other components to check whether the
    // game board has been places so we will add an accessor to this.
    public bool Placed()
    {
        return placed;
    }
}
```
When you return to the unity editor you should see your script component now has a field for “Game Board”. Drag your game board object from your scene hierarchy into this field. 

![image8.png](/Instructions/image8.png)

You should now build and run to test what you have done so far. By selecting a location on a detected plane, you should be able to place your game board. 

You will notice we haven’t actually used our function to allow the user to move the game board once it is placed. So let’s implement that. For this we are going to add a button to the screen that calls “AllowMoveGameBoard” when pressed. 

Select GameObject->UI->Canvas to add a Canvas to the scene. The Canvas is where we will place all 2D UI elements that are meant to appear attached to the screen. Now create a button GameObject->UI->Button. It should automatically appear under the canvas in the hierarchy.

![image5.png](/Instructions/image5.png)

First let’s set the button position and size to be more reasonable than the default. Select your button and inside the inspector change the width and height to 160 and 80 respectively. Then click on the icon showing multiple boxes and lines, above the word “Anchors”. This allows you to choose the general position of your button on the screen. Select bottom+center.  Now change the X and Y values of the Pivot field to 0.5 and 0 respectively. Lastly zero out the transform position, as these will have changed as you were modifying the other values to try and keep the button at its original position.

![image11.png](/Instructions/image11.png)

If you build and run now you should see a button present at the bottom of your screen, but clicking it doesn’t actually do anything. 

To change this, in your button inspector, scroll down to the field labeled “On Click”. Press the + selection at the bottom of this field. Where the word “none” appears now, drag your AR Session Origin from your scene hierarchy into this location. Lastly change the “No Function” selection to PlaceGameBoard->AllowMoveGameBoard(). If you build and run now, after placing your game board, pressing this button should bring back the place visualization and allow you to move your game board to a new location. 

![image3.png](/Instructions/image3.png)

Lastly let’s change the button label to something more intuitive than “Button”. Expand your Button object in the scene hierarchy and select the Text object that appears below it. Change the text field under “Text (Script)” in the inspector to “Move Board”. Build and Run to see your changes. 


## Making A Simple Interactable Object

Making interactable objects in AR is fairly easy. The short version is, we just have to check if a raycast from a user’s touch intersects with an interactable object, and call a function from that object. 

So let’s make a couple objects to interact with. Create two new cubes in the scene, rescale them to be 10cm x 10cm x 10cm, and rename them “AR Button 1” and “AR Button 2”. Drag them to be part of the Game Board in your hierarchy. Reactivate your game board so you can see it, and then reposition your buttons to be at two separate locations on your game board. Don’t forget to deactivate your game board again once you do this. 

![image6.png](/Instructions/image6.png)

To make it easy for us to call different functions for each button we will create an abstract class which each button will inherit from. In the project window at the bottom of the screen, Right Click->Create->C# Script and name it OnTouch3D. All you need to place in this script is:

```C++
public interface OnTouch3D
{
    void OnTouch();
}  
```

And then our AR Button 1 will inherit from this script and implement this function. For a simple interaction we will make the object move up by 10cm when pressed. In AR Button 1, Add Component -> New Script and name it “ARButton1”. Open this script and place the following code:

```C++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

// Adding OnTouch3D here forces us to implement the 
// OnTouch function, but also allows us to reference this
// object through the OnTouch3D class.
public class ARButton1 : MonoBehaviour, OnTouch3D
{
    // Debouncing is a term from Electrical Engineering referring to 
    // preventing multiple presses of a button due to the physical switch
    // inside the button "bouncing".
    // In CS we use it to mean any action to prevent repeated input. 
    // Here we will simply wait a specified time before letting the button
    // be pressed again.
    // We set this to a public variable so you can easily adjust this in the
    // Unity UI.
    public float debounceTime = 0.3f;
    // Stores a counter for the current remaining wait time.
    private float remainingDebounceTime;

    void Start()
    {
        remainingDebounceTime = 0;
    }

    void Update()
    {
        // Time.deltaTime stores the time since the last update.
        // So all we need to do here is subtract this from the remaining
        // time at each update.
        if (remainingDebounceTime > 0)
            remainingDebounceTime -= Time.deltaTime;
    }

    public void OnTouch()
    {
        // If a touch is found and we are not waiting,
        if (remainingDebounceTime <= 0)
        {
            // Move the object up by 10cm and reset the wait counter.
            this.gameObject.transform.Translate(new Vector3(0, 0.1f, 0));
            remainingDebounceTime = debounceTime;
        }
    }
}
```


Next let’s add a tag to our object to make it easy to tell that this object is interactable. Select your AR Button 1 and at the top of the inspector window, expand the “Tag” dropdown list and select “Add Tag”. Select + and add the tag “Interactable”. This tag will show up on the Tag dropdown list from now on. Select this as the tag for AR Button 1.

![image4.png](/Instructions/image4.png)

Now we need to create the script to actually perform the raycasting to this object. In AR Session Origin, Add Component -> New Script and name it “ARButton Manager”. In this script place the following: 

```C++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
//using cs294_137.hw2; //TO_COMMENT
using UnityEngine.XR.ARFoundation; //TO_ADD
using UnityEngine.XR.ARSubsystems; //TO_ADD

public class ARButtonManager : MonoBehaviour
{
    private Camera arCamera;
    private PlaceGameBoard placeGameBoard;

    void Start()
    {
        // Here we will grab the camera from the AR Session Origin.
        // This camera acts like any other camera in Unity.
        //arCamera = FindObjectOfType<ARCamera>().GetComponent<Camera>();//TO_COMMENT
        arCamera = GetComponent<ARSessionOrigin>().camera; //TO_ADD
        // We will also need the PlaceGameBoard script to know if
        // the game board exists or not.
        placeGameBoard = GetComponent<PlaceGameBoard>();
    }

    void Update()
    {
        //if (placeGameBoard.Placed() && Input.GetMouseButtonDown(0))//TO_COMMENT
        if (placeGameBoard.Placed() && Input.touchCount > 0) //TO_ADD
        {
            //Vector2 touchPosition = Input.mousePosition; //TO_COMMENT
            Vector2 touchPosition = Input.GetTouch(0).position; //TO_ADD
            // Convert the 2d screen point into a ray.
            Ray ray = arCamera.ScreenPointToRay(touchPosition);
            // Check if this hits an object within 100m of the user.

/*TO_COMMENT
            RaycastHit[] hits;
            hits = Physics.RaycastAll(ray, 100.0F);
            for (int i = 0; i < hits.Length; i++)
            {
                // Check that the object is interactable.
                if(hits[i].transform.tag=="Interactable")
                    // Call the OnTouch function.
                    // Note the use of OnTouch3D here lets us
                    // call any class inheriting from OnTouch3D.
                    hits[i].transform.GetComponent<OnTouch3D>().OnTouch();
            }
*/

//TO_ADD ================================================
            RaycastHit hit; 
            if (Physics.Raycast(ray, out hit,100))
            {
                // Check that the object is interactable.
                if(hit.transform.tag=="Interactable")
                    // Call the OnTouch function.
                    // Note the use of OnTouch3D here lets us
                    // call any class inheriting from OnTouch3D.
                    hit.transform.GetComponent<OnTouch3D>().OnTouch();
            }
//================================================
        }
    }
}
```
Build and run your game. When you place your game board, you should now see that this button moves up when you touch it. 

It is worth noting that that the button does not actually have to be visible for you to interact with this. This is useful if you want the user to be able to interact with empty spaces on the game board, or just want the selectable region to be bigger than the object that is displayed. 

Let’s turn AR Button 2 into an invisible button. First, don’t forget to add the “Interactable” tag since we are now going to set up interaction for this button. Then disable the checkbox next to the “Mesh Renderer” component. This stops the button from being rendered, making it essentially invisible. 

![image12.png](/Instructions/image12.png)

Since moving an invisible object doesn’t make much sense, let’s instead have this object display a message on the screen. Add a new script to AR Button 2 and name it “ARButton2”. Open the script and add the following: 

```C++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class ARButton2 : MonoBehaviour, OnTouch3D
{
    public Text messageText;

    public void OnTouch()
    {
        messageText.gameObject.SetActive(true);
        messageText.text = "Button2Pressed";
    }
}
```

We will then need to create the Text object for this to reference. Add a Text object to the scene GameObject->UI->Text and rename it “Message Text”. We will leave this object centered, but be sure the (X,Y,Z) positions are all set to 0. Change the width and height to 250 and 60 respectively and change the font size to 28.  As with our Game Board we are going to set this to inactive to start. 

![image13.png](/Instructions/image13.png)

To make this a proper message, let’s also add a script to make the text go inactive again after a specified time. Add a new script component and name it DisappearingText. Open it and add the following:

```C++
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class DisappearingText : MonoBehaviour
{
    // displayTime will be set to a public float
    // so that you can easily change it in the Unity UI
    public float displayTime = 1;

    private float timeRemaining;

    // Start is called before the first frame update
    void Start()
    {
        timeRemaining = displayTime;
    }

    // Update is called once per frame
    // but only while the object is active
    void Update()
    {
        timeRemaining -= Time.deltaTime;
        if (timeRemaining < 0)
        {
            timeRemaining = displayTime;
            this.gameObject.SetActive(false);
        }
    }
}
```

Next let’s add a panel to make the text more visible against the background scene. Right Click on your Message Text object in the scene hierarchy and select UI->Panel. This should automatically place a panel under the Message Text object. 

![image5.png](/Instructions/image5.png)

Select the panel and change the “Source Image” in the inspector to UISprite. 

![image1.png](/Instructions/image1.png)

Finally, select your AR Button 2 again and drag your Message Text object into the Message Text section of your AR Button 2 script. 

![image2.png](/Instructions/image2.png)

Build and run your game. After placing your game board you should now be able to click on the location where the button would be (if it were visible) and a message should appear on screen. 




## Using your game from HW2:


First copy your HW2 code to a new repository.

Make sure to complete the steps in the setting up your project section (of this Readme)

Delete "Miniworld_FloorPlan229_physics" gameobject from the scene (we no longer require this simulated environment), and delete the folders - "ARFoundationSim" and ":"SimEnvironments".  

Delete the "ARCamera" GameObject and Follow the steps outlined in this readme. When making changes to your HW2 code, you may use 'TO_COMMENT' and 'TO_ADD' tags to see what functiones need ot be changed.

The image below indicated the stuffs that need to be deleted from your project.

![image16.PNG](/Instructions/image16.PNG)


## (NEW) Adding physically tracked virtual objects into the scene:
In this section, we will learn how to track images in a physical scene and use that for controlling virtual elements in the scene. To do this, we will leverage ARFoundation's image tracking ability. 

First we will setup the image that we need AR kit to track. 

We will add all image(s) we want our AR applicaiton to track. We will first add images to the project. In the project window right click -> Import new assets. Now add all the images that you want to track.

In the project window, right click -> Create -> XR -> ReferenceImageLibrary

![image17.PNG](/Instructions/image17.PNG)

Now select the Reference Image Library and in the inspector, use 'Add Image' to add images that you would like to track. Select the texture and choose the image(s) that you imported.

Next check 'specify size' and type in the physical size of your printed image. Note that, aspect ratio is determined based on the texture dimensions. So make sure to type in the physical measurements on the printout that corresponds to the image you uploaded. Note: cropping any unneccssary white borders on the uploaded image could help you get this right.

![image18.PNG](/Instructions/image18.PNG)

Now select the AR session Origin Game Object, and through the inspector, Add 'AR Tracked Image Manager' component. Drag your ReferenceImageLibrary from the project window to `SerializedLibrary' variable of the manager. This ensures that the AR application tracks the images that we have in the referece image library. Make sure to set the maximum number of moving components as the number of images that you want to track.

![image19.PNG](/Instructions/image19.PNG)

Now we will need a script that will help us figure out which images are currently being tracked in the scene. Below is a template script that needs to be attached to the 'AR session Origin' game object.  Note that this is only a template script, and we use it to attach cubes to every uniquely tracked images. You  will certainly need to modify this to suit the needs of your game. 

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
        //This gets a reference to the AR Tracked Image Manager attached to the 'AR session Origin' gameobject
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

