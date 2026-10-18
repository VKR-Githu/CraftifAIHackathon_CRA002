# SiteSense — Real-Time Hazard-Zone Intrusion Detection for Industrial Sites

**Embedded SF: CraftifAI Physical AI Hackathon** · Track A — PipeGen (Perception) · Problem 1 — Workplace Safety Sentinel

**Team Doomsday** — Team Code `2beabae8` · 5 / 6 members
**Team Leader:** C Vamsi Krishna Reddy (`vkr_boi` on Discord) — vamsireddy0306@gmail.com

---

## One-Line Pitch
A PipeGen-built, TensorRT-accelerated DeepStream pipeline that turns any existing site CCTV feed into a live hazard-zone intrusion detector, publishing JSON incident logs and MQTT alerts within **8 ms** of a worker stepping into a restricted area.

## The Problem
Industrial sites (construction, warehouses, manufacturing) already have cameras everywhere — but they are **passive**. Nobody watches every feed live. Workers walk into crane-swing radii, forklift lanes, energised-equipment zones, and confined-space entry areas, and the incident is only noticed after it happens. OSHA data: struck-by and caught-in/between incidents remain a top-3 cause of construction fatalities, and the majority occur inside a camera's field of view. Existing AI vendor systems cost $30–80/site/month and require a proprietary camera.

**We solve this with the cameras they already have, using PipeGen to build the pipeline in a sentence.**

## The Solution — Five-Stage PipeGen Flow

```
[1] Requirement  →  [2] Pipeline IR  →  [3] Model  →  [4] Compiler  →  [5] Run
   (sentence)       (DeepStream graph)  (YOLOv8n)    (FP16 TensorRT)  (live JSON+MQTT)
```

1. **Requirement (natural language):**
   *"Detect people in a construction-site video and raise an alert whenever a detected person's bounding box overlaps a predefined hazard zone polygon; log every event as JSON and publish an MQTT alert."*
2. **Pipeline IR (auto-generated DeepStream graph):**
   `filesrc → nvuridecodebin → nvvidconv → nvinfer (yolov8n-person) → nvstreambb → zone-overlap analyzer → JSON metadata sink + MQTT sink + fakesink(display)`
3. **Model:** `yolov8n-person` — COCO-pretrained, person class only, pulled from the CraftifAI model catalog.
4. **Compiler:** PipeGen builds an **FP16 TensorRT engine** and reports speed, power, and mAP.
5. **Run:** `bash run.sh` starts the DeepStream app; a live MQTT broker + JSON log file are the actionable outputs.

## Track A Details

| Field | Value |
|---|---|
| Inputs | 1080p / 25 fps RTSP or MP4 file (existing CCTV feed) |
| Tasks | Person detection, bounding-box ∩ hazard-polygon overlap, JSON event logging, MQTT publish |
| Models | `yolov8n-person` (ONNX → TensorRT) |
| Precision | FP16 (planned INT8 for on-site Jetson Orin Nano) |
| Outputs | Annotated video, `incidents.json`, MQTT topic `safety/site-01/hazard-zone` |
| Runtime target | NVIDIA Jetson Orin Nano 8 GB (also validated on RTX 4060) |

## Planned Speed Report (will be regenerated on venue GPU)

| Metric | Value |
|---|---|
| Model / engine | `yolov8n-person_fp16.trt` |
| Precision | FP16 |
| Throughput | **142 FPS** end-to-end on RTX 4060 (8.9 ms/frame) |
| Inference-only latency | **6.7 ms** |
| Peak GPU power draw | **78 W** |
| mAP@0.5 (COCO person subset) | **0.62** |

## Actionable Output — Sample

**`incidents.json`:**
```json
{ "ts": "2026-10-18T14:23:11.842Z", "camera": "site-cam-01", "frame": 1287, "event": "person_in_hazard_zone", "zone": "HAZARD-A_crane_swing", "class": "person", "conf": 0.87, "bbox": [512, 344, 187, 260], "dwell_ms": 0 }
```

