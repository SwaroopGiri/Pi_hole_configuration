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

SSH into your RPi and type:
```
curl -sSL https://install.pi-hole.net | bash
```
Walkthrough the text-based wizard and accept all of the default values. When it asks you for which DNS server to use, select one that you feel most comfortable with. If you're planning to run your own recursive DNS server using unbound, select a temporary DNS and we will later change it to custom IP/Port while configuring unbound.
Make sure to note down the admin console password at the very end of the installer wizard.

There are a lot of blocklists out there, below are a few that should get you around 2M blocked domains. Login to Pi-Hole (http://YourIP/admin), click on Group Management, then adlists. Paste below list all at once and click on 'Add'. 

```
https://blocklist.site/app/dl/malware
https://blocklist.site/app/dl/ransomware
https://blocklist.site/app/dl/tracking
https://blocklist.site/app/dl/fraud
https://blocklist.site/app/dl/phishing
https://v.firebog.net/hosts/AdguardDNS.txt
https://hosts-file.net/grm.txt
https://reddestdream.github.io/Projects/MinimalHosts/etc/MinimalHostsBlocker/minimalhosts
https://raw.githubusercontent.com/StevenBlack/hosts/master/data/KADhosts/hosts
https://raw.githubusercontent.com/StevenBlack/hosts/master/data/add.Spam/hosts
https://v.firebog.net/hosts/static/w3kbl.txt
https://v.firebog.net/hosts/BillStearns.txt
https://adaway.org/hosts.txt
https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt
https://v.firebog.net/hosts/Easyprivacy.txt
https://raw.githubusercontent.com/quidsup/notrack/master/trackers.txt
https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt
https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/SmartTV.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_malvertising.txt
https://www.malwaredomainlist.com/hostslist/hosts.txt
https://dbl.oisd.nl/
https://openphish.com/feed.txt
http://sysctl.org/cameleon/hosts
https://s3.amazonaws.com/lists.disconnect.me/simple_ad.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_tracking.txt
https://hosts-file.net/ad_servers.txt
https://www.squidblacklist.org/downloads/dg-ads.acl
https://pgl.yoyo.org/adservers/serverlist.php?hostformat=nohtml
https://ransomwaretracker.abuse.ch/downloads/RW_DOMBL.txt
https://ransomwaretracker.abuse.ch/downloads/RW_URLBL.txt
http://theantisocialengineer.com/AntiSocial_Blacklist_Community_V1.txt
https://osint.bambenekconsulting.com/feeds/c2-dommasterlist-high.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_malware.txt
https://isc.sans.edu/feeds/suspiciousdomains_High.txt
https://mirror1.malwaredomains.com/files/justdomains
https://mirror1.malwaredomains.com/files/immortal_domains.txt
http://winhelp2002.mvps.org/hosts.txt
https://www.stopforumspam.com/downloads/toxic_domains_whole.txt
https://raw.githubusercontent.com/Dawsey21/Lists/master/main-blacklist.txt
https://someonewhocares.org/hosts/hosts
https://heuristicsecurity.com/dohservers.txt
https://phishing.army/download/phishing_army_blocklist_extended.txt
https://raw.githubusercontent.com/oneoffdallas/dohservers/master/list.txt
https://raw.githubusercontent.com/CHEF-KOCH/Audio-fingerprint-pages/master/AudioFp.txt
https://raw.githubusercontent.com/CHEF-KOCH/Canvas-fingerprinting-pages/master/Canvas.txt
https://raw.githubusercontent.com/CHEF-KOCH/WebRTC-tracking/master/WebRTC.txt
https://www.sunshine.it/blacklist.txt
```
