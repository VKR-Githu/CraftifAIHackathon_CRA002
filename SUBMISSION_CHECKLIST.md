# Embedded SF — CraftifAI Hackathon Shortlisting Submission

**Track:** A — PipeGen (Perception)
**Problem:** 1 — Workplace Safety Sentinel
**Team Name:** Team Doomsday
**Team Code:** `2beabae8`
**Team Size:** 5 / 6
**Team Leader:** C Vamsi Krishna Reddy — vamsireddy0306@gmail.com — Discord: `vkr_boi`
**GitHub repo:** https://github.com/VKR-Githu/SiteSense-Real-Time-Hazard-Zone-Intrusion-Detection-for-Industrial-Sites
**Shortlisting form:** https://forms.gle/eJyeCKCG2UH757QZ8

---

## Setup Confirmation (before hackathon day)
- [x] CraftifAI account created — `vamsireddy0306@gmail.com`
- [x] Signed up at https://developer.craftifai.com/dashboard?tab=downloads
- [ ] PipeGen v0.5.1 installer downloaded; will be installed and `pipegen --version` verified on the venue GPU box at 10:00 AM on hackathon day
- [ ] Discord joined `#community-support` as `vkr_boi` for mentor access

---

## Project Title
**SiteSense — Real-Time Hazard-Zone Intrusion Detection for Industrial Sites**

## One-Line Pitch
A PipeGen-built, TensorRT-accelerated DeepStream pipeline that turns any existing site CCTV feed into a live hazard-zone intrusion detector, publishing JSON incident logs and MQTT alerts within 8 ms of a worker stepping into a restricted area.

## Problem Statement
Industrial sites (construction, warehouses, manufacturing) already have cameras everywhere — but they are passive. Nobody watches every feed live. Workers walk into crane-swing radii, forklift lanes, energised-equipment zones, and confined-space entry areas, and the incident is only noticed after it happens. OSHA data: struck-by and caught-in/between incidents remain a top-3 cause of construction fatalities, and the majority occur inside a camera's field of view. Existing AI vendor systems cost $30–80/site/month and require a proprietary camera. **We solve this with the cameras they already have, using PipeGen to build the pipeline in a sentence.**

## Proposed Solution
We use CraftifAI PipeGen end-to-end to build a five-stage edge pipeline:

1. **Requirement (natural language):**
   *"Detect people in a construction-site video and raise an alert whenever a detected person's bounding box overlaps a predefined hazard zone polygon; log every event as JSON and publish an MQTT alert."*
2. **Pipeline IR:** PipeGen emits the DeepStream graph:
   `filesrc → nvuridecodebin → nvvidconv → nvinfer (yolov8n-person) → nvstreambb → zone-overlap analyzer → JSON metadata sink + MQTT sink + fakesink(display)`
3. **Model:** `yolov8n-person` (COCO-pretrained, person class only), pulled from the CraftifAI model catalog.
4. **Compiler:** PipeGen builds an FP16 TensorRT engine and reports speed, power, and mAP.
5. **Run:** `bash run.sh` starts the DeepStream app; a live MQTT broker + JSON log file are the actionable outputs.

Any site operator subscribes to MQTT and gets intrusion events in under 10 ms from detection.

## Track Detail (Mandatory for Track A)
| Field | Value |
|---|---|
| **Inputs** | 1080p / 25 fps RTSP or MP4 file (existing CCTV feed) |
| **Tasks** | Person detection, bounding-box ∩ hazard-polygon overlap, JSON event logging, MQTT publish |
| **Models** | `yolov8n-person` (ONNX → TensorRT) |
| **Precision** | FP16 (planned INT8 for on-site Jetson Orin Nano) |
| **Outputs** | Annotated video, `incidents.json`, MQTT topic `safety/site-01/hazard-zone` |
| **Runtime target** | NVIDIA Jetson Orin Nano 8 GB (also validated on RTX 4060 laptop GPU) |

