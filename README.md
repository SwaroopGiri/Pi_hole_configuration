# How to configure network-wide adblocking with Pi-Hole using raspberry pi

### Important informaiton
This tutorial starts assuming that you have a raspberry pi setup with raspberry pi OS and ssh enabled on it. If you're completely new to this and have a raspberry pi straight out of box, just google on how to setup a headless raspberry pi and follow instructions mentioned in first couple of links.

### My hardware
- <a href="https://www.raspberrypi.org/products/raspberry-pi-4-model-b/">Raspberry Pi 4 Computer Model B, 2GB RAM</a>
- <a href="https://www.raspberrypi.org/products/type-c-power-supply/">Type-C PowerSupply</a>
- <a href="https://shop.westerndigital.com/products/memory-cards/sandisk-ultra-uhs-i-microsd#SDSQUNC-016G-AN6MA">SanDisk Ultra microSDHC A1 UHS-I U3 Card + Adapter 16GB</a>
- <a href="https://www.raspberrypi.org/products/raspberry-pi-4-case/">Raspberry Pi 4 Model B Case</a>
- <a href="https://www.raspberrypi.org/products/raspberry-pi-4-case-fan/">Raspberry Pi 4 Model B Case Fan</a>

### Raspberry Pi 4 Preparation
1. Now we need to update all of the packages if you haven't done so during the desktop GUI configuration process. Type:
```
sudo apt update
sudo apt dist-upgrade
```
Wait for the updates to complete. Reboot after the updates, so type **sudo reboot**.
2. Update Raspberry Pi 4 EEPROM (Firmware)

From time to time new Raspberry pi 4 EEPROM (firmware) may be available. This procedure will install an auto-updater and keep you on the latest firmware version. Run these commands below. If it says update required, then merely reboot your RPI with **sudo reboot** and the update will be installed. 

```
sudo apt update
sudo apt full-upgrade
sudo apt install rpi-eeprom
sudo rpi-eeprom-update
```
3. Package Removal

Libreoffice and Chromium are space hog on the Raspberry Pi 4 if you've installed OS with recommended software downloads and most likely you won't need it. Plus this makes your backups larger, so let's get rid of it. If you need it, please skip this section.

```
sudo apt remove --purge libreoffice*
sudo apt remove --purge chromium-browser
sudo apt clean
sudo apt autoremove
```
### Installing and Configuring Pi-Hole

