# 🖱️ Copy-Paste-noVNC-on-Proxmox

A simple browser script that allows you to paste text **from your host machine to a Proxmox VM** using the noVNC web console — with a **middle mouse click**.

> No more manually typing long commands or passwords into a slow VM terminal. Just copy on your host, middle-click in the VM, and paste like magic.

---

## 📌 What This Script Does

- ✅ Reads clipboard text from your host machine (Mac, Windows, Linux)
- ✅ Detects the noVNC canvas inside Proxmox's web UI
- ✅ Waits for a **middle mouse button click** on the VM window
- ✅ Simulates real keystrokes into the VM console
- ❌ Does **not** copy anything **from** the VM — it's one-way only

---

## 🧑‍💻 Who Is This For?

- You're using **Proxmox VE** and regularly access VMs via the **web-based noVNC console**
- You want a **quick, hands-free way to paste** long commands, passwords, or config
- You're comfortable copying a script or using a browser extension like **Tampermonkey**

---

## 🔧 Setup Instructions (Step-by-Step)

### ✅ Prerequisites

| Requirement                     | Why It’s Needed                         |
|-------------------------------|------------------------------------------|
| Proxmox + VM running          | Your target environment                 |
| Modern browser (Chrome, Brave)| Required for clipboard access           |
| Middle-click support          | Default trigger for the paste command   |
| Userscript Manager (Optional) | For making the script persistent        |

---

### 🔹 Step 1: Choose How You Want to Use It

#### 💡 Option A — One-Time Use (Quick Test)
Paste the script manually:

1. Open your Proxmox VM’s noVNC console in the browser
2. Press `F12` or `Ctrl+Shift+I` to open DevTools → Console
3. Paste the script (from below) and press Enter
4. Now middle-click inside the VM window to paste clipboard content

#### 💡 Option B — Persistent (Recommended)
Install a **userscript extension**:

| Browser     | Userscript Manager       | Link                                                |
|-------------|---------------------------|-----------------------------------------------------|
| Chrome/Brave| [Tampermonkey](https://tampermonkey.net) |
| Firefox     | [Violentmonkey](https://violentmonkey.github.io) |
| Safari (Mac)| [Userscripts App](https://apps.apple.com/us/app/userscripts/id1463298887) |

1. Install the extension
2. Create a new script
3. Paste the script and metadata (provided below)
4. Save — it now runs automatically on all noVNC consoles

---

## ✏️ The Script

Paste this into DevTools (Option A) or Tampermonkey (Option B):

```javascript
// ==UserScript==
// @name         noVNC Clipboard Paste via Middle Click
// @namespace    https://yourdomain.dev/
// @version      1.0
// @description  Paste host clipboard to noVNC canvas on middle click
// @author       You
// @match        *://*/?console=kvm*
// @grant        clipboardRead
// @run-at       document-idle
// ==/UserScript==

(function () {
  const CANVAS_SELECTOR = "canvas";
  const MIDDLE_MOUSE_BUTTON = 1;

  let canvas;

  function waitForCanvas() {
    canvas = document.querySelector(CANVAS_SELECTOR);

    if (canvas) {
      initCanvas();
    } else {
      console.log("NoVNC canvas not found. Retrying...");
      setTimeout(waitForCanvas, 1000);
    }
  }

  function initCanvas() {
    console.log("NoVNC detected. Middle-click paste enabled.");
    canvas.id = "canvas-id";
    canvas.addEventListener("mousedown", handleMouseDown);
  }

  function handleMouseDown(event) {
    if (event.button === MIDDLE_MOUSE_BUTTON) {
      console.log("Middle-click detected. Pasting clipboard content...");
      event.preventDefault();
      pasteClipboardContent();
    }
  }

  async function pasteClipboardContent() {
    try {
      const text = await navigator.clipboard.readText();
      sendString(text);
    } catch (error) {
      console.error("Clipboard read failed:", error);
    }
  }

  function sendString(text) {
    text.split("").forEach((char, index) => {
      setTimeout(() => {
        const needsShift = /[A-Z!@#$%^&*()_+{}:"<>?~|]/.test(char);
        const event = new KeyboardEvent("keydown", { key: char, shiftKey: needsShift });

        if (needsShift) {
          const shiftDownEvent = new KeyboardEvent("keydown", { keyCode: 16 });
          canvas.dispatchEvent(shiftDownEvent);
        }

        canvas.dispatchEvent(event);

        if (needsShift) {
          const shiftUpEvent = new KeyboardEvent("keyup", { keyCode: 16 });
          canvas.dispatchEvent(shiftUpEvent);
        }

        const keyUpEvent = new KeyboardEvent("keyup", { key: char });
        canvas.dispatchEvent(keyUpEvent);
      }, index * 10);
    });
  }

  waitForCanvas();
})();
