# TOE Platform — Installation Guide
*Raspberry Pi 5 (ARM64) · Headless Setup*
*One command per step. Copy and paste each line individually.*

---

## Prerequisites
- RPi5 connected via SSH
- Pixel on same local network as RPi5
- Internet connection on RPi5 for initial setup only

---

## PHASE 1 — System Preparation

### Step 1 — Update system packages
```bash
sudo apt update && sudo apt upgrade -y
```

### Step 2 — Install core system dependencies
```bash
sudo apt install -y python3 python3-pip python3-venv git ffmpeg redis-server tesseract-ocr tesseract-ocr-eng curl wget build-essential libatlas-base-dev
```

### Step 3 — Install OpenCV dependencies
```bash
sudo apt install -y libopencv-dev python3-opencv libhdf5-dev libhdf5-serial-dev
```

### Step 4 — Install audio dependencies
```bash
sudo apt install -y libsndfile1 portaudio19-dev libffi-dev libssl-dev
```

### Step 5 — Enable and start Redis
```bash
sudo systemctl enable redis-server && sudo systemctl start redis-server
```

### Step 6 — Create project directory
```bash
mkdir -p /home/$USER/toe-platform && cd /home/$USER/toe-platform
```

### Step 7 — Create Python virtual environment
```bash
python3 -m venv venv
```

### Step 8 — Activate virtual environment
```bash
source /home/$USER/toe-platform/venv/bin/activate
```

### Step 9 — Upgrade pip
```bash
pip install --upgrade pip
```

---

## PHASE 2 — Python Packages

### Step 10 — Install Flask and task queue
```bash
pip install flask flask-cors redis rq
```

### Step 11 — Install video processing
```bash
pip install pyav opencv-python-headless
```

### Step 12 — Install book parsing libraries
```bash
pip install pymupdf ebooklib python-docx pytesseract Pillow
```

### Step 13 — Install audio analysis
```bash
pip install librosa soundfile
```

### Step 14 — Install Whisper (lyrics transcription)
```bash
pip install openai-whisper
```

### Step 15 — Install yt-dlp (YouTube extraction)
```bash
pip install yt-dlp
```

### Step 16 — Install astrology engine
```bash
pip install pyswisseph kerykeion
```

### Step 17 — Install HTTP client and utilities
```bash
pip install httpx python-dotenv pyyaml schedule GitPython
```

### Step 18 — Install rclone (vault sync to Google Drive)
```bash
curl https://rclone.org/install.sh | sudo bash
```

---

## PHASE 3 — Project Structure

### Step 19 — Create module directories
```bash
mkdir -p /home/$USER/toe-platform/modules/{vision,codex,oracle,sovereign,resonance}
```

### Step 20 — Create vault output directories
```bash
mkdir -p /home/$USER/toe-platform/vault_output/{vision,codex,oracle,sovereign,resonance}
```

### Step 21 — Create inbox directories
```bash
mkdir -p /home/$USER/toe-platform/inbox/{video,books,audio}
```

### Step 22 — Create static and template directories
```bash
mkdir -p /home/$USER/toe-platform/{static,templates,scheduler}
```

### Step 23 — Initialize git repository
```bash
cd /home/$USER/toe-platform && git init
```

---

## PHASE 4 — Pixel Setup (LLM Inference)
*Do these steps on your Pixel (GrapheneOS) in Termux*

### Step 24 — Install Termux from F-Droid
*(Manual — install F-Droid, then install Termux from F-Droid store)*

### Step 25 — Update Termux packages
```bash
pkg update && pkg upgrade -y
```

### Step 26 — Install build tools in Termux
```bash
pkg install -y clang cmake git python
```

### Step 27 — Clone llama.cpp in Termux
```bash
git clone https://github.com/ggerganov/llama.cpp && cd llama.cpp
```

### Step 28 — Build llama.cpp in Termux
```bash
cmake -B build && cmake --build build --config Release -j4
```

### Step 29 — Download LLaVA vision model (on Pixel, ~4GB)
```bash
wget -O llava-7b-q4.gguf https://huggingface.co/mys/ggml_llava-v1.5-7b/resolve/main/ggml-model-q4_k.gguf
```

### Step 30 — Download LLaVA vision projector (on Pixel)
```bash
wget -O mmproj-llava.gguf https://huggingface.co/mys/ggml_llava-v1.5-7b/resolve/main/mmproj-model-f16.gguf
```

### Step 31 — Start LLaVA server on Pixel (run this whenever using the platform)
```bash
./build/bin/llama-server -m llava-7b-q4.gguf --mmproj mmproj-llava.gguf --host 0.0.0.0 --port 8080
```

---

## PHASE 5 — Configuration

### Step 32 — Create config file on RPi5
```bash
nano /home/$USER/toe-platform/config.yaml
```
*Paste the following into nano, replace PIXEL_IP with your Pixel's local IP:*
```yaml
inference:
  primary: pixel
  pixel_host: "http://PIXEL_IP:8080"
  oracle_host: ""

vault:
  output_path: "/home/e031a/toe-platform/vault_output"
  gdrive_remote: "gdrive:toe"

scheduler:
  weekly_day: "sunday"
  weekly_time: "02:00"
```
*Save: Ctrl+X, then Y, then Enter*

### Step 33 — Find Pixel's local IP address (run on Pixel in Termux)
```bash
ip addr show wlan0 | grep inet
```

---

## PHASE 6 — Google Drive Sync Setup

### Step 34 — Configure rclone for Google Drive
```bash
rclone config
```
*Follow prompts: New remote → name it `gdrive` → Google Drive → follow browser auth*

### Step 35 — Test rclone connection
```bash
rclone ls gdrive:toe
```

---

## PHASE 7 — Run the Platform

### Step 36 — Activate virtual environment (every session)
```bash
source /home/$USER/toe-platform/venv/bin/activate
```

### Step 37 — Start Redis worker
```bash
cd /home/$USER/toe-platform && rq worker &
```

### Step 38 — Start Flask server
```bash
python app.py
```

### Step 39 — Access in browser (from any device on your network)
```
http://raspberrypi.local:5000
```

---

## PHASE 8 — Auto-start on Boot (Optional)

### Step 40 — Create systemd service
```bash
sudo nano /etc/systemd/system/toe-platform.service
```
*Paste this:*
```ini
[Unit]
Description=TOE Intelligence Platform
After=network.target redis-server.service

[Service]
User=e031a
WorkingDirectory=/home/e031a/toe-platform
ExecStart=/home/e031a/toe-platform/venv/bin/python app.py
Restart=always

[Install]
WantedBy=multi-user.target
```

### Step 41 — Enable and start the service
```bash
sudo systemctl enable toe-platform && sudo systemctl start toe-platform
```

---

## Troubleshooting

### Check Redis is running
```bash
sudo systemctl status redis-server
```

### Check platform logs
```bash
journalctl -u toe-platform -f
```

### Check Pixel inference server is reachable (from RPi5)
```bash
curl http://PIXEL_IP:8080/health
```

### Manually trigger vault sync
```bash
rclone sync /home/$USER/toe-platform/vault_output gdrive:toe
```

---

*Documentation version: 1.0*
*Platform: Raspberry Pi 5 ARM64 + Pixel (GrapheneOS)*
*Updated: 2026-05-10*
