# Nut Detection, Tracking & Counting System

A classical computer vision system for detecting, separating, tracking, and counting metallic nuts moving on a conveyor belt.

The project was developed using **Python** and **OpenCV** without relying on deep learning object detectors. It combines image segmentation, Watershed-based object separation, motion-aware tracking, Hungarian assignment, and line-crossing logic to build a complete video-processing pipeline.

---

## Project Overview

The objective of this project is to automatically count metallic nuts moving on a conveyor from video.

The main challenge is not only detecting the nuts, but also handling cases where multiple nuts touch or overlap visually. A simple contour-based approach can merge nearby objects into a single detection, resulting in incorrect counting.

To address this, the system uses a multi-stage classical computer vision pipeline that separates touching objects before tracking them across frames.

---

## Processing Pipeline

The system processes each video frame through the following stages:

1. Frame preprocessing
2. HSV color segmentation
3. Morphological noise removal
4. Hole filling
5. Distance Transform
6. Local peak detection
7. Watershed segmentation
8. Object centroid extraction
9. Motion-aware object tracking
10. Hungarian one-to-one assignment
11. Line-crossing detection
12. Unique object counting

This pipeline allows the system to detect individual nuts, maintain their identities across consecutive frames, and count each object only when it crosses the defined counting line.

---

## Detection & Segmentation

### HSV Segmentation

The conveyor and metallic nuts have different visual characteristics, allowing the objects to be isolated using HSV-based thresholding.

HSV color space provides more practical control over color-based segmentation than directly thresholding RGB values.

### Morphological Processing

Morphological operations are applied to reduce small segmentation noise and improve the object masks before further processing.

### Hole Filling

Because nuts naturally contain a central hole, the binary mask may represent one physical nut as a ring-shaped region.

Hole filling is applied before distance-based segmentation to produce a more stable representation of each object.

---

## Separating Touching Nuts

One of the main challenges in the project occurs when two or more nuts are touching.

Using only connected components or contours may incorrectly interpret touching nuts as a single object.

To solve this problem, the system combines:

- Distance Transform
- Local peak detection
- Marker generation
- Watershed segmentation

The Distance Transform estimates the distance of each foreground pixel from the nearest background region.

Local maxima are then used as markers representing likely individual object centers.

These markers are passed to the **Watershed algorithm**, which separates connected groups into individual nut detections.

---

## Object Tracking

After detecting the nuts in each frame, the system tracks them across the video.

Instead of matching detections using only the nearest current position, the tracker uses previous motion information to predict the expected next position of each object.

A cost matrix is generated between predicted track positions and current detections.

The **Hungarian assignment algorithm** is then used to obtain a one-to-one matching between existing tracks and new detections.

This reduces incorrect associations and provides more stable tracking when multiple objects are moving close to each other.

---

## Counting Logic

A virtual counting line is defined across the conveyor.

Each tracked nut is counted only when its trajectory crosses this line in the expected direction.

The tracking IDs prevent the same nut from being counted multiple times while it remains visible in the video.

---

## Engineering Iteration

During validation, the initial counting logic assumed the wrong conveyor movement direction.

After inspecting the tracking behavior and video sequence, the actual motion direction was identified and the line-crossing condition was corrected.

The final crossing condition checks for movement from below the counting line to above it:

```python
old_y > LINE_Y and new_y <= LINE_Y
```

This correction highlights an important part of the project development process: validating assumptions against the real video rather than relying only on the initial implementation.

---

## Final Result

The final pipeline detected, separated, tracked, and counted the nuts throughout the validation video.

The system produced a final count of:

**219 nuts**

This value represents the output of the implemented counting pipeline.

A manually verified ground-truth count is required before reporting a formal counting accuracy. Therefore, the project does not claim an accuracy percentage based only on the algorithm's final count.

---

## Technologies & Methods

- Python
- OpenCV
- NumPy
- SciPy
- Classical Computer Vision
- HSV Color Segmentation
- Morphological Image Processing
- Distance Transform
- Watershed Segmentation
- Motion Prediction
- Hungarian Assignment Algorithm
- Object Tracking
- Line-Crossing Counting

---

## Key Challenges & Solutions

| Challenge | Solution |
|---|---|
| Separating metallic nuts from the conveyor | HSV-based segmentation |
| Small mask noise | Morphological processing |
| Holes inside the nuts | Hole filling |
| Touching nuts detected as one object | Distance Transform + Watershed |
| Maintaining object identity | Motion-aware tracking |
| Matching multiple tracks and detections | Hungarian assignment |
| Preventing duplicate counts | Track IDs + line-crossing logic |
| Incorrect initial motion assumption | Validation and crossing-direction correction |

---

## Repository Structure

```text
nut-detection-tracking-counting/
│
├── notebook/
│   └── Nut_Counting_Project_FINAL.ipynb
│
├── videos/
│   └── nut_counting_validation.mp4
│
├── assets/
│   └── Project preview images
│
├── README.md
├── requirements.txt
└── .gitignore
```

---

## Validation

The project includes an annotated validation video showing the final detection, tracking, and counting pipeline.

The displayed count represents the number of tracked nuts that satisfy the defined line-crossing condition.

For a complete quantitative evaluation, the predicted count should be compared against a manually verified ground-truth count.

---

## Project Scope

This project demonstrates the implementation of an end-to-end **classical computer vision pipeline** for an industrial conveyor application.

It focuses on practical challenges such as object segmentation, separation of touching objects, temporal tracking, data association, counting logic, and iterative validation without relying on a pretrained deep learning detector.
