---
layout: project
title: Paper Edit → Clips
description: Turning a handwritten shot log into ready-to-cut DNxHR footage.
---
<link rel="stylesheet" href="/assets/css/custom.css">

Tools for turning a paper edit — clock-time in/out points, logged by hand — into DNxHR clips ready for DaVinci Resolve on Linux. Two ways to work: a terminal-only workflow (durable, plain text, nothing lost if a tab closes) and a browser app (visual, good for reviewing a long list). Both produce the same CSV format and the same `ffmpeg` commands.

## Project Goals

- Preserve the human judgment call — what's worth keeping — as a paper-and-pen step, not an automated one
- Automate everything mechanical after that decision: logging, transcoding, verifying
- Keep the workflow durable — plain text on disk, not dependent on a browser tab staying open
- Support multiple quality tiers from the same source list, without re-logging anything
- Never let one bad row silently kill an entire batch

## How It Works

**1. Log each segment**

```bash
./log_clip.sh clips.csv "/path/to/source.mp4" 00:03:05 00:03:15
```

Every call appends one row to a plain CSV file. Multiple selects from the same source auto-number themselves.

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

Each preset writes into its own subfolder, so a proxy pass and a full-quality pass of the same list never collide.

**3. Verify before trusting the whole batch**

```bash
ffprobe -hide_banner -v error -select_streams v:0 \
  -show_entries stream=codec_name,profile,pix_fmt,width,height,r_frame_rate \
  /path/to/output/dnxhr_hq/your_clip_seg1.mov
```

Then straight into Resolve's Media Pool.

## The Browser App

<img src="/assets/images/paper-edit-screenshot.png" width="75%">

A single self-contained HTML file — no frameworks, no build step, no backend — for visually logging and reviewing a long segment list before exporting. It generates the same CSV format and the same `ffmpeg` commands as the terminal tools, so the two are fully interchangeable: log with the script, review in the app, or vice versa.

## Being Honest About the Limits

The browser app's segment list only persists in that specific browser's local storage. A cache clear, a private window, or switching browsers loses it. The terminal workflow doesn't have this problem — a CSV on disk survives all of that — which is why it's the recommended path, not just an alternative.

## Files in the Kit

| File | Purpose |
|---|---|
| `log_clip.sh` | Appends one in/out segment to a CSV |
| `extract_clips.sh` | Reads a CSV, transcodes every row to DNxHR at the chosen preset(s) |
| `paper_edit_csv.html` | Browser app — visual alternative, same underlying format |
| `clips_example.csv` | Example CSV showing the expected format |
| `README.md` | Full documentation |

## Currently In Use

Actively logging and transcoding footage for a live edit right now — segments get discarded as the cut narrows, and surviving clips go straight into Resolve.

[Download the tools on GitHub →](REPLACE-WITH-YOUR-REPO-LINK)
