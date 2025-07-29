# META – Digital Forensics Lab 

## Scenario

> "I'm roaming free, you will never catch me."

A criminal on the run posts two images online with the taunting caption above. Your mission as a digital forensic analyst is to extract hidden clues from the images that could help 
track the suspect's identity, location, and activity.

By working on this lab, the focus was on **metadata analysis**, **image forensics**, and **open-source intelligence (OSINT)**.

---
NOTE : It is not intentional not to add screenshots but I wanted to document just after I finished. I will add the screenshots soon enough.
---

## Objectives

- Identify the **camera model** used.
- Determine the **date and time** the picture was taken.
- Extract **embedded comments** from the image.
- Locate **where the image was taken**.

## Tools I Used

- `file` (Linux command)
- exiftool
- Google Image Search (OSINT)
- Google Maps

## Investigation Process

### Step 1: Extracting the Lab Files
I downloaded and unzipped a file containing **two images**. I analyzed each of the images individually.
- uploaded_1.jpg
- uploaded_2.png

### Step 2: Basic Image Information
Used the `file` command to get basic image info such as format and encoding:
```
file unploaded_1.jpg
file uploaded_2.png
```
This confirmed that the image was a standard JPEG & PNG file, ready for metadata extraction.

### Step 3: Metadata Extraction with ExifTool
I ran exiftool on the image to extract metadata.
```
exiftool unploaded_1.jpg
exiftool uploaded_2.png
```
From the output, I gathered the camera model, date Taken and the funny comment the criminal made. The comment was funny LMAO. I know I'm documenting this but allow me to exppress my emotions haha.

### Step 4: Locating the Criminal (Geo-Intelligence)
While exiftool revealed GPS coordinates (latitude and longitude), I took an alternative route.
Instead of parsing the coordinates manually, I reverse-searched the image using Google Image Search.

- Found visually similar images.

- Identified the exact place: It was a palace square of a particular city.

- I recorded the name of the City.

## Reflection
Exif metadata can reveal surprising amounts of information if you sure to check fields like GPS, comments, and timestamps. 
OSINT tools like Google Image Search are invaluable in narrowing down location without needing advanced geospatial tools.
Always check multiple layers: metadata and image content (I didnt think of opening the image at first but when I did, the google image search idea came to mind) for good footprinting.

