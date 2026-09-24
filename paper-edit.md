---
layout: project
title: Paper Edit → Clips
description: Turning a handwritten paper edit into ready-to-cut DNxHR clips.
---

# Paper Edit → Clips

Tools for turning a paper edit — a list of clock-time in/out points, logged by hand — into DNxHR clips ready for DaVinci Resolve on Linux.

I still log footage the old way: pen, paper, timecodes. The technology exists to skip that step, but the judgment call of *what's worth keeping* is exactly the part that shouldn't be automated. This kit picks up right after that human decision is made, and automates everything mechanical from there — logging, transcoding, verifying — so the only slow part left is the part that should be slow.

Two ways to work: a terminal-only workflow (durable, plain text, nothing lost if a tab closes) and a browser app (visual, good for reviewing a long list). Both produce the same CSV format and the same `ffmpeg` commands.

## Terminal Workflow

**1. Log each clip segment**
```bash
./log_clip.sh clips.csv "/path/to/source.mp4" 00:03:05 00:03:15
```
Every call appends one row to `clips.csv`. Multiple selects from the same source auto-number themselves.

**2. Convert to DNxHR**
```bash
./extract_clips.sh clips.csv /path/to/output --preset hq
```

| Preset | Use for |
|---|---|
| `lb` | Small, fast-scrubbing proxies |
| `sq` | Light offline editing |
| `hq` | Full quality, edit-and-deliver (default) |
| `hqx` | 10-bit finishing/mastering |

**3. Verify before trusting the whole batch**
```bash
ffprobe -hide_banner -v error -select_streams v:0 \
  -show_entries stream=codec_name,profile,pix_fmt,width,height,r_frame_rate \
  /path/to/output/dnxhr_hq/your_clip_seg1.mov
```
Then straight into Resolve's Media Pool.

## Browser App

A single self-contained HTML file — no frameworks, no build step, no backend — for visually reviewing a long segment list before exporting. Same CSV format as the terminal tools; the two are fully interchangeable.

## Why It's Built This Way

- **Clock-time in/out points**, not frame-based — matches how a paper edit actually gets logged by hand.
- **Fast input seek** (`-ss` before `-i`) — accurate for re-encodes, much faster than decoding the whole file first.
- **Never halts on one bad row** — the extraction script reports success or failure per line and skips anything invalid, rather than losing an entire batch over one typo.

[View the full repository on GitHub →](REPLACE-WITH-YOUR-REPO-LINK)
