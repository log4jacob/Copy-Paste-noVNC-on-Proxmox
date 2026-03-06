# Copy-Paste-noVNC-on-Proxmox

Script that allows you to paste clipboard text from your host machine into a Proxmox VM through the web-based noVNC console by middle-clicking anywhere inside the VM display window.

---

## Features

- Paste **from host clipboard into VM**
- Triggered by **middle mouse button click**
- Works in Proxmox's built-in **noVNC web console**

---

## What Problem Does This Solve?

Typing long commands, scripts, passwords, or config files into a VM through a slow web console is a pain. This script simulates keystrokes into the VM as if you typed them.

This script only pastes **from host to VM**. It does not support copying from VM to host.

---

## How It Works

- Waits for the noVNC `<canvas>` to load in the web GUI
- Listens for **middle-clicks** inside the VM display
- On click, reads your system clipboard (with permission)
- Simulates each character via keystrokes into the VM

---

## Setup Instructions

### Step 1: Add the Script

#### Option A: Run Manually (Temporary)

1. Open your VM's noVNC console
2. Open DevTools Console (`F12` or `Cmd+Opt+I`)
3. Paste the contents of [`paste-script.user.js`](./paste-script.user.js) into the console
4. Hit Enter — it will wait for middle-click

---

## Using the Script

1. Copy text to your clipboard on the host machine (e.g., a command)
2. Go to the VM's noVNC browser tab
3. Click inside the VM to focus it
4. **Middle-click** (scroll wheel click) to paste

The text will be typed into the VM window.

---

## Limitations

- Only pastes **from host to VM**, not the other way
---

## Security & Permissions

- The script reads your clipboard **only on middle-click**
- **Absolutely nothing** is sent externally
- Runs entirely within your browser
- Only affects Proxmox noVNC pages

---

## Feedback

Open an issue or fork the repo to improve or customize it further.

---

