# 1. Introduction
Shadow recreation for Extended Reality (XR) productions has often relied on **motion capture (MoCap) systems** or **AI-generated skeletal data** to approximate shadow placement in a 3D environment using humanoid models. These methods can be **expensive**, require **extensive setup**, and are limited to **predefined tracked individuals**.

This project introduces a novel approach to **digital shadow reconstruction** using high-frame-rate LED screen technology, specifically **GhostFrame** or **Frame Remapping**. By utilising a camera with a **sensor sync offset** or a Genlock sync signal with **sync offset**, a different slice of time can be captured in the **Primary Camera** compared to the **Shadow Camera**, allowing us to:

- **Capture the virtual environment in the Primary Camera** using Disguise XR workflows.

- **Capture a green screen background in the Shadow Camera**, positioned at the location of the digital light source.

Unlike MoCap-based or AI-generated solutions, this method requires **no tracking system** and works with **any object**, making it significantly more **accessible, flexible**, and **cost-effective**.

This document outlines the process, **technical setup**, and **potential applications** of this technique, providing a foundation for further development and refinement.


# 2. Process

1. **Capture the virtual environment** in the Primary Camera using Disguise XR workflows (MRset).

2. **Capture a green screen background in the Shadow Camera**, positioned at the digital light source.

3. **Key out objects or people** captured in the Shadow Camera, resulting in a cutout-coloured image.

4. **Colour adjust the keyed footage to black**, creating a **ShadowPass**.

5. **Re-project (Perspective Mapping) the ShadowPass** from the Shadow Camera onto a digital floor plate.

6. **Render the digital floor plate** to the XR set (MRset) BackPlate Mapping.

7. **Use blur and gradient masking** to soften the ShadowPass as needed.



# 3. Technical Setup

