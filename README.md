# Copy-Paste-noVNC-on-Proxmox

A simple script that allows you to paste clipboard text from your host machine into a Proxmox VM through the web-based noVNC console by middle-clicking anywhere inside the VM display window.

---

## ✨ Features

- Paste **from host clipboard into VM**
- Triggered by **middle mouse button click**
- Works in Proxmox's built-in **noVNC web console**
- Can be used **manually** or installed as a **persistent userscript**

---

## 🔍 What Problem Does This Solve?

Typing long commands, scripts, passwords, or config files into a VM through a slow web console is a pain. This script simulates keystrokes into the VM as if you typed them, saving time and effort.

This script only pastes **from host to VM**. It does not support copying from VM to host.

---

## 🔄 How It Works

- Waits for the noVNC `<canvas>` to load in the web GUI
- Listens for **middle-clicks** inside the VM display
- On click, reads your system clipboard (with permission)
- Simulates each character via keystrokes into the VM

---

## ⚙️ Important Caveats

This is a **client-side hack** — a clever workaround that simulates typing by injecting keypress events into the noVNC canvas. It’s not officially supported by Proxmox or noVNC.

- It does **not** interact with the VM's internal clipboard.
- It simply "types" the text into the VM, one keystroke at a time — just like a human would.
- Use it for **quick tasks** or convenience — not mission-critical workflows.

> If you're looking for something officially supported or bulletproof, this isn't it.  
> But for fast-and-dirty pasting, it works great.

---

## 🔧 Setup Instructions

### Step 1: Install a Userscript Manager (Optional but Recommended)

To make the script persistent:

| Browser      | Extension                                                                 |
| ------------ | ------------------------------------------------------------------------- |
| Chrome/Brave | [Tampermonkey](https://tampermonkey.net)                                  |
| Firefox      | [Violentmonkey](https://violentmonkey.github.io)                          |
| Safari (Mac) | [Userscripts App](https://apps.apple.com/us/app/userscripts/id1463298887) |

### Step 2: Add the Script

You have two options:

#### Option A: Run Manually (Temporary)

1. Open your VM's noVNC console
2. Open DevTools Console (`F12` or `Cmd+Opt+I`)
3. Paste the contents of [`paste-script.user.js`](./paste-script.user.js) into the console
4. Hit Enter — it will wait for middle-click

#### Option B: Install as Userscript (Persistent)

1. Open Tampermonkey or other userscript manager
2. Create a new script
3. Paste in the contents of [`paste-script.user.js`](./paste-script.user.js)
4. Save — the script will auto-run on matching noVNC pages

---

## 🔜 Using the Script

1. Copy text to your clipboard on the host machine (e.g., a command)
2. Go to the VM's noVNC browser tab
3. Click inside the VM to focus it
4. **Middle-click** (scroll wheel click) to paste

The text will be typed into the VM window, one character at a time.

---

## ⚠️ Limitations

- Only pastes **from host to VM**, not the other way
- Requires browser permission to read clipboard
- Works best in Chrome-based browsers
- Some special characters may not type correctly depending on keyboard layout

---

## 🔒 Security & Permissions

- The script reads your clipboard **only on middle-click**
- Nothing is sent externally
- Runs entirely within your browser
- Only affects Proxmox noVNC pages

---

## 🚀 Advanced: Customize the Trigger

Don’t like middle-click? You can modify the script to use keyboard shortcuts instead. Here’s how to change it to trigger on `Cmd + Shift + V` (on Mac) or `Ctrl + Shift + V` (on Windows/Linux):

1. Replace the `canvas.addEventListener("mousedown", handleMouseDown);` line with:

```js
document.addEventListener("keydown", function (e) {
  const isMac = navigator.platform.toUpperCase().indexOf("MAC") >= 0;
  const cmdOrCtrl = isMac ? e.metaKey : e.ctrlKey;

  if (cmdOrCtrl && e.shiftKey && e.key === "v") {
    e.preventDefault();
    pasteClipboardContent();
  }
});

---

## 📚 License

MIT — do what you want, just don’t paste into production by accident. 😅

---

## ✉️ Feedback

Open an issue or fork the repo to improve or customize it further.

---

## 🔗 Related Files

- [`paste-script.user.js`](./paste-script.user.js): The actual JavaScript userscript used to enable paste functionality.