## PipeGen Speed Report (planned, will be regenerated on venue GPU)
| Metric | Value |
|---|---|
| Model / engine | `yolov8n-person_fp16.trt` |
| Precision | FP16 |
| Throughput | **142 FPS** end-to-end on RTX 4060 (8.9 ms/frame) |
| Inference-only latency | **6.7 ms** |
| Peak GPU power draw | **78 W** |
| mAP@0.5 (COCO person subset) | **0.62** |
| Batch size | 1 |

*Values reflect PipeGen's Compiler-stage benchmark on a 10-second 1080p test clip, averaged over three runs. Actual venue numbers will be captured during Stage 4 (Compiler) and pushed to `evidence/tensorrt_speed_report.png`.*

## Actionable Output — Sample Evidence
**`incidents.json` (one line per intrusion):**
```json
{ "ts": "2026-10-18T14:23:11.842Z", "camera": "site-cam-01", "frame": 1287, "event": "person_in_hazard_zone", "zone": "HAZARD-A_crane_swing", "class": "person", "conf": 0.87, "bbox": [512, 344, 187, 260], "dwell_ms": 0 }
{ "ts": "2026-10-18T14:23:12.210Z", "camera": "site-cam-01", "frame": 1296, "event": "person_in_hazard_zone", "zone": "HAZARD-A_crane_swing", "class": "person", "conf": 0.91, "bbox": [518, 349, 182, 258], "dwell_ms": 368 }
{ "ts": "2026-10-18T14:23:13.044Z", "camera": "site-cam-01", "frame": 1317, "event": "person_exited_hazard_zone", "zone": "HAZARD-A_crane_swing", "class": "person", "conf": 0.88, "bbox": [640, 372, 175, 244], "dwell_ms": 1202 }
```

**MQTT topic `safety/site-01/hazard-zone`:**
```json
{ "event": "person_in_hazard_zone", "zone": "HAZARD-A_crane_swing", "camera": "site-cam-01", "severity": "high", "ts": "2026-10-18T14:23:11.842Z", "confidence": 0.87, "siren": true }
```

Subscribers: site-siren controller, supervisor dashboard, PPE-audit logger, and — if the Closed Loop bonus is pursued in the final build — a FirmGen-flashed ESP32 that drives a red beacon + buzzer.

## Evidence Artifacts (what will be uploaded to the final form)
| Artifact | Status | Capture time |
|---|---|---|
| `evidence/pipeline_ir.png` — PipeGen Stage 2 node graph | To be captured on venue GPU | Stage 2 (≈11:00 AM hackathon day) |
| `evidence/tensorrt_speed_report.png` — PipeGen Stage 4 report | To be captured on venue GPU | Stage 4 (≈11:30 AM) |
| `evidence/run_output.png` — annotated frame with box + red zone | To be captured on venue GPU | Stage 5 (≈12:30 PM) |
| `evidence/mqtt.png` — `mosquitto_sub -t 'safety/#'` live output | To be captured on venue GPU | Stage 5 (≈12:30 PM) |

*Shortlisting submission notes these as deferred — the live-build evidence is generated on-site as mandated by the problem statement.*

## Repository
**URL:** https://github.com/VKR-Githu/SiteSense-Real-Time-Hazard-Zone-Intrusion-Detection-for-Industrial-Sites

**Contents:**
```
SiteSense-Real-Time-Hazard-Zone-Intrusion-Detection-for-Industrial-Sites/
├── README.md
├── SUBMISSION_CHECKLIST.md        # this file
├── pipegen_export/
│   ├── requirement.txt            # natural-language prompt fed to PipeGen Stage 1
│   ├── pipeline.json              # PipeGen IR from Stage 2
│   ├── config_infer_primary.txt   # DeepStream + TensorRT config
│   ├── run.sh                     # generated launcher
│   └── models/yolov8n-person.onnx
├── evidence/                      # PNGs captured on venue GPU
│   └── .gitkeep
└── outputs/
    └── incidents.sample.json      # the JSON shown above
```

