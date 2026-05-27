# Feature Detection using OpenCV — AR Video Overlay

A **computer vision** project that detects a target image in a live webcam feed using **ORB feature matching**, then overlays a video onto the detected surface with **perspective transformation** — an augmented-reality-style effect built entirely with **OpenCV** and **NumPy**.

[![OpenCV](https://img.shields.io/badge/OpenCV-4.x-green?logo=opencv)](https://opencv.org)
[![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python)](https://www.python.org)
[![NumPy](https://img.shields.io/badge/NumPy-latest-orange?logo=numpy)](https://numpy.org)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-red?logo=jupyter)](https://jupyter.org)

---

## Overview

This project demonstrates **real-time feature detection and homography-based video overlay**. A reference image (`TargetImage.png`) is scanned for ORB keypoints. When the webcam sees that same image (printed or on screen), the algorithm matches features, computes a homography matrix, and warps a video (`video.mp4`) onto the detected plane — creating a live AR effect.

**Why this project matters:** It covers core computer vision concepts — keypoint detection, descriptor matching, Lowe's ratio test, RANSAC homography, perspective warping, and mask compositing — in a single interactive notebook.

---

## How It Works

```text
┌──────────────┐     ORB detect      ┌──────────────┐
│ TargetImage  │ ──────────────────► │  Keypoints   │
│   .png       │                     │  + Descriptors│
└──────────────┘                     └──────┬───────┘
                                          │ BFMatcher + ratio test
┌──────────────┐     ORB detect          │
│   Webcam     │ ──────────────────► ────┘
│  (live feed) │          │
└──────────────┘          ▼
                   > 20 good matches?
                          │
                          ▼ Yes
              ┌───────────────────────┐
              │ findHomography (RANSAC)│
              │ perspectiveTransform   │
              │ warpPerspective (video)│
              │ mask + bitwise blend   │
              └───────────────────────┘
                          │
                          ▼
              ┌───────────────────────┐
              │  6-panel debug view   │
              │  webcam · video · target│
              │  matches · warp · AR  │
              └───────────────────────┘
```

### Pipeline steps

| Step | OpenCV function | Purpose |
|------|-----------------|---------|
| 1 | `cv2.ORB_create()` | Detect keypoints & descriptors on target + webcam |
| 2 | `BFMatcher.knnMatch()` | Match descriptors between target and frame |
| 3 | Ratio test (`0.75`) | Filter good matches (Lowe's criterion) |
| 4 | `cv2.findHomography()` | Compute transformation matrix (RANSAC) |
| 5 | `cv2.perspectiveTransform()` | Map target corners to webcam space |
| 6 | `cv2.warpPerspective()` | Warp video onto detected surface |
| 7 | Mask compositing | Blend warped video with webcam background |

---

## Features

- **ORB feature detection** — 1000 keypoints on target image
- **Brute-force KNN matching** — with Lowe's ratio test (threshold 0.75)
- **Homography estimation** — RANSAC for robust plane detection
- **AR video overlay** — warps `video.mp4` onto the detected target in real time
- **Detection threshold** — activates overlay when > 20 good matches found
- **6-panel debug view** — stacked visualization of all processing stages
- **Auto-loop video** — resets video playback when detection is lost

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| Language | Python 3.11 |
| Computer Vision | OpenCV (`cv2`) |
| Numerical | NumPy |
| Environment | Jupyter Notebook |
| Input | Webcam + static target image + video file |

---

## Project Files

| File | Description |
|------|-------------|
| `FeatureDetection.ipynb` | Main notebook — full detection + overlay pipeline |
| `TargetImage.png` | Reference image to detect in the webcam feed |
| `video.mp4` | Video content overlaid when target is found |

---

## Prerequisites

| Tool | Version | Check |
|------|---------|-------|
| **Python** | 3.8+ (3.11 recommended) | `python --version` |
| **pip** | Latest | `pip --version` |
| **Webcam** | Required | Built-in or USB camera |
| **Jupyter** | Latest | `jupyter --version` |

---

## How to Run

### Step 1 — Clone the repository

```bash
git clone https://github.com/ahmad-alhalwany/Feature-Detection-using-openCV.git
cd Feature-Detection-using-openCV
```

### Step 2 — Create a virtual environment (recommended)

```bash
python -m venv venv

# Windows
venv\Scripts\activate

# macOS / Linux
source venv/bin/activate
```

### Step 3 — Install dependencies

```bash
pip install opencv-python opencv-contrib-python numpy jupyter ipykernel
```

> `opencv-contrib-python` includes ORB and extended matching modules.

### Step 4 — Launch Jupyter Notebook

```bash
jupyter notebook FeatureDetection.ipynb
```

Or with JupyterLab:

```bash
jupyter lab FeatureDetection.ipynb
```

### Step 5 — Run the notebook

1. Open `FeatureDetection.ipynb` in the browser
2. Connect your **webcam**
3. Display or print `TargetImage.png` in front of the camera
4. Run the code cell (**Shift + Enter**)
5. Point the webcam at the target image
6. When > 20 feature matches are found, the video overlay activates

### Step 6 — Stop the program

Press **`Q`** in the OpenCV window, or use the **Interrupt** button in Jupyter (■).

---

## Expected Output

The notebook opens a window titled **`imgStacked`** showing a 2×3 grid:

| Row 1 | Webcam feed | Video frame | Target image |
|-------|-------------|-------------|--------------|
| Row 2 | Feature matches | Warped video | Final AR composite |

- Before detection: only the webcam and match visualization are active
- After detection (> 20 matches): video is warped and blended onto the target area

---

## Configuration

You can tune these values in the notebook:

```python
orb = cv2.ORB_create(nfeatures=1000)   # number of keypoints
# Ratio test threshold
if m.distance < 0.75 * n.distance:     # Lowe's ratio (default 0.75)
# Detection trigger
if len(good) > 20:                      # minimum matches to activate overlay
# Webcam index
cap = cv2.VideoCapture(0)              # change to 1 if external camera
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `NameError: name 'bf' is not defined` | Add `bf = cv2.BFMatcher()` before the while loop |
| Webcam not opening | Change `VideoCapture(0)` to `VideoCapture(1)` |
| No detection / overlay never activates | Ensure good lighting; hold `TargetImage.png` flat and fully visible |
| `ModuleNotFoundError: cv2` | Run `pip install opencv-python opencv-contrib-python` |
| Window freezes in Jupyter | OpenCV GUI works best when run as `.py` script, not always in notebook |
| Video not playing | Check `video.mp4` exists in the same directory as the notebook |
| Too many false matches | Increase threshold from `20` to `30+` or lower ORB features |

### Missing BFMatcher fix

Add this line after creating the ORB detector:

```python
bf = cv2.BFMatcher()
```

---

## Run as Python Script (alternative)

Extract the notebook cell to `main.py` and run:

```bash
python main.py
```

This avoids Jupyter GUI issues on some systems.

---

## Screenshots

| Target Detection | Feature Matches | AR Overlay |
|------------------|-----------------|-----------|
| _Add screenshot_ | _Add screenshot_ | _Add screenshot_ |

---

## Concepts Demonstrated

- **ORB** (Oriented FAST and Rotated BRIEF) — fast binary feature detector
- **Descriptor matching** — finding corresponding points between two images
- **Lowe's ratio test** — filtering ambiguous matches
- **Homography** — 3×3 matrix mapping one plane to another
- **RANSAC** — robust estimation ignoring outlier matches
- **Perspective warping** — projecting video onto a detected surface
- **Alpha compositing** — blending overlay with live camera feed

---

## Roadmap

- [ ] Add missing `BFMatcher` initialization to notebook
- [ ] Add `requirements.txt` for reproducible setup
- [ ] Support multiple target images
- [ ] Save output video to file (`cv2.VideoWriter`)
- [ ] Convert notebook to standalone `.py` script
- [ ] Add SIFT/AKAZE comparison mode

---

## Author

**Ahmad Alhalwany**

- GitHub: [@ahmad-alhalwany](https://github.com/ahmad-alhalwany)
- Repository: [Feature-Detection-using-openCV](https://github.com/ahmad-alhalwany/Feature-Detection-using-openCV)

---

## License

MIT — free to use for learning and reference.
