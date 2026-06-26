---
title: Road Traffic Analyzer
emoji: 🚦
colorFrom: green
colorTo: blue
sdk: gradio
sdk_version: 4.44.1
app_file: app.py
pinned: false
license: mit
---

# 🚦 Road Traffic Analyzer

Drop a street video into the page and get back an **annotated video** that
detects, tracks, and counts **cars, buses, trucks, motorcycles, and people**
using **YOLOv8** — along with live **traffic-density** readouts and a set of
analytics charts.

![Built with YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-blue)
![Gradio](https://img.shields.io/badge/UI-Gradio-orange)
![License: MIT](https://img.shields.io/badge/License-MIT-green)

---

## What it does

- **Detects** cars, buses, trucks, motorcycles, bicycles, and people.
- **Tracks** every object with a stable ID across frames (ByteTrack).
- **Counts** each one as it crosses a virtual line — once, by class and direction.
- **Measures density** in real time and labels it Low / Medium / High.
- **Returns an annotated video** with boxes, motion trails, the counting line, and a live dashboard.
- **Generates charts**: totals by type, composition, cumulative crossings, and density over time.

---

## Try it locally

```bash
pip install -r requirements.txt
python app.py
```

Open <http://localhost:7860>, drop in a short street clip, and click **Analyze traffic**.

The first run downloads the YOLO weights (~6 MB) automatically.

---

## Deploy it free (Hugging Face Spaces)

Hugging Face Spaces is the recommended free host: the CPU tier has 16 GB RAM,
no request timeout to interrupt a long video job, and ffmpeg preinstalled. No
credit card needed.

1. Create a free account at <https://huggingface.co>.
2. Go to <https://huggingface.co/new-space>.
3. Set: **SDK = Gradio**, **Hardware = CPU basic (FREE)**, **Visibility = Public**, **License = MIT**.
4. Click **Create Space**.
5. Open the **Files** tab → **Add file → Upload files**, and upload `app.py`,
   `engine.py`, and `requirements.txt`. Commit.
6. Wait for the build to finish (a few minutes the first time). When the status
   shows **Running**, your site is live at
   `https://huggingface.co/spaces/<your-username>/<space-name>`.

> If you upload this `README.md` too, keep the `--- ... ---` block at the top —
> Spaces needs it to configure the app. The `sdk_version` is a safe floor; the
> app works on Gradio 4.44 through 6.

### Deploy with git (alternative)

```bash
git clone https://huggingface.co/spaces/<your-username>/<space-name>
cd <space-name>
cp /path/to/app.py /path/to/engine.py /path/to/requirements.txt .
git add app.py engine.py requirements.txt
git commit -m "Add traffic analyzer"
git push
```

### Why not Vercel / Netlify?

They're serverless: function size is capped around 250 MB (PyTorch alone is far
bigger) and requests time out in seconds, while a video pass takes minutes. They
can't run this workload. Use Spaces, or split the app (static frontend on Vercel
calling a Spaces backend API).

---

## Controls

| Control | What it does |
|---|---|
| **Model** | `yolov8n` (fast) or `yolov8s` (more accurate, slower) |
| **Detection confidence** | Higher = fewer, more certain detections |
| **Counting line position** | Where the line sits, top → bottom of the frame |
| **Seconds to analyze** | Caps processing time on the free CPU tier |

---

## How it works

1. **Detect** — YOLOv8 finds objects each frame and keeps only traffic classes.
2. **Track** — ByteTrack gives each object a persistent ID.
3. **Count** — a virtual line is drawn across the road; using the sign of a 2D
   cross product we know which side each object is on, and when a tracked object
   flips sides (within the line segment) it's counted once, by class and direction.
4. **Density** — the number of vehicles in frame is smoothed and mapped to
   Low / Medium / High.
5. **Report** — per-frame data is logged and turned into charts and a summary.

---

## Files

| File | Purpose |
|---|---|
| `app.py` | Gradio web UI: drag-drop video in, annotated video + charts out |
| `engine.py` | Detection, tracking, counting, density, and chart generation |
| `requirements.txt` | Dependencies |

---

## Notes on speed

The free tier has no GPU, so CPU inference is the bottleneck. To go faster: keep
clips short, use the **Fast (yolov8n)** model, lower `cfg.img_size` in `app.py`,
or upgrade the Space to a GPU tier later — the same code runs unchanged.

---

## License

MIT
