# Taskbar Media Bar & Resource Bar

**Two minimal, transparent, always-visible bars for Windows: one shows the song or video currently playing and lets you control it, the other shows your CPU, RAM and GPU usage in real time. Both float over the taskbar area without getting in your way.**

*Written in Python with Tkinter, pywin32 and psutil. Each one can be compiled into a single `.exe` with PyInstaller.*

---

## Overview

| Tool | Script | What it does |
|------|--------|--------------|
| **Media Bar** | `barra.py` | Shows what is playing and controls playback |
| **Resource Bar** | `rendimiento.py` | Shows CPU, RAM and GPU usage |

Both share the same look and behavior: frameless, transparent background, always on top, horizontally draggable, position remembered between launches, and automatically hidden when a fullscreen window is active.

---

# Media Bar

## What it does

The bar sits at the bottom right of the screen, frameless and with a transparent background, so only the text and buttons are visible. It regularly checks the open windows, detects what is playing and displays its title.

### Main features

- **Real-time title:** shows the name of what is playing, trimmed to 35 characters so the bar does not grow out of control.
- **Media controls:** previous, play/pause and next. They work by sending the Windows media keys, just like the keys on a keyboard with audio controls.
- **Reload button:** forces a new search for the active player.
- **Close button:** a small, discreet `×` closes the application.
- **Horizontally adjustable position:** drag the text with the mouse to the left or right. The height stays fixed.
- **Remembered position:** when you release the mouse, the position is saved and restored on the next launch.
- **Hides in fullscreen:** if the active window fills the whole screen (a game or a video), the bar moves to the background so it does not get in the way.
- **Always on top:** otherwise it stays above all other windows.

## Supported players

| Type | Programs | How it is detected |
|------|----------|--------------------|
| Browsers | Chrome, Edge, Firefox | Windows whose title contains **YouTube** |
| Local players | Spotify, VLC, Windows Media Player | Title of the program's window |

> Detection is based on the **window title**, not on a direct connection to the player.

## Known issue

**Sometimes the bar does not show the correct title, stays on "Sin reproducción activa" (the default "no active playback" text, shown in Spanish), or the controls do not respond as expected.**

*This is known behavior and has a simple fix:*

1. **Click on the player window** (the YouTube tab, Spotify, VLC, etc.).
2. If needed, press the bar's **reload button**.
3. From that point on, everything works normally.

This tends to happen when the bar is started before the player, or when Windows is not yet sure which application is the active one for the media keys. Giving the player focus with a click resolves it.

## Usage

| Action | How |
|--------|-----|
| Move the bar | Drag the **text** sideways |
| Play / pause | Center button |
| Previous track | Left button |
| Next track | Right button |
| Search for the player again | Reload button |
| Close | `×` |

---

# Resource Bar

## What it does

A compact readout of your system load, styled like the Media Bar. It looks like this:

```
CPU  12%    RAM  45%    GPU   8%
```

### Main features

- **CPU, RAM and GPU usage:** refreshed every second (CPU and RAM) and every few seconds (GPU).
- **Color-coded load:** white for normal usage, **orange from 70%** and **red from 90%**, so a glance is enough to spot a spike.
- **Horizontally adjustable position:** drag any of the numbers to move the bar sideways. The height stays fixed.
- **Remembered position:** saved when you release the mouse and restored on the next launch.
- **Hides in fullscreen:** goes to the background when a game or video is fullscreen.
- **Close button:** a small `×` closes it.

## How the GPU is read

The GPU is queried in a **background thread**, so the bar never freezes while waiting for the result.

| Situation | Method |
|-----------|--------|
| NVIDIA card with `nvidia-smi` available | Reads utilization directly from `nvidia-smi` (fast and lightweight) |
| Any other GPU (AMD, Intel) | Reads the Windows GPU performance counters (3D engine usage) |
| Neither works | Shows `GPU N/D` |

> The Windows counters method is slightly heavier, which is why the GPU is only sampled every few seconds.

## Usage

| Action | How |
|--------|-----|
| Move the bar | Drag the **text** sideways |
| Close | `×` |

---

# Installation

## Requirements

- Windows 10 or 11
- Python 3.8 or higher

## Dependencies

```bash
pip install pywin32 psutil pyinstaller
```

Tkinter is included with the standard Python installation for Windows.

## Run from source

```bash
python barra.py
python rendimiento.py
```

---

# Build the .exe

To generate a single executable with no console window:

```bash
python -m PyInstaller --noconsole --onefile barra.py
python -m PyInstaller --noconsole --onefile rendimiento.py
```

The results appear in the `dist` folder.

**Tip:** if something fails while building or running the `.exe`, build first **without** `--noconsole` to see the errors on screen:

```bash
python -m PyInstaller --onefile barra.py
```

If the executable does not start because of missing modules, try hidden imports:

```bash
python -m PyInstaller --noconsole --onefile --hidden-import win32timezone --hidden-import psutil barra.py
```

---

# Generated files

They are created next to the `.exe` (or next to the `.py` if run without compiling). Each bar has its own files, so they never overwrite each other.

| Bar | Position file | Error log |
|-----|---------------|-----------|
| Media Bar | `barra_config.json` | `barra_errores.log` |
| Resource Bar | `rendimiento_config.json` | `rendimiento_errores.log` |

- The position file stores the horizontal position. **Delete it** to return to the initial position.
- The error log is very useful for diagnosing problems, since the `.exe` runs without a console.

---

# Configuration

At the top of each script there are constants you can tweak.

**Media Bar** (`barra.py`):

```python
DESPLAZAMIENTO_X = 160
```

**Resource Bar** (`rendimiento.py`):

```python
DESPLAZAMIENTO_X = 620   # initial horizontal position
INTERVALO_MS = 1000      # CPU and RAM refresh (milliseconds)
INTERVALO_GPU_S = 3      # GPU refresh (seconds)
```

`DESPLAZAMIENTO_X` is only the value for the **first run**. After that, the saved position takes priority.

---

# Limitations

- Works on **Windows** only.
- The Media Bar controls act on whichever application Windows considers active for the media keys, which does not always match the one whose title is shown.
- If several players are open at the same time, the Media Bar shows the last title detected.
- The Resource Bar shows the usage of the 3D engine of the GPU, which is the most representative value for games but may differ from other GPU tools.
- If the GPU cannot be read on your system, the Resource Bar shows `GPU N/D`.

---

# Blog

Want the full story behind this project, with screenshots and the development process? Read the write-up on Blogger:

**[Read the blog post](https://YOUR-BLOG.blogspot.com/YYYY/MM/your-post.html)**