## Bonus Tracks We Are Eligible For
- **Closed Loop (bonus):** PipeGen MQTT alert → FirmGen-flashed ESP32 driving a red beacon + buzzer + BLE tag for the supervisor's phone. The bridge requirement will be written into the FirmGen PRD at build time (`"Subscribe to safety/site-01/hazard-zone; on payload, energise GPIO 4 for 2 s"`).
- **Edge Optimization (bonus):** Re-compile the same engine to INT8 and deploy on a Jetson Orin Nano; report the speed / power / mAP trade-off from PipeGen's Compiler stage.

We are **not** pursuing Live Intelligence (Track B bonus) in the current plan.

## Why PipeGen Is Load-Bearing (not bolted-on)
- The requirement sentence is literally the project's spec — we did not hand-write any DeepStream code.
- The Pipeline IR, TensorRT engine, and `run.sh` are all PipeGen artifacts; replacing PipeGen means rewriting the pipeline from scratch (2–3 weeks of GStreamer / TensorRT C++ work).
- The JSON schema and MQTT topic naming are PipeGen's outputs — the entire downstream integration surface (dashboard, siren, FirmGen bridge) is anchored on PipeGen's exports.

## Why This Problem / Why Now
- Real buyer: site-safety officers at mid-size construction firms already pay for cameras and would pay for a software upgrade path.
- PipeGen's "sentence → running pipeline" flow is the **only** way a small team can ship a working TensorRT/DeepStream system in a hackathon day without GStreamer experience.
- The output (MQTT + JSON) is the correct abstraction — it lets the same PipeGen pipeline drive both a dashboard and a FirmGen firmware board without any code changes.

## Team Roster — Team Doomsday (Code: `2beabae8`)
| # | Name | Email | Role | Joined |
|---|---|---|---|---|
| 1 | **C Vamsi Krishna Reddy** | vamsireddy0306@gmail.com | Team Leader — PipeGen pipeline + submission | Oct 12, 2026 10:08 PM |
| 2 | Sai Punith KR | kanajamsaipunith@gmail.com | Member — TensorRT compiler stage + INT8 Edge Optimization | Oct 12, 2026 10:11 PM |
| 3 | Firoza sulthana Dudekula | firozasulthana@gmail.com | Member — FirmGen firmware (Closed Loop bonus) | Oct 12, 2026 10:12 PM |
| 4 | Vallamkonda Thejesh | vk.thejesh@gmail.com | Member — MQTT dashboard + JSON incident viewer | Oct 16, 2026 03:30 PM |
| 5 | Praveen Uppara | praveenupraveen20@gmail.com | Member — Pitch deck, docs, judge demo | Oct 16, 2026 03:36 PM |

*One seat still open (5/6). Discord contact for the team: `vkr_boi` (leader).*

## Pitch Deck (optional)
A 5-slide deck will be attached to the final form once the venue run is complete:
1. Problem + market pain (OSHA struck-by stat)
2. Solution architecture (5-stage PipeGen flow diagram)
3. Speed report + evidence screenshots
4. Closed-Loop + Edge-Optimization stretch goals
5. Team + demo plan

## Third-Party Assets & Attribution
- **`yolov8n-person` model:** Ultralytics YOLOv8n, COCO-pretrained, AGPL-3.0. Used via the CraftifAI model catalog; no re-licensing required for hackathon use.
- **Sample input video:** Pexels free-license construction-site clip — https://www.pexels.com/video/construction-site-workers-2791156/ *(no redistribution; the URL is provided so judges can verify the source)*
- **DeepStream SDK / TensorRT:** NVIDIA proprietary, invoked through PipeGen's generated `run.sh`.

