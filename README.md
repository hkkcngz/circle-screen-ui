# circle-screen-ui
Raspberry pi 4 and Waveshare 5inch_1080x1080_LCD office lights controller. 


## Prepare
###  1. Install Raspberry Pi OS Lite 64Bit with Raspberry Pi Imager,

  - EDIT SETTINGS (Edit Settings - Gear icon in the bottom right):
  - General Tab:
  - Hostname: kodmobpi (remember this).
  - Set username and password: Use 'pi' as your username and something easy to use as your password (e.g., 1234).
  - Configure Wireless LAN: Enter your Wi-Fi name and password, checking them carefully. (Pay attention to uppercase/lowercase letters!).
  - Wireless LAN country: Set to TR.
  - Services Tab:
  - Check the "Enable SSH" box and select "Use password authentication".
  - Click the SAVE and WRITE buttons.

###  2. Edit config.txt from PC, 

  ``# Temel Görüntü Ayarları
hdmi_force_hotplug=1
config_hdmi_boost=10
hdmi_group=2
hdmi_mode=87

# Senin Ekranının Özel Zamanlaması
hdmi_timings=1080 0 68 32 100 1080 0 12 4 16 0 0 0 60 0 85500000 0

# Pi 4 için Grafik Sürücüsünü Devre Dışı Bırak (Bu çok önemli!)
# dtoverlay=vc4-kms-v3d  <-- Bu satırı sildik veya yorum yaptık
# display_auto_detect=1 <-- Bunu da sildik

# Ekranın akım çekebilmesi için
max_usb_current=1
hdmi_drive=2

# Diğer Zorunlu Ayarlar
arm_64bit=1
disable_overscan=1``

### 3. Connect to Pi with Putty, IP Adress from modem dashboard. 
Test to Bypass the "Socket Interaction" Message
If you still see that message on the screen, it means the system is actually booting but cannot start the desktop. Connect via SSH and enter the following command:

``sudo raspi-config``
In the menu that opens:
  - Follow the path System Options -> Boot / Auto Login.
  - Select the Desktop Autologin (or Console Autologin) option.
  - Then go to Advanced Options -> GL Driver and make sure the Legacy option is selected.


### 4. Install Chromium and Auto run app settings
Connect to Pi via Putty
``# 1. Sistem paket listesini güncelle ve gerekli araçları kur
sudo apt update
sudo apt install -y --no-install-recommends xserver-xorg x11-xserver-utils xinit openbox chromium

# 2. Grafik arayüzü başlatıldığında neyin çalışacağını belirleyen .xinitrc dosyasını oluştur
cat <<EOF > ~/.xinitrc
# Ekran koruyucuyu ve uyku modunu kapat
xset s off
xset -dpms
xset s noblank

# Tarayıcıyı kiosk modunda (tam ekran) Google ile başlat
exec chromium --kiosk --noerrdialogs --disable-infobars --check-for-update-interval=31536000 --disable-pinch https://www.google.com
EOF

# 3. Kullanıcı giriş yaptığında (Login) grafik arayüzünün otomatik başlamasını sağla
# Eğer satır zaten varsa tekrar eklememesi için kontrol eder
if ! grep -q "startx" ~/.bash_profile; then
  echo 'if [ -z "$DISPLAY" ] && [ $(tty) = /dev/tty1 ]; then startx; fi' >> ~/.bash_profile
fi

# 4. Sistemi yeniden başlatarak kurulumu test et
sudo reboot``

- Change google.com with your own app adress
Switching to Your Own Code: To open your own app file instead of Google's, simply replace https://www.google.com in the .xinitrc file with the location where you uploaded the file (e.g., file:///home/pi/index.html).
