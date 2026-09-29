# Taskbar Media Bar

**A minimal, transparent, always-visible bar for Windows that shows the song or video currently playing and lets you control it without switching windows.**

*Written in Python with Tkinter, pywin32 and psutil. Can be compiled into a single `.exe` with PyInstaller.*

---

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

---

## Supported players

| Type | Programs | How it is detected |
|------|----------|--------------------|
| Browsers | Chrome, Edge, Firefox | Windows whose title contains **YouTube** |
| Local players | Spotify, VLC, Windows Media Player | Title of the program's window |

> Detection is based on the **window title**, not on a direct connection to the player.

---

## Known issue

**Sometimes the bar does not show the correct title, stays on "Sin reproducción activa" (the default "no active playback" text, shown in Spanish), or the controls do not respond as expected.**

*This is known behavior and has a simple fix:*

1. **Click on the player window** (the YouTube tab, Spotify, VLC, etc.).
2. If needed, press the bar's **reload button**.
3. From that point on, everything works normally.

This tends to happen when the bar is started before the player, or when Windows is not yet sure which application is the active one for the media keys. Giving the player focus with a click resolves it.

---

## Installation

### Requirements

- Windows 10 or 11
- Python 3.8 or higher

### Dependencies

```bash
pip install pywin32 psutil
```

Tkinter is included with the standard Python installation for Windows.

### Run from source

```bash
python barra.py
```

---

## Build the .exe

To generate a single executable with no console window:

```bash
python -m PyInstaller --noconsole --onefile barra.py
```

The result appears in the `dist` folder.

**Tip:** if something fails while building or running the `.exe`, build first **without** `--noconsole` to see the errors on screen:

```bash
python -m PyInstaller --onefile barra.py
```

If the executable does not start because of missing modules, try hidden imports:

```bash
python -m PyInstaller --noconsole --onefile --hidden-import win32timezone --hidden-import psutil barra.py
```

---

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

## Generated files

They are created next to the `.exe` (or next to `barra.py` if run without compiling):

- `barra_config.json` stores the horizontal position. **Delete it** to return to the initial position.
- `barra_errores.log` records internal errors. It is very useful for diagnosing problems, since the `.exe` runs without a console.

---

## Configuration

At the top of the script there is a constant with the bar's initial position:

```python
DESPLAZAMIENTO_X = 160
```

It is only the value for the **first run**. After that, the saved position takes priority.

---

## Limitations

- Works on **Windows** only.
- The controls act on whichever application Windows considers active for the media keys, which does not always match the one whose title is shown.
- If several players are open at the same time, the bar shows the last title detected.

---

## License

Add the license of your choice here (for example, MIT).