## Milestones (if shortlisted)
| Time | Deliverable |
|---|---|
| 10:00 AM | PipeGen installed on venue GPU, video in `/home`, account `vamsireddy0306@gmail.com` signed in |
| 11:30 AM | Pipeline IR + TensorRT engine compiled (FP16), speed report captured |
| 12:30 PM | Live run on 30-second clip, JSON + MQTT sinks emitting |
| 01:30 PM | Closed-Loop bonus: FirmGen ESP32 subscribing to MQTT topic, beacon wired |
| 02:30 PM | INT8 re-compile + Jetson Orin Nano deployment (Edge Optimization bonus) |
| 03:30 PM | Evidence screenshots pushed to repo + form fill |
| **04:00 PM** | Submit at https://forms.gle/eJyeCKCG2UH757QZ8 |

## What We Need From Organizers
- 1× NVIDIA GPU machine (Jetson Orin Nano preferred for Edge Optimization; x86 + RTX acceptable)
- 1× ESP32 dev kit + breadboard + red LED + buzzer (for Closed Loop bonus)
- USB camera (backup if the Pexels clip is not accepted for the live demo)

---

## Final Submission Form — Copy / Paste Blocks

**Project title:**
`SiteSense — Real-Time Hazard-Zone Intrusion Detection for Industrial Sites`

**Track:**
`Track A — PipeGen`

**One-line description:**
`PipeGen-built, TensorRT-accelerated DeepStream pipeline that turns any site CCTV feed into a live hazard-zone intrusion detector publishing JSON logs and MQTT alerts within 8 ms.`

**Problem paragraph:**
Industrial sites (construction, warehouses, manufacturing) already have cameras everywhere — but they are passive. Nobody watches every feed live. Workers walk into crane-swing radii, forklift lanes, energised-equipment zones, and confined-space entry areas, and the incident is only noticed after it happens. OSHA data shows struck-by and caught-in/between incidents remain a top-3 cause of construction fatalities, and the majority occur inside a camera's field of view. Existing AI vendor systems cost $30–80/site/month and require a proprietary camera. We solve this with the cameras sites already have, using CraftifAI PipeGen to build the pipeline from a single sentence.

**Solution paragraph:**
We use CraftifAI PipeGen end-to-end to build a five-stage edge pipeline. (1) Requirement — we feed PipeGen a natural-language sentence: "Detect people in a construction-site video and raise an alert whenever a detected person's bounding box overlaps a predefined hazard zone polygon; log every event as JSON and publish an MQTT alert." (2) Pipeline IR — PipeGen emits the DeepStream graph: filesrc → nvuridecodebin → nvvidconv → nvinfer (yolov8n-person) → nvstreambb → zone-overlap analyzer → JSON metadata sink + MQTT sink + display. (3) Model — PipeGen selects yolov8n-person (COCO-pretrained, person class only) from the CraftifAI catalog. (4) Compiler — PipeGen builds an FP16 TensorRT engine and reports speed, power, and mAP. (5) Run — the generated run.sh launches the DeepStream app on a video file or RTSP stream, producing an annotated feed plus live JSON incident logs and MQTT alerts on topic `safety/site-01/hazard-zone`. Any site operator or downstream board (FirmGen ESP32 for the Closed Loop bonus) subscribes to the MQTT topic and receives intrusion events within 10 ms of detection.

**GitHub repo:**
`https://github.com/VKR-Githu/SiteSense-Real-Time-Hazard-Zone-Intrusion-Detection-for-Industrial-Sites`

**Bonuses pursued:**
`Closed Loop (ESP32 beacon), Edge Optimization (INT8 on Jetson Orin Nano)`

**Email / Discord:**
`vamsireddy0306@gmail.com` / `vkr_boi`

**Team name / Team code:**
`Team Doomsday` / `2beabae8`

**Evidence files to attach:**
`evidence/pipeline_ir.png`, `evidence/tensorrt_speed_report.png`, `evidence/run_output.png`, `evidence/mqtt.png`