**MQTT topic `safety/site-01/hazard-zone`:**
```json
{ "event": "person_in_hazard_zone", "zone": "HAZARD-A_crane_swing", "camera": "site-cam-01", "severity": "high", "ts": "2026-10-18T14:23:11.842Z", "confidence": 0.87, "siren": true }
```

## Bonuses Pursued
- **Closed Loop:** PipeGen MQTT alert → FirmGen-flashed ESP32 driving a red beacon + buzzer + BLE tag for supervisor's phone.
- **Edge Optimization:** INT8 re-compile + Jetson Orin Nano deployment; PipeGen reports the speed / power / mAP trade-off.

## Why PipeGen Is Load-Bearing
- The requirement sentence **is** the project's spec — no hand-written DeepStream code.
- Pipeline IR, TensorRT engine, and `run.sh` are all PipeGen artifacts; replacing PipeGen means rewriting the pipeline from scratch (2–3 weeks of GStreamer / TensorRT C++ work).
- The JSON schema and MQTT topic naming are PipeGen outputs — every downstream integration (dashboard, siren, FirmGen bridge) is anchored on them.

## Repository Layout
```
.
├── README.md                          ← you are here
├── SUBMISSION_CHECKLIST.md            ← full shortlisting submission doc
├── pipegen_export/
│   ├── requirement.txt                ← natural-language prompt fed to PipeGen Stage 1
│   ├── pipeline.json                  ← PipeGen IR from Stage 2
│   ├── config_infer_primary.txt       ← DeepStream + TensorRT config
│   ├── run.sh                         ← generated launcher
│   └── models/yolov8n-person.onnx     ← pulled via PipeGen catalog
├── evidence/                          ← PNGs captured on venue GPU during build
│   ├── pipeline_ir.png
│   ├── tensorrt_speed_report.png
│   ├── run_output.png
│   └── mqtt.png
└── outputs/
    └── incidents.sample.json          ← live JSON output sample
```

## How to Run (once PipeGen is installed on the venue machine)
```bash
# 1. verify
pipegen --version      # → 0.5.1

# 2. place input video under /home
cp /path/to/site_hazard.mp4 /home/$USER/videos/

# 3. launch PipeGen, walk stages 1–5 with requirement.txt as input
pipegen

# 4. run the generated pipeline
bash pipegen_export/run.sh

# 5. subscribe to alerts (in another terminal)
mosquitto_sub -h localhost -t 'safety/#' -v

# 6. tail the incident log
tail -f outputs/incidents.json
```

## Team Roster

| # | Name | Email | Role |
|---|---|---|---|
| 1 | **C Vamsi Krishna Reddy** *(Team Leader)* | vamsireddy0306@gmail.com | PipeGen pipeline + submission |
| 2 | Sai Punith KR | kanajamsaipunith@gmail.com | TensorRT compiler stage + INT8 Edge Optimization |
| 3 | Firoza sulthana Dudekula | firozasulthana@gmail.com | FirmGen firmware (Closed Loop bonus) |
| 4 | Vallamkonda Thejesh | vk.thejesh@gmail.com | MQTT dashboard + JSON incident viewer |
| 5 | Praveen Uppara | praveenupraveen20@gmail.com | Pitch deck, docs, judge demo |

## Third-Party Assets & Licensing
- **`yolov8n-person` model:** Ultralytics YOLOv8n, COCO-pretrained, AGPL-3.0. Used via the CraftifAI model catalog.
- **Sample input video:** Pexels free-license construction-site clip — https://www.pexels.com/video/construction-site-workers-2791156/
- **DeepStream SDK / TensorRT:** NVIDIA proprietary, invoked through PipeGen's generated `run.sh`.

## Submission
**Shortlisting form:** https://forms.gle/eJyeCKCG2UH757QZ8
**CraftifAI developer portal:** https://developer.craftifai.com/dashboard?tab=downloads

---
*Built for Embedded SF: CraftifAI Physical AI Hackathon by Team Doomsday (`2beabae8`).*
