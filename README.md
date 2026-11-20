# IE415: Visual-Inertial SLAM with Fiducial Landmark Anchors

Indoor localization and mapping pipeline for **IE415: Control of Autonomous Systems**. This system estimates the 6-DoF trajectory of a handheld smartphone moving through an indoor environment, constructs and maintains a persistent map of fixed ArUco fiducial markers, and fuses visual-inertial motion estimation with landmark constraints via factor-graph optimization.

---

## System Architecture

This project implements **Architecture C: Phone–Laptop Hybrid**:

1. **Sensing Platform (Smartphone):** Records synchronized RGB video frames, high-rate IMU measurements (accelerometer and gyroscope), and nanosecond timestamps.
2. **Processing & Visualization (Laptop):** Runs camera calibration, visual odometry / inertial propagation, ArUco marker detection, GTSAM factor-graph optimization, quantitative evaluation, and an interactive web dashboard.

Separating data acquisition from estimation allows recorded datasets to be replayed deterministically for tuning, debugging, and running baseline-versus-anchored comparisons.

---

## Tech Stack

| Component | Tool / Library | Purpose |
| :--- | :--- | :--- |
| **Mobile Data Logger** | OpenCamera-Sensors (Android) / MiSensorKit (iOS) | Synchronized video + raw IMU capture with timestamps |
| **Calibration & Vision** | Python, OpenCV (`opencv-contrib-python`) | Camera intrinsics, feature tracking / VO, ArUco detection (`solvePnP`) |
| **SLAM Backend** | GTSAM 4.3 (`gtsam`) | Factor-graph optimization (IMU preintegration, odometry, ArUco landmark factors) |
| **User Interface** | Streamlit + Plotly | Dataset loading, pipeline execution, 3D trajectory & map visualization, diagnostics |
| **Numerics & Eval** | NumPy, SciPy | Rigid-body transforms, error metrics, and baseline comparisons |

---

## Repository Structure

```text
ie415-vi-slam-aruco/
├── data/
│   ├── raw/                # Recorded smartphone video & IMU logs (git-ignored)
│   └── processed/          # Synchronized streams, calibration YAML, saved maps
├── docs/
│   └── M1_Proposal.md      # M1 Proposal, system design, and technical reports
├── src/
│   ├── calibration/        # Camera intrinsic calibration & camera-IMU alignment
│   ├── frontend/           # ArUco detection, solvePnP, visual odometry
│   ├── backend/            # GTSAM factor-graph SLAM & map management
│   ├── eval/               # Baseline vs. ArUco-anchored error evaluation
│   └── ui/
│       └── app.py          # Streamlit + Plotly interactive dashboard
├── requirements.txt
└── README.md
```

---

## Setup & Installation

### Linux / macOS (`venv`)
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

### Windows (`conda`)
Use `conda-forge` for `gtsam` to avoid C++ build issues:
```bash
conda create -n vi_slam python=3.11 -y
conda activate vi_slam
conda install -c conda-forge gtsam -y
pip install opencv-contrib-python numpy scipy streamlit plotly
```

### Verify Installation
```bash
python -c "import cv2, gtsam, streamlit, plotly; print('Environment ready!')"
```

---

## Workflow & Usage

1. **Record Dataset:** Capture synchronized video + IMU data on the smartphone and transfer the files into `data/raw/<experiment_name>/`.
2. **Calibrate Camera:** Run the intrinsic calibration script in `src/calibration/` using a checkerboard or ChArUco board to generate camera intrinsics and distortion parameters.
3. **Launch Dashboard:**
   ```bash
   streamlit run src/ui/app.py
   ```
4. **Run & Evaluate:** Load a dataset in the UI, toggle between **VO/VIO-only (baseline)** and **ArUco-anchored SLAM**, inspect the 3D trajectory and marker map, and export error metrics.

---

## Project Milestones

- [ ] **M1:** Problem formulation, Architecture C selection, stack setup, initial system diagram
- [ ] **M2:** Synchronized phone camera + IMU acquisition, timestamping, camera calibration
- [ ] **M3:** Reliable ArUco detection and camera-to-marker pose estimation ($^{C_k}T_{M_i}$)
- [ ] **M4:** Continuous VO/VIO trajectory estimation without marker correction (baseline)
- [ ] **M5:** Marker-map construction, repeated landmark observation, and 3D visualization
- [ ] **M6:** GTSAM drift correction, loop closure, relocalization, and baseline comparison
- [ ] **M7:** Full 5-scenario experiments, quantitative evaluation, UI polish, report, and demo

---

## Team
* **Member 1:** `Samay Kharidia` 
* **Member 2:** `Bhagwati Prince Vishnu`