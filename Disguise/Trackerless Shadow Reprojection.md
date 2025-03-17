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
- ROE Ruby 1.9
### LED Prosessor
- Megapixel Helios 8k (2x Displayport)
### Genlock Source / Sync Generator
- Evertz 5601MSC Master Sync and Clock Generator
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


| Camera           | Time Slice   | Vertical Phase Offset |
|------------------|--------------|-----------------------|
| **Primary Camera** | Time Slice 1 | 0                     |
| **Shadow Camera**  | Time Slice 2 | 440                   |




## Multi Frame LED Processor Mapping
Both GhostFrame and Frame Remapping are innovative solutions designed to enhance camera and high framerate workflows involving LED screens, but they currently operate through distinct methodologies.

### Chosen Setup
GhostFrame was used in this setup due to our studio haveing Megapixal prosessing. As we demonstrate multiple background images at once we have 2x Display connections from our VX 4+ to Megapixel Helios. 
However using pure Green for the 2nd Time Slice you can use the build in Chroma Green options.



# WIP

## Disguise Designer Setup




- **Capture the Virtual Enviroment in the Primary Camera** using Disguise XR workflows. 
- **Capture a Green Screen background in the Shadow Camera** [[ from the location of the light source]]
- **Key any object or persons** captured in the Shadow Camera. Resulting in a cutout coloured Image.
- **Coloud Adjust this keyed footage to Black** This can be called our **ShadowPass**
- **Re-project (Perspective Mapping) the ShadowPass** from the perspective of the **Shadow Camera**. 
- Shadows can travel a long distance, if the **ShadowPass** requires going a further distance than your LED floor a Digital floor plate will be requiered inside Disguise Designer.

# Potential Applications
  








Shadow recreation techniques in Virtual Production and VFX often rely on **motion capture (MoCap) systems** or **AI-generated skeletal meshes** to approximate shadow placement in a 3D environment. These methods can be **expensive**, require extensive **setup**, and are limited to **predefined tracked objects**.

This project introduces a novel approach to **digital shadow reconstruction** using high-frame-rate LED screen technology, specifically **GhostFrame** or **FrameRemapping**. By utilizing a camera with a **sensor sync offset**, we capture a different time slice than the primary camera, allowing us to:

- **Remove the object** from the LED screen using greenscreen techniques.
- **Re-project the shadow** from the captured offset frame, ensuring it aligns naturally with the primary camera’s perspective.

Unlike MoCap-based or AI-generated solutions, this method requires **no tracking system** and works with **any object**, making it significantly more **accessible**, **flexible**, and **cost-effective**.

This document outlines the **process**, **technical setup**, and **potential applications** of this technique, providing a foundation for further development and refinement.
