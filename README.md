# 💿 Videomin Studio

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/)
[![FFmpeg](https://img.shields.io/badge/dependency-ffmpeg-green.svg)](https://ffmpeg.org/)

A lightweight command-line tool to detect, analyze, and fix interlaced video artifacts using FFmpeg.

## 📖 Table of Contents
- [What is Interlacing?](#-what-is-interlacing)
- [Installation](#-installation)
- [Usage](#-usage)
- [The Science](#-the-science-fields-vs-frames)
- [Contributing](#-contributing)

---

## 🧐 What is Interlacing?

Interlacing is a legacy compression technique used in broadcast television (NTSC/PAL) and analog media (VHS/DVD). 

Instead of capturing a full image at once (Progressive), interlacing captures lines 1, 3, 5... at time $T$, and lines 2, 4, 6... at time $T+1$.

**The Problem:**
When you play interlaced video on modern progressive screens (monitors, phones), you see "combing" artifacts (jagged horizontal lines) during motion.

**The Solution:**
This tool uses motion-adaptive deinterlacing (`yadif`) to weave these fields back into full progressive frames.

---

## ⚡ Installation

Requires **FFmpeg** installed on your system path.

```bash
git clone [https://github.com/yourusername/deinterlacer-cli.git](https://github.com/yourusername/deinterlacer-cli.git)
cd deinterlacer-cli
pip install -r requirements.txt

```
# Video Deinterlacing 101

A technical overview of deinterlacing techniques, why they are necessary for modern displays, and how to implement them.

## 📺 What is Interlacing?

Interlacing was a technique developed for analog television (CRT) to transmit video at a perceived high frame rate without using extra bandwidth. Instead of sending full frames, the signal sends alternating lines:
- **Field A:** Odd lines (1, 3, 5...)
- **Field B:** Even lines (2, 4, 6...)

Modern displays (LCD, OLED) are **Progressive**, meaning they draw all lines at once. When you play interlaced content on a progressive screen, you see "combing" artifacts.



---

## 🛠 Deinterlacing Methods

Deinterlacing is the process of reconstructing a progressive frame from interlaced fields.

### 1. Field Splitting (Bob)
Each field is scaled up to a full frame. 
- **Result:** $60i \rightarrow 60p$.
- **Downside:** Significant loss in vertical resolution and "shimmering" edges.

### 2. Field Combination (Weave)
Merging two consecutive fields into one frame.
- **Result:** Full resolution.
- **Downside:** Heavy "combing" artifacts during any horizontal motion.

### 3. Motion Adaptive (YADIF)
"Yet Another DeInterlacing Filter" is the industry standard. It analyzes the image:
- It uses **Weave** for static parts of the image.
- It uses **Bob/Interpolation** for moving parts.

---

## 🚀 Implementation Examples

### FFmpeg
To deinterlace a video using the YADIF filter (the most common method):

```bash
# Output 1 frame per frame (30fps from 60i)
ffmpeg -i input.mp4 -vf "yadif=0" output.mp4

# Output 1 frame per field (60fps from 60i - smoother)
ffmpeg -i input.mp4 -vf "yadif=1" output.mp4