## 3.1 XR Stage Setup 
### Disguise Hardware
- 2x VX 4+
- 2x RX III
### LED Screen
- [ROE RB1.9BV2](https://www.roevisual.com/en/products/ruby)
- [ROE BlackMarble](https://www.roevisual.com/en/products/black-marble-bm4)
### LED Processor
- [Megapixel Helios 8k (2x Displayport)](https://megapixelvr.com/helios/)
### Genlock Source / Sync Generator
- [Evertz 5601MSC Master Sync and Clock Generator](https://evertz.com/products/5601MSC)
### Camera and Lenses
- 2x RED KOMODO
- 1x 14mm (**Primary Camera**)
- 1x 18-55mm (**Shadow Camera**)

## 3.2 Time Offset
For this workflow you must be able to offset when your  **Shadow Camera** captures its image. This is possible in two main ways, a Sensor Offset setting built into the Camera or a Genlock Offset built into your Genlock Source / Sync Generator.
### Sensor Offset
A **Camera Sensor Offset** delays the moment when the camera starts capturing an image relative to the received Genlock sync signal. This shifts the exact moment in time the camera captures a frame.
### Genlock Offset
A **Genlock Offset** adjusts the synchronisation signal timing shared between devices (cameras, LED walls, etc.). This ensures proper synchronisation, with some devices introducing a slight delay per output to align frames correctly across systems.

**Setting Changed:** `Vertical Phase Offset` on the Evertz sync generator.

### Chosen setup
The **RED KOMODO** cameras have a built-in setting called `Sensor Sync Shift`, but it was found to be finicky, so **Evertz Genlock offsets** were used instead.
To simplify communication and documentation, the terminology **"Time Slice 1"** and **"Time Slice 2"** was used.
The following is what Offsets worked with our GhostFrame setup. These values are dependent on your own setup and requirements.


| **Camera**           | **Time Slice**   | **Vertical Phase Offset** |
|--------------------|--------------|-----------------------|
| **Primary Camera** | Time Slice 1 | 0                     |
| **Shadow Camera**  | Time Slice 2 | 440                   |




## 4. Multi-Frame LED Processor Mapping (GhostFrame vs. Frame Remapping)
Both **GhostFrame** and **Frame Remapping** enable multi-frame workflows for LED screens, but they operate differently: [More Info](https://help.disguise.one/workflows/xr/multi-frame-remapping.html)

### Chosen Setup
**GhostFrame** was used in this setup due to the studio using **Megapixel** processing. Two DisplayPort connections from the VX 4+ to the Megapixel Helios were utilised to display multiple background images simultaneously.



## 5. Disguise Designer Setup

### 5.1 LED Surfaces
If using 1 Video signal and applying the Green frame via your LED Processor, skip to 5.3.

If using two or more video time slices, create multiple LED screens per surface inside Disguise Designer.

| **LED Screen** | **GhostFrame Slice**   | **Disguise LED Screens**|
|------------|--------------------|---------------------|
| **Right Wall** | GhostFrame Slice 1 | Right Wall GFS 1    |
| **Right Wall**| GhostFrame Slice 2 | Right Wall GFS 2    |
| **Left Wall**  | GhostFrame Slice 1 | Left Wall  GFS 1    |
| **Left Wall** | GhostFrame Slice 2 | Left Wall GFS 2     |
| **Floor**     | GhostFrame Slice 1 | Floor GFS 1         |
| **Floor**      | GhostFrame Slice 2 | Floor GFS 2         |

### 5.2 Feed Outputs
Match your LED screens to your VFC outputs based on your LED Processor setup. [Details Here](https://help.disguise.one/workflows/xr/multi-frame-remapping.html)

IMAGE OF FEED MAPPING


### 5.3 Camera
Add both your **Primary Camera** and **Shadow Cameras** as normal.

### 5.4 GhostFrame / Frame Remapping
It is critical at this point to verify and confirm that you see the correct Time Slice in each camera. Use Test Pattern or colour solids to verify there is no mixing of time slices.

### 5.5 MR set
Follow standard XR workflows for **Primary Camera** setup.
Position the **Shadow Camera** manually or via Spatial Calibration.


### 5.6 Projection plate
Create a large Projection Surface called **Shadow Plate**, rotate and move this projection surface flat to the floor and in front of the camera. 

Important settings for this layer:
Render layer: Backplate (MR)
Blend mode: Alpha
Screen background: Transparent


### 5.7 Video Layer and Keying

Create a video layer and set 
Media: **Shadow Camera** input
Mapping: [Next Section]
Keying: Set your Key Colour and Threshold to remove the Chroma Green. You should have a cutout of any object on your stage.
Colour Shift: We want to turn off colours to black. Simple change "Red minimum" to 1.
Crop: If your **Shadow Camera** is capturing past your LED Volume, use the crop to clean up the Key.

### 5.8 Perspective Mapping
Create a Perspective mapping - Ensure **Shadow Plate** is added to **Shadow Camera** (Perspective).

### 5.9 StageRender Layer
Mapping: mr target (backplate)

Render layer: Backplate


### 5.10 Blur layer and Gradient Mask Texture
Use a blur layer to soften your shadows.
Add a Blur layer above your Shadow Camera Video layer and alt+click Video layer to Blur layer to arrow into the blur layer
Mapping for the Blur layer should be your **Shadow Camera** Perspective mapping.
Done correctly you will now be able to control the Blur of your shadow.
Shadows start off Sharp and become softer with distance. 
Using the Mask texture inside the Blur layer we can create a gradient to control the intensity of the blur. Depth Maps may also be used.

### 5.11 Creating Gradient Texture for Shadow falloff.
White is more Blur

Black is less Blur



There should be no blur where the feet or objects rest on the ground. With a large LED floor you may need to change where this blur starts as walking around can give more or less blur. With my testing I set a gradient to only start blurring once the shadows are within 1m of the rear LED.

With a high blur value shadows have a natural drop off in brightness. Masks can also be used for opacity when needed.


# 6. Conclusion
A workflow enables real-time digital shadows with no tracking systems.

### Pros
- Low Latency
- No tracking
- Easy to adjust
- Content agnostic - works with 2D content all the way to Realtime UE
- Cost effective
- Simple Hardware
- Easy to troubleshoot


### Cons
- Double Shadows
- Requires GhostFrame or FrameRemapping.
- Limited 'lighting' possibilities.
- Objects or persons must be in front of LED and in shot of the **Shadow Camera**.





# 7. Other possible workflow additions
- 3D mesh projection on **Shadow Plate** (Depth Map possibilities?)
- Multi-camera setups for complex shadows.

  


