[README.md](https://github.com/user-attachments/files/34550683/README.md)
# Fortnite-Auto-AFK# Fortnite Lobby Ready Loop

A state-aware Python script that watches your screen for Fortnite's **Play / Ready** button, clicks it, waits for the match to start and end, clicks through post-match popups (Return to Lobby, Claim, etc.), and repeats.

Instead of blind timers, it uses OpenCV template matching to detect what is on screen, so it stays in sync even when queue times vary or you are eliminated early.

> **Disclaimer:** Automating gameplay or AFK farming may violate Epic Games' Terms of Service and can lead to account action. This script only reads the screen and sends normal mouse clicks, but you use it at your own risk. This project is not affiliated with Epic Games.

## Features

- Template-matching detection of buttons (multi-scale, tolerant of small resolution differences)
- State machine: `WAIT_FOR_READY` -> `WAIT_FOR_POP` -> `IN_MATCH` -> repeat
- Automatically clicks popup buttons (Return to Lobby, Claim, Continue, ...) in both the lobby and match states
- Failsafe: move the mouse into any screen corner to kill the script
- Clean Ctrl+C handling
- Timestamped logging to the terminal and to `fortnite_loop.log`
- `--test` mode that scores your templates against the current screen and saves a debug image

## Requirements

- Windows 10 / 11
- Python 3.9 or newer (added to PATH during install)
- Fortnite set to **Windowed Fullscreen** (exclusive fullscreen often produces black screenshots)
- Fortnite on your **primary monitor**

Python packages (installed from `requirements.txt`):

- `pyautogui`
- `opencv-python`
- `numpy`
- `pillow`

## Installation

1. **Install Python** from [python.org/downloads](https://www.python.org/downloads/). On the first installer screen, tick **Add python.exe to PATH**.

2. **Download this project**:
   ```
   git clone https://github.com/YOUR-USERNAME/YOUR-REPO.git
   cd YOUR-REPO
   ```
   Or click **Code -> Download ZIP** on GitHub and extract it.

3. **Open a terminal in the project folder.** In File Explorer, click the address bar, type `cmd`, and press Enter.

4. **Install the dependencies**:
   ```
   pip install -r requirements.txt
   ```

5. **Create a `templates` folder** in the project folder if it does not already exist.

## Capture your button templates

Template matching compares small screenshots of buttons against your screen, so you need to capture them on **your own** setup. Use the same resolution, window mode, and UI scale you will use when the script runs.

1. Set Fortnite to **Windowed Fullscreen**.
2. Get the button on screen and keep your mouse **off** it (hover glow changes its colour).
3. Press **Win + Shift + S** and drag a tight box around just the button face (the text plus a thin edge, no lobby background).
4. Save it as a PNG in the `templates` folder.

| File | Button | Required |
|---|---|---|
| `templates/ready.png` | The Play / Ready button | Yes |
| `templates/return_to_lobby.png` | Return to Lobby after elimination | Recommended |
| `templates/claim.png` | Claim popup (rewards, soundtracks, etc.) | Recommended |

To handle more popups, save each button as its own PNG and add it to the `DISMISS_TEMPLATES` list near the top of `fortnite_ready_loop.py`:

```python
DISMISS_TEMPLATES = [
    "templates/return_to_lobby.png",
    "templates/claim.png",
    "templates/continue.png",
]
```

If the main button text changes (for example **Play** vs **Ready**), add each version to `READY_TEMPLATES`.

## Test your templates

With one of your buttons visible on screen, run:

```
python fortnite_ready_loop.py --test
```

You get a 5-second countdown. Click on Fortnite so it is the front window. The script then prints a score for each template:

```
ready.png: best score 0.888 (PASS) at (258, 914)
```

- **0.90+**: great
- **0.80 to 0.90**: works, but consider recropping tighter
- **Below 0.80**: not detected; recapture the template

It also saves `debug_match.png` with a green box around every button that passed. Check that the boxes are on the right buttons.

Also test a screen where the button is **not** showing. The score should drop well below 0.80.

## Run

```
python fortnite_ready_loop.py
```

1. You get a 5-second countdown. Click on Fortnite so it is the front window, with the lobby showing.
2. Watch the terminal for status messages:
   ```
   Ready button detected and clicked
   Button gone -> queue popped / loading. Match in progress...
   Dismiss button detected (return_to_lobby.png), clicking.
   Returned to lobby after 4.2 min.
   ```

### How to stop it

- Move the mouse into **any screen corner** (fastest), or
- Click the terminal and press **Ctrl + C**

## Configuration

Settings are at the top of `fortnite_ready_loop.py`:

| Setting | Default | Meaning |
|---|---|---|
| `MATCH_THRESHOLD` | `0.80` | Minimum score to count as a match. Raise for false positives, lower for misses. |
| `SCALES` | `0.8 to 1.2` | Template sizes tried, for resolution tolerance. |
| `SEARCH_REGION` | `None` | `(left, top, width, height)` to search only part of the screen (faster, fewer false positives). |
| `POLL_LOBBY_SEC` | `1.0` | How often to check in the lobby. |
| `POLL_MATCH_SEC` | `3.0` | How often to check during a match. |
| `CLICK_RETRY_SEC` | `20` | Re-click if the button is still visible this long after a click. |
| `MATCH_WARN_MIN` | `45` | Log a warning if a match lasts this long. |
| `START_DELAY_SEC` | `5` | Countdown before starting. |

## Troubleshooting

| Problem | Fix |
|---|---|
| `'python' is not recognized` | Reinstall Python and tick **Add python.exe to PATH**. |
| `Template not found: templates/ready.png` | Put `ready.png` in the `templates` folder next to the script. |
| Score is low | Retake the template tighter, with the mouse off the button, at your current resolution. |
| `debug_match.png` is black | Switch Fortnite to **Windowed Fullscreen**. |
| Script gets stuck on a screen | Capture that screen's button, save it in `templates`, and add it to `DISMISS_TEMPLATES`. |
| Clicks the wrong spot | Make sure Fortnite is on your primary monitor and you did not change resolution after taking the templates. |
| Stops after a while | Turn off sleep and screen lock in Windows power settings. |

Check `fortnite_loop.log` to see where the script was when it stalled.

## Notes

- Test for several full loops while you are present before leaving it unattended.
- Unexpected popups (updates, disconnects, error dialogs) will stall the loop unless you add templates for them.
- Leaving matches early or being AFK can cause queue penalties and may affect ranked or competitive modes.

## License

Add a license of your choice (for example MIT) before publishing.
