# ISOStream Interview Mode: remote guests in OBS

Bring up to **8 remote guests** into OBS Studio with **one link per show**. Guests join from their
browser (no app, no account). Each person arrives in OBS as their own camera and audio source. An
active-speaker director switches shots for you.


---

## Quick start (about 5 minutes)

1. [Download](#1-download) the file for your computer.
2. [Install the plugin](#2-install-the-obs-plugin) into OBS.
3. [Turn on Browser Source Hardware Acceleration](#3-turn-on-browser-source-hardware-acceleration).
4. [Connect your account and start an interview](#4-connect-and-start-an-interview).

### 1. Download

Open the [**Releases**](../../releases/latest) page and download the file for your computer:

| Your computer | Download | What's inside |
|---|---|---|
| Mac with Apple Silicon (M1–M4) | `isostream-interview-macos-arm64.zip` | **OBS plugin** + bridge |
| Mac with Intel chip | `isostream-interview-macos-x64.zip` | bridge |
| Windows PC (64-bit, Intel or AMD) | `isostream-interview-windows-x64.zip` | bridge |
| Linux (x86-64) | `isostream-interview-linux-x64.tar.gz` | bridge |

Not sure which Mac you have? Click  → **About This Mac**. "Chip: Apple M…" means Apple Silicon.

The **plugin** puts an **Interview** panel inside OBS. It is the easiest way to run ISOStream Interview Mode.
For now it is only built for Apple Silicon Macs. On other computers, use the
[bridge](#using-the-bridge-instead-of-the-plugin) instead.

### 2. Install the OBS plugin

**Requires:** OBS Studio 30 or newer.

1. **Quit OBS.**
2. Unzip `isostream-interview-macos-arm64.zip`.
3. In Finder, press **⇧⌘G** and go to `~/Library/Application Support/obs-studio/plugins`.
   Create the `plugins` folder if it doesn't exist.
4. Copy `obs-plugin/isostream-interview.plugin` into that folder.
5. macOS marks downloaded files as untrusted, which can stop OBS from loading the plugin. Clear that
   mark once by pasting this into **Terminal**:
   ```bash
   xattr -dr com.apple.quarantine ~/Library/Application\ Support/obs-studio/plugins/isostream-interview.plugin
   ```
6. Open OBS. The **Interview** panel appears on the right.
   If you don't see it, use **Tools → ISOStream Interview → Show Interview dock**.

### 3. Turn on Browser Source Hardware Acceleration

Guest video plays inside OBS browser sources. With hardware acceleration **on**, your graphics card
decodes the video. Guests then look smooth, and OBS stays light on the processor. With it **off**,
expect choppy video and high CPU use.

![OBS Settings → Advanced → Sources → Enable Browser Source Hardware Acceleration](docs/images/obs-hardware-acceleration.svg)

1. Open **Settings**:
   - **Mac:** OBS menu → **Settings** (or **⌘,**)
   - **Windows / Linux:** **File → Settings**
2. Click **Advanced** in the list on the left.
3. Scroll to **Sources** and tick **Enable Browser Source Hardware Acceleration**.
4. Click **OK**, then **quit and reopen OBS**. The setting only takes effect after a restart.

> Is the option missing or greyed out? Your OBS was built without browser support. Install OBS from
> [obsproject.com](https://obsproject.com) instead.

### 4. Connect and start an interview

![Interview dock: Connect account → approve the code → pick a show → Start interview and copy the guest link](docs/images/dock-connect.svg)

1. In the **Interview** panel, click **Connect account**. Your normal web browser opens.
2. Sign in with Google or email, then check that the code matches the one in OBS. Click **Approve**.
   The panel connects by itself within a few seconds.
3. Pick a show, or click **New show**. Each show keeps **one permanent guest link**, so you can send
   the same link every episode.
4. Click **Start interview**, then **Copy** the guest link and send it to your guests.
   - Guests wait in a waiting room until you click **Bring on** on their camera tile.
   - Click **+** on a tile to place that person in your current scene. If the scene has a vertical
     twin, the vertical version is filled too.
   - When you finish, click **End interview**. Every guest is removed and nothing keeps running.

**Room connection settings.** All of these are in OBS under **Tools → ISOStream Interview**:

| Setting | When to change it |
|---|---|
| **Server address…** | Only if you host your own ISOStream Interview Mode server. Enter its `https://` address. Leave it empty to use the default hosted service. |
| **Host key…** | Only if you were given a studio key instead of an account. Paste it here or under *I have a studio key* in the panel. |
| **Back to my shows** | Return to the show list. |
| **Reload Interview dock** | Use if the panel looks stuck after a network drop. |

**Plans.** The Free plan allows 2 guests on air and 45-minute interviews. **Creator** adds 4 guests,
chat and spotlight. **Studio Pro** adds 8 guests, separate (ISO) audio per guest and a program return
feed. To change plans, click **Upgrade** in the panel.

---

## Using the bridge instead of the plugin

Use the bridge on Windows, Linux and Intel Macs, or wherever you can't install the plugin. It is a
small program that lets the ISOStream Interview Mode control room switch your OBS scenes over OBS's built-in
WebSocket.

1. In OBS, go to **Tools → WebSocket Server Settings**. Tick **Enable WebSocket server**, then click
   **Show Connect Info** and note the **port** and **password**.
2. Unzip the download. In the folder, rename `.env.example` to `.env` and open it in a text editor:
   ```ini
   OBS_WEBSOCKET_URL=ws://localhost:4455      # change 4455 if your port differs
   OBS_WEBSOCKET_PASSWORD=your-obs-password
   PUBLIC_BASE_URL=https://…                  # your studio site address
   ```
   Keep `.env` in the **same folder** as the program.
3. Start the bridge:
   - **Windows:** double-click `isostream-bridge.exe`. If SmartScreen warns you, click
     **More info → Run anyway**.
   - **Mac:** in Terminal, run `./isostream-bridge`. The first time, right-click it → **Open** to allow it.
   - **Linux:** in a terminal, run `./isostream-bridge`.
4. When it works, the bridge prints `listening on ws://localhost:4456` and `connected to ws://localhost:4455`.
   Keep the window open during the show.

Also turn on [Browser Source Hardware Acceleration](#3-turn-on-browser-source-hardware-acceleration).
It matters just as much with the bridge.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| No **Interview** panel | **Tools → ISOStream Interview → Show Interview dock**. Still nothing? Re-check step 2, including the Terminal command. |
| Guest video is choppy, or OBS uses a lot of CPU | Turn on hardware acceleration (step 3) and restart OBS. |
| Panel says it can't reach the server | Check your internet connection, then **Tools → ISOStream Interview → Reload Interview dock**. |
| "Connect account" never finishes | The code expires after 10 minutes. Click **Connect account** again and approve the new code. |
| A guest can't be heard | Look at their tile's meter. If it is grey, the guest is muted: click their mic icon to ask them to unmute. |
| Bridge says `connected` never appears | The WebSocket server is off in OBS, or the password or port in `.env` is wrong. |

Every download has a `READ-ME-FIRST.txt` with the short version of these steps. Each release also
lists SHA-256 checksums, so you can check that your download is complete.
