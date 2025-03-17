# Introduction
Shadow recreation for Extended Reality productions have often relyed on **motion capture (MoCap) systems** or **AI-generated skeletal data** to approximate shadow placement in a 3D environment using 3D humanoids. These methods can be **expensive**, require extensive **setup**, and are limited to **predefined tracked persons**.

This project introduces a novel approach to **digital shadow reconstruction** using high-frame-rate LED screen technology, specifically **GhostFrame** or **FrameRemapping**. 
By utilising a camera with a **sensor sync offset** or using a Genlock sync signal with **sync offset**, A different slice of time can be captured in the **Primary Camera** compared to the **Shadow Camera**, allowing us to:
- **Capture the Virtual Enviroment in the Primary Camera** using Disguise XR workflows. 
- **Capture a Green Screen background in the Shadow Camera** This Shadow Camera should be in the location of your Digital light source.

Unlike MoCap-based or AI-generated solutions, this method requires **no tracking system** and works with **any object**, making it significantly more **accessible**, **flexible**, and **cost-effective**.
This document outlines the **process**, **technical setup**, and **potential applications** of this technique, providing a foundation for further development and refinement.


# Process

- **Capture the Virtual Enviroment in the Primary Camera** using Disguise XR workflows aka (MRset). 
- **Capture a Green Screen background in the Shadow Camera** This Shadow Camera should be in the location of your Digital light source.
- **Key any object or persons** captured in the Shadow Camera. Resulting in a cutout coloured Image.
- **Coloud Adjust this keyed footage to Black** This can be called our **ShadowPass**.
- **Re-project (Perspective Mapping) the ShadowPass** from the perspective of the **Shadow Camera** onto a Digital floor plate.
- **Render the Diginat Floor Plate** to the Mix Reality set (MRset) BackPlate Mapping.
- **Using Blur and Gradient Masking** to softern the ShadowPass as required.




# Technical Setup

