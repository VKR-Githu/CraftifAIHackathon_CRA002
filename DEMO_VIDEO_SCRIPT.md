# Demo Video Script — SiteSense Architecture Walk-through

**Total runtime:** 2:00 minutes
**Recording tool:** OBS Studio (free) or Loom — record your screen at 1080p
**Upload target:** YouTube as **Unlisted** (anyone with link can view)
**Title:** `SiteSense — Architecture Walk-through | Team Doomsday (CRA002) | Embedded SF Hackathon`
**Description:** `PipeGen Track A submission. Architecture walk-through — live pipeline run scheduled for hackathon venue GPU on Oct 18. Repo: https://github.com/VKR-Githu/CraftifAIHackathon_CRA002`

---

## Recording checklist (before you press Record)

- [ ] Close all personal tabs, Slack, Discord, mail
- [ ] Set Windows wallpaper to plain dark color
- [ ] Turn on dark mode in your file explorer and browser
- [ ] Have your browser set to 100% zoom, full screen (F11)
- [ ] Turn OFF notifications (Focus Assist / Do Not Disturb)
- [ ] Have a **construction-site CCTV clip** ready in a browser tab (Pexels link from README) — you'll show it as "the input"
- [ ] Have a **text-to-speech or your own voice** for the narration (voice is more credible)

---

## Shot-by-shot script

### Shot 1 — 0:00 – 0:15 · Title card
**Screen:** Open `README.md` in your browser (rendered GitHub view) or in VS Code.
**Narration:**
> "Hi, this is Vamsi from Team Doomsday. We're submitting SiteSense to Embedded SF Track A, the PipeGen track. This is a two-minute architecture walk-through. The live pipeline run is scheduled on the venue GPU on hackathon day."

**On-screen:** README hero section with the project title + one-line pitch.

---

### Shot 2 — 0:15 – 0:35 · The problem
**Screen:** Scroll README down to "The Problem" section.
**Narration:**
> "Industrial sites already have cameras everywhere, but nobody watches every feed live. OSHA data shows struck-by incidents are a top three cause of construction fatalities, and most happen inside a camera's field of view. Site-safety officers today patrol a wall of monitors manually and only notice a hazard-zone intrusion after an incident."

---

### Shot 3 — 0:35 – 0:55 · The requirement sentence
**Screen:** Open `pipegen_export/requirement.txt` in a text editor, zoom to 14pt.
**Narration:**
> "We give PipeGen one sentence. Detect people in a construction-site video, raise an alert whenever a bounding box overlaps a predefined hazard zone polygon, log every event as JSON, publish an MQTT alert. That sentence is our entire spec — no hand-written DeepStream code."

**Action:** Highlight the first sentence with the mouse.

---

### Shot 4 — 0:55 – 1:20 · Pipeline IR diagram
**Screen:** Open `evidence/pipeline_ir.svg` in a browser (full screen).
**Narration:**
> "Here's the Pipeline IR that PipeGen generates from that sentence. Input file source, preprocess to RGB and resize, inference with yolov8n-person at FP16 TensorRT, postprocess to draw bounding boxes, then the analyzer node we typed in — the zone-overlap check against the crane-swing polygon. Three outputs fan out: live display, JSON incident log, and MQTT publisher on safety/site-01/hazard-zone."

**Action:** Mouse over each node slowly as you name it.

---

### Shot 5 — 1:20 – 1:35 · Sample outputs
**Screen:** Open `outputs/incidents.sample.json` in a text editor.
**Narration:**
> "Here's what the JSON incident log looks like when a worker steps in and out of the zone. Each event has a timestamp, camera ID, frame number, class, confidence, bounding box, and dwell time in milliseconds. The MQTT message on the same event carries severity high and a siren flag that a downstream board can act on."

**Action:** Scroll through the JSON lines.

---

### Shot 6 — 1:35 – 1:50 · Reference of what the Run stage looks like
**Screen:** Open the CraftifAI "10 minutes" YouTube video tab: https://www.youtube.com/watch?v=ZSxDxxdIkts
**Play 10 seconds of the section where the Run console shows detections.**
**Narration (over the video):**
> "This is a screenshot from CraftifAI's own tutorial showing what the Run stage looks like — the video console with detections, the terminal log, and the events table filling up. On venue day we'll produce the same view with our hazard-zone pipeline running on the site CCTV clip."

**On-screen:** Add a **text overlay** in the video editor: *"Reference: CraftifAI PipeGen tutorial video (linked in description)"* — this is critical for honesty.

---

### Shot 7 — 1:50 – 2:00 · Close
**Screen:** Back to README, scroll to Team Roster.
**Narration:**
> "Team Doomsday, code 2beabae8. Track A, PipeGen, targeting the Closed Loop and Edge Optimization bonuses. Repo is public at the link in the description. Thanks."

---

## Editing rules (be honest, avoid DQ)

1. **Shot 6 must be labeled on-screen** as "Reference: CraftifAI tutorial". Do not present it as your own run.
2. **Do not** add fake FPS counters, fake terminal logs, or fake detection boxes to the video.
3. **Do** say the words *"architecture walk-through"* and *"venue run scheduled"* in the intro and outro — judges will search for these phrases.
4. **Do** keep the video under 2 minutes. Longer feels like padding.
5. **Do** upload to YouTube as Unlisted (not Public — you don't want it indexed against you; not Private — the form requires link-viewable).

---

## What to paste into the form's "Demo video URL" field

```
https://youtu.be/<your-unlisted-id>
```

If the form has a text-notes box near it, add:
```
Architecture walk-through (2 min). Live pipeline run scheduled on venue GPU on hackathon day. Shot 6 references CraftifAI's own tutorial video for what the Run stage looks like.
```

---

## Time budget

| Task | Minutes |
|---|---|
| Rehearse the narration once | 10 |
| Record the 7 shots | 15 |
| Stitch in a simple editor (Clipchamp is built into Windows 11) | 15 |
| Upload to YouTube as unlisted | 5 |
| **Total** | **45 min** |
