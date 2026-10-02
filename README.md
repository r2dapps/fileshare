# 📡 Offline QR File Transfer (Air-Gapped 2MB Protocol)

[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](LICENSE)
[![Zero Backend](https://img.shields.io/badge/Backend-None%20(Pure%20Client--Side)-111111.svg)](#)
[![Offline Ready](https://img.shields.io/badge/Offline-Air--Gapped%20Ready-emerald.svg)](#)
[![Max File Size](https://img.shields.io/badge/File%20Size-Up%20to%202MB-blue.svg)](#)

A zero-dependency, serverless, air-gapped file sharing utility designed to transfer files up to 2MB between devices using high-density animated QR code streams and camera optical scanning.

No Wi-Fi, Bluetooth, local area network, cables, or cloud servers required. Works entirely within the browser via optical line-of-sight.

---

## 🚀 Key Features

- **🔒 100% Air-Gapped & Offline**: Zero network calls, zero server telemetry, and no intermediary storage. Data moves strictly from display to camera sensor.
- **📦 Sequential Chunking Engine**: Files up to 2MB are encoded in base64, packaged with metadata, and divided into dense 450-byte chunks optimized for rapid QR recognition.
- **⏯️ Dynamic Transmission Control**:
  - Auto-play broadcast loop with configurable frame intervals (0.5s – 2.5s).
  - Manual Next/Previous chunk stepping.
  - Scrub slider for instant navigation across the packet stream.
- **📷 Continuous Optical Receiver**:
  - Live device camera viewfinder powered by `html5-qrcode`.
  - Non-sequential packet reception (chunks are tracked by index, tolerating frame skips).
  - Real-time progress bar and packet counter.
  - Instant file reassembly and automatic download trigger upon completion.
- **🎨 Minimalist Monochromatic UI**: Clean, responsive, brutalist-inspired interface styled with Tailwind CSS.

---

## 🧠 How It Works (The Protocol)

```
[Sender Device]                                            [Receiver Device]
 +-------------+                                            +---------------+
 | Select File |                                            | Open Camera   |
 +------+------+                                            +-------+-------+
        |                                                           |
 [Read as Base64]                                                   |
        |                                                           |
 [Wrap Metadata JSON]                                               |
  { n: name, t: type, d: data }                                     |
        |                                                           |
 [Slice into 450B Chunks]                                           |
        |                                                           |
 [Packet Format]                                                    |
  TX|<idx>|<total>|<chunk>                                          |
        |                                                           |
 [Animated QR Broadcast] ======= Optical Line-of-Sight ======> [Camera Scan]
                                                                    |
                                                           [Store Chunk Map]
                                                                    |
                                                          [Check: All Received?]
                                                                    |
                                                          [Reconstruct & Download]
```

### Packet Structure
Each QR code carries a framed packet string:
```text
TX|<chunk_index>|<total_chunks>|<chunk_payload>
```
- `TX`: Protocol header identifier.
- `<chunk_index>`: 1-based index of the current chunk.
- `<total_chunks>`: Total number of frames in the sequence.
- `<chunk_payload>`: Slice of the serialized JSON file payload.

Because the receiver indexes chunks by their ID, the receiver can start scanning at any point in the cycle and will safely collect missing frames as the broadcast loops.

---

## 🛠️ Tech Stack

- **HTML5 & Vanilla JavaScript**: Core logic, FileReader API, Blob/DataURL handling.
- **[Tailwind CSS](https://tailwindcss.com/)**: Utility-first styling.
- **[QRious](https://github.com/neocotic/qrious)**: High-performance client-side QR generation onto HTML5 Canvas.
- **[HTML5-QRCode](https://github.com/mebjas/html5-qrcode)**: Cross-platform camera scanner and barcode reader.

---

## 💻 Quick Start & Usage

### 1. Run Directly in Browser
You don't need any build steps or npm installations. Simply open [`index.html`](file:///d:/ReactApps2Git/QR_2mbFile_Sharing/index.html) in any modern web browser:
- Double-click `index.html`, or
- Use a lightweight static file server:

```bash
# Using Node.js npx
npx serve .

# Or using Python 3
python -m http.server 8080
```

> **Note on Camera Access**: Modern browsers require either `localhost` or an `HTTPS` connection (such as GitHub Pages) to grant webcam/mobile camera permissions for the **Receive** mode.

### 2. Transmitting a File (Sender)
1. Select the **Send** tab.
2. Drag and drop any file (images, PDFs, keys, configs, archives) up to **2MB**.
3. Adjust the frame interval slider if necessary (default: 1.5s/frame).
4. Direct the receiving device's camera towards the QR display.

### 3. Receiving a File (Receiver)
1. Select the **Receive** tab on the destination device.
2. Click **Start Camera Scanner** and grant camera permissions.
3. Aim the camera at the sender's QR sequence.
4. Watch the progress bar fill as frames are recognized.
5. Once 100% of chunks are collected, the file automatically reassembles and prompts a download.

---

## 💡 Tips for Best Performance

- **Screen Brightness**: Increase the screen brightness of the sending device to maximize contrast.
- **Optimal Distance**: Keep the camera at a distance where the QR code fills approximately 50–70% of the camera viewfinder.
- **Frame Rate**: If the receiver's camera has lower FPS or struggles with motion blur, increase frame delay to `1.5s` - `2.0s`.
- **Reflections**: Avoid glare on glossy screens by angling the receiving camera slightly.

---

## 🌐 Deploy to GitHub Pages

1. Push this repository to GitHub.
2. Go to repository **Settings** > **Pages**.
3. Under **Build and deployment** > **Source**, choose **Deploy from a branch**.
4. Select `main` branch and `/ (root)` folder, then click **Save**.
5. Your air-gapped transfer tool will be live over HTTPS with full camera support!

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
