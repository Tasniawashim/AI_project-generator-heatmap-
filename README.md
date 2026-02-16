#🚶‍♂️ Object Counting and Heatmap Generation for People Flow

This project leverages **Computer Vision** techniques — specifically **YOLOv8** and **ByteTrack** — to **detect, track, and count pedestrian movement** across predefined virtual lines.  
It also generates a **heatmap** visualizing traffic density and identifying regions with the highest pedestrian activity.

---

## 🧠 1. Detection and Tracking Method

The system follows a **two-stage pipeline** to ensure accurate object detection and reliable multi-person tracking.

### 🔹 Detection – YOLOv8
- **Model Used:** `YOLOv8n (yolov8n.pt)` – a lightweight, high-speed object detection model.
- **Target Class:** The model is configured to detect only the **“person”** class (COCO class ID: `0`).
- **Purpose:** Detects all pedestrians present in each video frame.

### 🔹 Tracking – ByteTrack
- **Algorithm:** ByteTrack assigns a **unique `tracker_id`** to each detected person to maintain consistent identity across frames.
- **Purpose:** Prevents multiple counts of the same individual and ensures continuity in tracking as people move through the frame.

---

## 📏 2. Line Coordinates and Setup

Two **horizontal virtual lines** are drawn across a standard `1280 × 720` video frame to monitor pedestrian flow direction and counting.

| **Line**           | **Purpose**            | **Y-Coordinate** | **Coordinates (Start → End)** | **Color** |
|--------------------|-----------------------|------------------|-------------------------------|-----------|
| **Line 1 (Upper)** | IN Counter Trigger    | Y = 250          | (0, 250) → (1280, 250)        | Blue      |
| **Line 2 (Lower)** | OUT Counter Trigger   | Y = 550          | (0, 550) → (1280, 550)        | Red       |

### 🎯 Triggering Anchors
The `LineZone` module monitors **all four corners** of each bounding box:
> **TOP_LEFT**, **TOP_RIGHT**, **BOTTOM_LEFT**, and **BOTTOM_RIGHT**

A count is triggered when **any of these anchor points** crosses one of the virtual lines.

---

## 🔄 3. IN/OUT Counting Logic

Directional counting is handled by `supervision.LineZone`, which analyzes the **movement direction** of every unique `tracker_id`.

| **Counter Type** | **Monitored Line** | **Movement Direction** | **Description** |
|------------------|--------------------|-------------------------|------------------|
| **IN Count**     | Line 1 (Upper)     | **Downward**            | Registers an **entry** when a tracked person crosses Line 1 from above to below. |
| **OUT Count**    | Line 2 (Lower)     | **Upward**              | Registers an **exit** when a tracked person crosses Line 2 from below to above. |

---

## 🌡️ 4. Heatmap Generation

A **final heatmap (`final_heatmap.png`)** is produced to visualize areas of high pedestrian density and movement concentration.

- The **center point (`sv.Position.CENTER`)** of each detected person is recorded per frame.  
- The **`HeatMapAnnotator`** aggregates these points over time to map traffic density.  
- The final heatmap uses color gradients:
  - 🔥 **Hot Colors (Red/Yellow):** Indicate zones with **high pedestrian traffic** or **long dwell times**.  
  - 🟢 **Cool Colors (Blue/Green):** Represent areas with **low activity**.

---

## 🧾 Summary

This project integrates:
- ✅ **YOLOv8** for real-time pedestrian detection  
- ✅ **ByteTrack** for robust object tracking  
- ✅ **LineZone-based directional counting**  
- ✅ **Heatmap visualization** for spatial traffic analysis  

Together, these modules create a **comprehensive analytics tool** for understanding **pedestrian flow, crowd management, and urban mobility**.

---

## Tech Stack

| Component | Description |
|------------|-------------|
| **Language** | Python |
| **Frameworks/Libraries** | Ultralytics YOLOv8, ByteTrack, Supervision, OpenCV, NumPy |
| **Output** | Live count display + Final heatmap image (`final_heatmap.png`) |

