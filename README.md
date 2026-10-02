# 📡 Offline QR File Transfer (Air-Gapped 2MB Protocol)

[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](LICENSE)
[![Zero Backend](https://img.shields.io/badge/Backend-None%20(Pure%20Client--Side)-111111.svg)](#)
[![Offline Ready](https://img.shields.io/badge/Offline-Air--Gapped%20Ready-emerald.svg)](#)
[![Max File Size](https://img.shields.io/badge/File%20Size-Up%20to%202MB-blue.svg)](#)

A high-performance, zero-dependency, serverless, air-gapped file sharing utility designed to transfer files up to 2MB between devices using high-density animated QR code streams and camera optical scanning.

No Wi-Fi, Bluetooth, local area network, cables, or cloud servers required. Works entirely within the browser via optical line-of-sight.

---

## 🚀 Key Features

- **🔒 100% Air-Gapped & Offline**: Zero network calls, zero server telemetry, and no intermediary storage. Data moves strictly from display to camera sensor.
- **🗜️ Client-Side Gzip Compression**: Automatically compresses files using the browser's native `CompressionStream('gzip')` API before chunking, drastically shrinking payloads by 50%–80% and reducing required QR frames.
- **📦 Optimized 380B Density**: Slices data into 380-byte segments, creating Version 11 QR codes with large, distinct modules easily resolved by smartphone and webcam sensors.
- **⏯️ Dynamic Transmission Speed Controls**:
  - Speed presets: **Fast (400ms)**, **Balanced (750ms)**, **Safe (1.2s)**, and **Slow (2.0s)**.
  - Interactive continuous slider (250ms – 2500ms) with live FPS indicator.
  - Manual Next/Previous frame stepping and sequence scrub slider.
- **🎯 Targeted Frame Broadcast (Solves Coupon Collector Delay)**:
  - The receiver displays exact missing frame numbers (e.g. `4, 12, 18-22`) with a one-click **Copy Missing** button.
  - The sender includes a **Targeted Frame Broadcast** input: paste the missing frame numbers to loop *only* those frames, finishing incomplete transfers in seconds.
- **📷 High-Speed Optical Scanner with Hardware Acceleration**:
  - Live viewfinder automatically scaled to 85% of camera view.
  - Hardware-accelerated `BarcodeDetector` support with instant fallback.
  - Dual camera fallback support (environment/rear camera with automatic front webcam fallback).
- **🔊 Visual & Audio Feedback**:
  - Synthesized Web Audio API chime on new frame capture with mute toggle.
  - Visual emerald pulse border around viewfinder when new chunks are captured.
- **🎨 Minimalist Monochromatic UI**: Clean, responsive, brutalist-inspired interface styled with Tailwind CSS.

---

## 🧠 How It Works (The Protocol)

```
[Sender Device]                                            [Receiver Device]
 +-------------+                                            +---------------+
 | Select File |                                            | Open Camera   |
 +------+------+                                            +-------+-------+
        |                                                           |
 [Gzip Compression (Native)]                                        |
        |                                                           |
 [Wrap Metadata JSON]                                               |
  { n: name, t: type, s: size, z: 1, d: data }                      |
        |                                                           |
 [Slice into 380B Chunks]                                           |
        |                                                           |
 [Packet Format: OF2|<idx>|<total>|<chunk>]                         |
        |                                                           |
 [Animated QR Broadcast (400px)] ===== Optical Line-of-Sight =====> [Camera Scan]
                                                                    |
                                                           [Audio & Visual Pulse]
                                                                    |
                                                           [Track Missing Frames]
                                                                    |
                                                          [Check: All Received?]
                                                                    |
                                                      [Decompress & Auto-Download]
```

### Packet Structure
Each QR code carries a framed packet string:
```text
OF2|<chunk_index>|<total_chunks>|<chunk_payload>
```
- `OF2`: Offline File Protocol v2 identifier (backward-compatible with `OFFLINEFILE`).
- `<chunk_index>`: 1-based index of the current chunk.
- `<total_chunks>`: Total number of frames in the sequence.
- `<chunk_payload>`: Slice of the serialized metadata and compressed payload.

Because the receiver indexes chunks by their ID, the receiver can start scanning at any point in the cycle and will safely collect missing frames as the broadcast loops.

---

## 🛠️ Tech Stack

- **HTML5 & Vanilla JavaScript**: Core logic, FileReader API, Compression Streams, Web Audio API, Canvas.
- **[Tailwind CSS](https://tailwindcss.com/)**: Utility-first styling.
- **[QRious](https://github.com/neocotic/qrious)**: High-performance client-side QR generation onto HTML5 Canvas.
- **[HTML5-QRCode](https://github.com/mebjas/html5-qrcode)**: Cross-platform camera scanner and barcode reader with `BarcodeDetector` integration.

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
3. The app automatically compresses the file using native Gzip and calculates the optimal QR sequence.
4. Choose your transmission speed:
   - **Fast (400ms)** for high-end phone cameras in bright lighting.
   - **Balanced (750ms)** (recommended default).
   - **Safe (1.2s)** or **Slow (2.0s)** for webcams or lower-spec devices.
5. Direct the receiving device's camera towards the QR display.

### 3. Receiving a File (Receiver)
1. Select the **Receive** tab on the destination device.
2. Click **Start Camera Scanner** and grant camera permissions.
3. Aim the camera at the sender's QR sequence.
4. Watch the progress bar fill and hear the audio chimes as frames are recognized.
5. **Handling Missing Frames:**
   - If only a few frames are missing, click **Copy Missing**.
   - Paste those numbers into the sender's **Targeted Frame Broadcast** input and click **Filter**.
   - The sender will loop only those missing frames, completing transfer in seconds!
6. Once 100% of chunks are collected, the file automatically decompresses, verifies, and prompts a download.

---

## 💡 Tips for Best Performance

- **Screen Brightness**: Increase the screen brightness of the sending device to maximize contrast.
- **Optimal Distance**: Keep the camera at a distance where the QR code fills approximately 50–70% of the camera viewfinder.
- **Frame Rate**: If the receiver's camera has lower FPS or struggles with motion blur, switch to `Balanced (750ms)` or `Safe (1.2s)`.
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
