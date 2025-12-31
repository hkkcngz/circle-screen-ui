```markdown
# 📊 circle-screen-ui
Raspberry Pi 4 and Waveshare 5inch_1080x1080_LCD office lights controller.

This project transforms a Raspberry Pi 4 with a round Waveshare display into a dedicated, high-performance office controller. It uses a minimal Linux setup (Kiosk Mode) to boot directly into a web-based UI without showing the desktop environment.

---

## 🛠 Preparation

### 1. Operating System Setup
Use [Raspberry Pi Imager](https://www.raspberrypi.com/software/) to install **Raspberry Pi OS Lite (64-bit)**. 

Before writing, click the **Edit Settings (Gear icon)**:
* **Hostname:** `kodmobpi`
* **Username/Password:** `pi` / `1234`
* **Wi-Fi:** Configure your SSID and password (select `TR` as country).
* **Services:** Enable **SSH** with password authentication.
* **Save** and **Write** to the SD card.

### 2. Display Configuration (config.txt)
After the programming is completed, open the `config.txt` file in the root directory of the SD card. Add the following code at the end of the file.

> **Important:** You must disable the default graphics driver to use custom HDMI timings on Pi 4.

```text
# --- Basic Display Settings ---
hdmi_force_hotplug=1
config_hdmi_boost=10
hdmi_group=2
hdmi_mode=87

# --- Waveshare 5" Round LCD Timings (1080x1080) ---
hdmi_timings=1080 0 68 32 100 1080 0 12 4 16 0 0 0 60 0 85500000 0

# --- Disable Default KMS Driver (Mandatory for Pi 4) ---
# dtoverlay=vc4-kms-v3d
# display_auto_detect=1

# --- Power & Performance ---
max_usb_current=1
hdmi_drive=2
arm_64bit=1
disable_overscan=1

```

---

## 🚀 Post-Boot Setup

### 3. Bypass "Socket Interaction" Message

If the screen hangs at boot logs, you need to enable **Auto-Login** and **Legacy Drivers**. Connect via SSH (Putty/Terminal):

```bash
sudo raspi-config

```

1. Navigate to **System Options** -> **Boot / Auto Login**.
2. Select **Desktop Autologin** (or Console Autologin).
3. Go to **Advanced Options** -> **GL Driver** and ensure **Legacy** is selected.
4. Finish and Reboot.

### 4. Install Kiosk Environment

Run the following commands to install the minimal UI wrapper and Chromium browser:

```bash
# Update and install core packages
sudo apt update
sudo apt install -y --no-install-recommends xserver-xorg x11-xserver-utils xinit openbox chromium

# Create the Kiosk launch script
cat <<EOF > ~/.xinitrc
# Disable screensaver and power management
xset s off
xset -dpms
xset s noblank

# Launch Chromium in Kiosk Mode
# Replace the URL with your local file path (e.g., file:///home/pi/index.html)
exec chromium --kiosk --noerrdialogs --disable-infobars --check-for-update-interval=31536000 --disable-pinch [https://www.google.com](https://www.google.com)
EOF

# Enable auto-start on login
if ! grep -q "startx" ~/.bash_profile; then
  echo 'if [ -z "$DISPLAY" ] && [ $(tty) = /dev/tty1 ]; then startx; fi' >> ~/.bash_profile
fi

sudo reboot

```

---

## 📂 Deploying Your Application

1. Transfer your `index.html` and assets to `/home/pi/` using WinSCP or FileZilla.
2. Update the URL in `~/.xinitrc`:
`exec chromium --kiosk file:///home/pi/index.html`
3. Restart: `sudo reboot`

## ⚠️ Notes

* **Power:** Always use a **5.1V / 3A** power adapter. Laptop USB ports will trigger "Undervoltage" warnings and system instability.
* **Port:** Use **HDMI-0** (the micro-HDMI port closest to the USB-C power input).

## 🔗 References

* **Official Wiki:** [Waveshare 5inch 1080x1080 LCD Guide](https://www.waveshare.com/wiki/5inch_1080x1080_LCD)

Hakkı Cengiz
01.01.2026
