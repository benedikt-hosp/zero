# ZERO: Eye Tracking Controller Interface for VR

ZERO is an open-source eye-tracking controller interface designed for virtual reality applications. This README provides guidance on how to integrate and use ZERO in your VR projects. The main purpose of ZERO is to support scientist who want to do eye-tracking in virtual reality. 


<!-- blank line -->

Click on the image to see the video about how to add the script:
<!-- blank line -->

[![Watch the video](https://img.youtube.com/vi/k842mTuHbdM/hqdefault.jpg)](https://youtu.be/k842mTuHbdM)

<!-- blank line -->


## Table of Contents
1. [Choose Devices](#1.-eye-tracking-and-vr-devices)
2. [How to access the controller](#2-how-to-access-the-controller)
3. [How to write gaze to file](#3-how-to-write-gaze-to-file)
4. [Get Live Gaze signal](#4-get-live-gaze-signal)
5. [Call device specific calibration](#5-call-device-specific-calibration)
6. [Device specific functions](#6-device-specific-functions)
7. [How to add a new Eye-tracker to ZERO](#6-how-to-add-a-new-eye-tracker-to-zero)
8. [How it works](#7-how-it-works)



## Unity inspector settings
To use the script you can attach the ZERO script to any game object on top level.
The inspector view has the following options:
- Choose your VR glasses 
- Choose eye-tracking device
- Specify if you want to save gaze files
- Specify the path where ZERO saves gaze files.
- Start initial calibration on load


![If you choose to use the scripts by attaching them to a game object, you need to specify which VR Headset you use and which EyeTracker.](HowToFiles/ChooseProvider.png "Provider")


## 2. How to access the controller

If you want to have more control of what is happening when, you can use these functions to personalize ZERO to your needs.
```
ZERO etController;					# create new object of ZERO
									# by taking the ET provider form inspector of Unity.
etController = new ZERO(Providers eyeTrackingProvider);		

etController.StartET()				# call this function to start harvesting gaze samples from the device
etController.CloseET()				# call this function to stop all processes
```

The ZERO object is the only object you will need to configure your controller. 

## 3. How to write gaze to file

When you checked "Write gaze to file" checkbox in the inspector, ZERO takes care about saving gaze files for each user in the personal user folders.
This is done with an GazeWriter Object.

## 4. Get Live Gaze signal

     * this.etController.getSetEyeTracker.ET_NewSampleAvailable_Event += GetCurrentGazeSignal;      // Register a method that is called when the eye tracker has a new sample


## 5. Call device specific calibration

etController.etpc.Calibrate()		# to call device specific calibration procedure any time

## 6. Device specific functions


## 7. How to add a new Eye-tracker to ZERO

## 8. How it works





public EyeTrackingProviderInterface getSetEyeTracker				# get the singleton of ZERO.
{
	 get 
	 	{
			 return this.etControler.etpc.eyeTrackingProviderInterface;
		}
}

     * Constructors
     * this.etController = new ZERO(eyeTrackingProvider);                                           // ZERO constructor
     * this.gazeWriter = new GazeWriter(userFolder, this, this.etController.getSetEyeTracker);      // GazeWriter constructor
     * 
     * this.etController.etpc.Calibrate())                                                          // Call to the eye tracker to calibrate
     * this.etController.getSetEyeTracker.ET_NewSampleAvailable_Event += GetCurrentGazeSignal;      // Register a method that is called when the eye tracker has a new sample
     * this.etController.StartET();                                                                 // Start eye tracking
     * this.gazeWriter.startGazeWriting();                                                          // Start writing gaze samples to file
     * this.gazeWriter.stopGazeWriting();                                                           // stop gaze writer and close file.
     * this.etController.CloseET();                                                                 // stop eye tracking thread
     */


public bool isCalibrated();									# Returns wether the eye tracker is calibrated or not

public GameObject loadGameobject(string path, string name);	# According to the EyeTrackingProvider, the correct game objects and prefabs are loaded into the scene

public void startET();										# Some eye trackers need to subscribe to the gaze signal or enable it. So call it once, before you
access data to enable it.

public void startSampleHarvester();							# Starts the coroutine that is collecting the latest gaze samples of the eye tracker  

public void stopSampleHarvester();							# Stops the coroutine that is collecting the latest gaze samples of the eye tracker  

public void close();										# Destructs all objects.
```

Create object of event tracker, wherever you want
```
    GazeWriter gazeWriter;
    public GazeWriter(string userFolder, EyeTrackingProviderInterface eyeTrackerObject)
```

Starts a coroutine that is periodically writing gaze samples to specific file
```
public void startGazeWriting()`
```

Stops the coroutine that writes gaze samples to file
```
public void stopGazeWriting();
```

Function returns the current system time in milliseconds
```
public long getCurrentSystemTimestamp()
```



#### Private functions

```
IEnumerator writeGazePeriodically();
WriteGazeToFile(List<SampleData> currentSamples); 
WriteGazeData(SampleData gazeData)
```
<!-- blank line -->

## 3. Add new Events and Listeners

How to Add Event listeners:

Let's assume we want to raise an event (let's call it "MyEvent") in our ExperimentController Gameobject when the key "C" on the keyboard is clicked and listen to it in our GazeWriter.

e.g.
```
public class ExperimentController
{
	.
	.
	.
	.
	
	void Update()
	{
		  if (Input.GetKeyUp(KeyCode.C))
		  {
				MyEvent();
		  }
	}
}
```


1. We have to define a delegate (which defines a callback or event listener ) somewhere public. To have a tidy code, we add it to our "Delegates.cs"
Let's call our delegate MyEvent_delegate, but you can name whatever you want.

```
public delegate void MyEvent_delegate();
```

2. We can only call events that are connected to a delegate. To create an event, 
we add it as a class member to a class in which the event will be raised.
Our event has to have the type of the delegate we just created.

e.g. 
```
public class ExperimentController
{

	public event MyEvent_delegate MyEvent;
	
	public ExperimentController(...){}
	.
	.
	.
	
}
```

Now we created an event we can raise when something happens and we created a delegate that allows us to add listeners to that event.





3. To add a listener to that event: Let's say we want to listen to that new event in our GazeWriter.

This time we have to add our listener inside the constructor of the class we want to handle the event.

```
public class GazeWriter
{
	public GazeWriter(...)
	{
		// Register a method "MyEvent_handlerMethod" to listen to the event "MyEvent"
		// raised by the Class "ExperimentController".
		ExperimentController.MyEvent += MyEvent_handlerMethod;
	}
}
```





4. Create the handler method

```
public void MyEvent_handlerMethod()
{
	// Do what you want to do when the letter c is pressed.
	e.g. write down a message with a timestamp into our events.csv file.
	
}
```


Note: It is also possible to add parameters to delegates.

1. Raise event with parameter: MyEvent(cCode);
2. Tell delegate that we want to access a parameter: public delegate void MyEvent_delegate(string cCode);
3. Add the parameter to your handler method: public void MyEvent_handlerMethod(string cCode)


And to listen to events raised by objects. Then you have to change your registration to that object:

```
ExperimentController.MyEvent += MyEvent_handlerMethod;
```
to
```
myObject.MyEvent += MyEvent_handlerMethod;
```

If you use ZERO in your research, please cite the following paper: 

@inproceedings{hosp2023zero,
  title={ZERO: A Generic Open-Source Extended Reality Eye-Tracking Controller Interface for Scientists},
  author={Hosp, Benedikt W and Wahl, Siegfried},
  booktitle={Proceedings of the 2023 Symposium on Eye Tracking Research and Applications},
  pages={1--4},
  year={2023}
}