## XR Stage Setup 
### Disguise Hardware
- 2x VX 4+
- 2x RX III
### LED Screen
- [ROE RB1.9BV2](https://www.roevisual.com/en/products/ruby)
### LED Prosessor
- [Megapixel Helios 8k (2x Displayport)](https://megapixelvr.com/helios/)
### Genlock Source / Sync Generator
- [Evertz 5601MSC Master Sync and Clock Generator](https://evertz.com/products/5601MSC)
### Camera and Lenses
- 2x RED KOMODO
- 1x 14mm
- 1x 18-55mm

## Time Offset
For this workflow you must be able to offset when your  **Shadow Camera** captures its image. This is posable in two main ways, a Sensor Offset setting built into the Camera or a Genlock Offset build into your Genlock Source / Sync Generator.
### Sensor Offset
A Camera Sensor Offset refers to a delay in when the camera starts capturing an image relative to the reseved Genlock sync signal. This affects what moment in time the camera caputres a frame.
### Genlock Offset
A Genlock Offset refers to a timing adjustment in the synchronization signal shared between devices (cameras, LED walls, etc.). It ensures everything stays in sync but some devices can introduce a slight delay per ouput to align the frames correctly across different systems.
The setting we change on the Evertz is the **Vertical Phase Offset**

### Chosed setup
On our RED Komodo cameras the Sensor Offset it called ***"Sensor Sync Shift"**. That can be used but I found it fiddily so I chose to use my Evertz Genlock output Offsets. 
In complex high framerate studios I use the termanology "Time Slice 1", "Time Slice 2" or "GhostFrame Slice 1", "GhostFrame Slice 2". This reduces confusion when Documenting, comunicating and using Disguise Designer. 
The following is what Offsets worked with our GhostFrame setup. These values are dependent on your own setup and requirements.


| Camera             | Time Slice   | Vertical Phase Offset |
|--------------------|--------------|-----------------------|
| **Primary Camera** | Time Slice 1 | 0                     |
| **Shadow Camera**  | Time Slice 2 | 440                   |




## Multi Frame LED Processor Mapping | GhostFrame/Frame Remapping
Both GhostFrame and Frame Remapping are innovative solutions designed to enhance camera and high framerate workflows involving LED screens, but they currently operate through distinct methodologies.

### Chosen Setup
GhostFrame was used in this setup due to our studio haveing Megapixal prosessing. As we demonstrate multiple background images at once we have 2x Display connections from our VX 4+ to Megapixel Helios. 
However using pure Green for the 2nd Time Slice you can use the build in Chroma Green options.



## Disguise Designer Setup

### LED Surfaces
Skip this step if you're only using 1 video time slice from Disguise and using Green Chroma via the LED Prosessor.

Inside Disguise Designer we need to create LED screens for our LED Surfaces. In our stage that is 3 surfaces total. Right Wall, Left Wall, and Floor.
As we're outputting 2 video time slices we need to create two screens inside Disguise Designer for each suface. 

| LED Screen | GhostFrame Slice   | Disguise LED Screens|
|------------|--------------------|---------------------|
| Right Wall | GhostFrame Slice 1 | Right Wall GFS 1    |
| Right Wall | GhostFrame Slice 2 | Right Wall GFS 2    |
| Left Wall  | GhostFrame Slice 1 | Left Wall  GFS 1    |
| Left Wall  | GhostFrame Slice 2 | Left Wall GFS 2     |
| Floor      | GhostFrame Slice 1 | Floor GFS 1         |
| Floor      | GhostFrame Slice 2 | Floor GFS 2         |

### Feed Outputs
Once you've built your LED screens you must map them to your VFC outputs. Match this to your LED Prosessor setup. 
Image shows Two 4K outputs, 1 for each time slice. This output mapping will be diffrent if you're using Frame Remapping on Brompton prosessing. For details can be found [Here](https://help.disguise.one/workflows/xr/multi-frame-remapping.html) on the Disguise User Guide

IMAGE OF FEED MAPPING


### Camera
Add both your Primary Camera and Shadow Cameras as normal.

### GhostFrame / Frameremapping
It is critical at this point to verify and confirm that you see the correct Time Slice in each camera. Using Test Patters or colour solids to verify.

### MR set
Use the Normal XR Workflows for setup and calibration for the Primary Camera
The Shadow Camera can be positioned using the Spactial Calibration, but can also be manually placed into position.


### Projection plate
Create a large Projection Surface called **Shadow Plate**, move this projection surface to mostly in front of the camera and not behind. 
We rely on the Resoltion to be high enough so we don't see pixelation. Leaving lots of the surface behind the Primary camera is a waste of resorse.

Important settings for this layer:
Render layer: Backplate (MR)
Blend mode: Alpha
Screen background: Transparent


### Video Layer and Keying for the Shadow Camera

Create a video layer and set 
Media: Shadow Camera input
Mapping: [Next Section]
Keying: Set your Key Colour and Threshold to remove the Chroma Green. You should have a cutout of any object on your stage.
Colour Shift: We want to turn off colours to black. Simple change "Red minimum" to 1.
Crop: If your Shadow Camera is capuring past your LED Volume, use the crop to clean up the Key.

### Perspective Mapping
(You may already have a Perspecive mapping from the creation of the cameras) "Shadow Camera (Perspective)". Under Screens add your **Shadow Plate** Surface.

### StageRender Layer
Mapping: mr target (backplate)
Render layer: Backplate

### Blur layer and Gradient Mask Texture
You can use a blur layer to softern your shadows.
Add a Blur layer above your Shadow Camera Video layer and alt+click Video layer to Blur layer to arrow into the blur layer
Mapping for the Blur layer should be your Shadow Camera Persepective mapping.
If done correctally you will now be able to control the Blur of your shadow.
However shadows start off Sharp and become softer with distance. 
Using hte Mask texture inside the Blur layer we can create a gradient to control the intencisy of the blur. Depth Maps may also be used.

### Creating Gradient Texture for Shadow falloff.
White is more Blur
Black is less Blur

There should be no blur where the feet or objects rest on the ground. With a large LED floor you may need to change where this blur starts as walking around can give more or less blur. With my testing I set a gradient to only start blurring once the shadows are within 1m of the rear LED.

With a high blur value shadows have a nataral drop off in brightness. Masks can also be used for opacity when needed.


# Conclution
There you have it, Shadows with no tracking systems.

### Pros
- Low Latency
- No tracking
- Easy to adjust
- Content agnotic - works with 2D content all the way to Realtime UE
- Cost effective
- Simple Harware
- Easy to toubleshoot


### Cons
- Double Shadows
- Requires GhostFrame or FrameRemapping
- Limited 'lighting' posabilities





# Other posable workflow additons
- Using a 3D mesh on the Shadow Plate Projection layer to accurally repusent 3D enviorment. Is using a DepthMap Posable?
- Using multiple camera

  
