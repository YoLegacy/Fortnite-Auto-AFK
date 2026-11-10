[README.md](https://github.com/user-attachments/files/34606324/README.md)
# Fortnite Ready Bot

A simple Python script that clicks the **Play / Ready** button in the Fortnite lobby for you, waits for the match to end, clicks through popups like **Return to Lobby** and **Claim**, and then repeats.

It looks at your screen to see which button is showing, so it does not rely on fixed timers.

> **Warning:** Using bots or AFK automation may break Epic Games' Terms of Service and could get your account banned. Use it at your own risk. This project is not affiliated with Epic Games.

## What you need

- Windows 10 or 11
- [Python](https://www.python.org/downloads/) (tick **Add python.exe to PATH** when installing)
- Fortnite set to **Windowed Fullscreen**, on your main monitor

## Setup

1. **Download this project.** Click **Code**, then **Download ZIP**, and extract it.

2. **Install the packages.** Open the project folder, click the address bar at the top, type `cmd`, and press Enter. In the black window, run:
   ```
   pip install -r requirements.txt
   ```
   If you don't have `requirements.txt`, run this instead:
   ```
   pip install pyautogui opencv-python numpy pillow
   ```

3. **Make a `templates` folder** inside the project folder, if there isn't one.

4. **Take pictures of the buttons.** In Fortnite, get the button on screen with your mouse **off** it. Press **Windows key + Shift + S**, drag a tight box around just the button, and save it as a PNG in the `templates` folder:

   | File name | Button |
   |---|---|
   | `ready.png` | Play / Ready (required) |
   | `return_to_lobby.png` | Return to Lobby |
   | `claim.png` | Claim |

   The pictures only work at your own screen resolution, so take them on the same setup you'll play on.

## Run it

Double-click **`run_bot.bat`**.

A black window opens with a 5-second countdown. Click on Fortnite before it ends, with the lobby showing. The window prints a message each time the bot does something.

## Stop it

Do either of these:

- Move your mouse into **any corner of the screen**
- Click the black window and press **Ctrl + C**

## Add more buttons

If the bot gets stuck on a new popup:

1. Take a picture of that button and save it in `templates` (for example `continue.png`).
2. Open `fortnite_ready_loop.py` in Notepad and add it to the `DISMISS_TEMPLATES` list near the top:
   ```python
   DISMISS_TEMPLATES = [
       "templates/return_to_lobby.png",
       "templates/claim.png",
       "templates/continue.png",
   ]
   ```
3. Save the file.

## Problems

| Problem | Fix |
|---|---|
| `'python' is not recognized` | Reinstall Python and tick **Add python.exe to PATH** |
| `Template not found` | Put `ready.png` in the `templates` folder |
| Bot doesn't click anything | Retake the button picture tighter, with the mouse off the button |
| Black screenshots | Switch Fortnite to **Windowed Fullscreen** |
| Stops working after a while | Turn off sleep and screen lock in Windows power settings |

The bot writes what it did to `fortnite_loop.log`, which helps you see where it got stuck.
