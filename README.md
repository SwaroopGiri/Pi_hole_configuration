# How to configure network-wide adblocker with Pi-Hole using raspberry pi

Pi-hole is a Linux network-level advertisement and Internet tracker blocking application which acts as a DNS sinkhole and optionally a DHCP server, intended for use on a private network. It is designed for low-power embedded devices with network capability, such as the Raspberry Pi, but supports any Linux machines.

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
Walk through the text-based wizard and accept all of the default values. 
Make sure to note down raspberry pi's static IP which will be displayed during configuration. We will need it for accessing Pi-hole's web interface and also to configure router to use Pi-hole. 
When it asks you for which DNS server to use, select one that you feel most comfortable with. If you're planning to run your own recursive DNS server with unbound, select a temporary DNS and we will later change it to custom IP/Port while configuring unbound in different section.
Make sure to note down the admin console password at the very end of the installer wizard.

### Configuring Router's DHCP settings to point to Pi-hole's DNS.

Login to admin panel of your router which usually are below IPs. 
`192.168.0.1 or 192.168.1.1`

Goto advanced settings, look for DHCP settings or DHCP Server.

Change primary DNS option in DHCP settings to your raspberrypi's static IP which you have configured in above step.


**Adding Adlists**

There are a lot of blocklists out there, below are a few that should get you around 2M blocked domains. Login to Pi-Hole (http://YourIP/admin), click on Group Management, then adlists. Paste below list all at once and click on 'Add'. 

```
https://v.firebog.net/hosts/AdguardDNS.txt
https://reddestdream.github.io/Projects/MinimalHosts/etc/MinimalHostsBlocker/minimalhosts
https://raw.githubusercontent.com/StevenBlack/hosts/master/data/KADhosts/hosts
https://raw.githubusercontent.com/StevenBlack/hosts/master/data/add.Spam/hosts
https://v.firebog.net/hosts/static/w3kbl.txt
https://adaway.org/hosts.txt
https://raw.githubusercontent.com/anudeepND/blacklist/master/adservers.txt
https://v.firebog.net/hosts/Easyprivacy.txt
https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt
https://raw.githubusercontent.com/Perflyst/PiHoleBlocklist/master/SmartTV.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_malvertising.txt
https://www.malwaredomainlist.com/hostslist/hosts.txt
https://dbl.oisd.nl/
https://openphish.com/feed.txt
http://sysctl.org/cameleon/hosts
https://s3.amazonaws.com/lists.disconnect.me/simple_ad.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_tracking.txt
https://pgl.yoyo.org/adservers/serverlist.php?hostformat=nohtml
http://theantisocialengineer.com/AntiSocial_Blacklist_Community_V1.txt
https://s3.amazonaws.com/lists.disconnect.me/simple_malware.txt
https://isc.sans.edu/feeds/suspiciousdomains_High.txt
http://winhelp2002.mvps.org/hosts.txt
https://www.stopforumspam.com/downloads/toxic_domains_whole.txt
https://raw.githubusercontent.com/Dawsey21/Lists/master/main-blacklist.txt
https://someonewhocares.org/hosts/hosts
https://heuristicsecurity.com/dohservers.txt
https://phishing.army/download/phishing_army_blocklist_extended.txt
https://raw.githubusercontent.com/oneoffdallas/dohservers/master/list.txt
https://www.sunshine.it/blacklist.txt
```
Goto Tools --> Gravity
Click on Update. This will take a while.

With all those blocked domains, there are a few we want whitelisted to prevent possible web surfing issues. So let's install whitelist script.

**Whitelist script Installation**
- Download
```
cd /opt/
sudo git clone https://github.com/Soundium/Pi_hole_Whitelist.git
sudo chmod +x /opt/Pi_hole_Whitelist/scripts/whitelist.sh
```
- Make the script to run the script at 1 AM every day.

`sudo nano /etc/crontab`

- Add this line at the end of the file:       
`0 1 * * *   root    /opt/Pi_hole_Whitelist/scripts/whitelist.sh`

CTRL + X then Y and Enter

- First run
```
sudo /opt/Pi_hole_Whitelist/scripts/whitelist.sh
```  

This will take a while, you might run through "Database is Locked" alert which can be ignored and let the process complete. When the process completes, 2 million domains will be added into your Pi-hole's Blocklist.

**NOTE** 

If you used all of the block lists above, be prepared to troubleshoot apps or websites that don't work because of blocked domains. If you run across a non-functional site or app, review the Pi-Hole logs for blocked domains and try whitelisting one at a time and re-testing your site/app to see what fixes the problem.

Many content providers (like YouTube / Reddit / Twitch / Spotify / Pandora / Facebook / IMDB / Hulu) now stream/serve ads from the same servers as the content (meaning if you attempt to block the ads using a PiHole you will also block the content).
Your best bet for blocking any and all ads/telemetry/etc is a multitiered approach of a PiHole as well as browser plugins: uBlock origin / Privacy Badger / CanvasBlocker on Firefox or Canvas Defender on Chrome / Decentraleyes / Smart Referer / Ugly Email / Pixel Block / other security browser plugins (as necessary) to prevent ads and protect your privacy.

Happy Adblocking :)

Click [here](https://github.com/SwaroopGiri/Pihole-with-unbound) to know how to further configure Pihole with unbound making it a truly self-contained DNS resolver.

Click [here](https://github.com/SwaroopGiri/Pihole-with-Anonymized-ODOH) to know how to further configure Pihole with DNSCrypt-proxy with ODoH making it an encrypted anonymous DNS Resolver.

Click [here](https://github.com/SwaroopGiri/Pivpn-wireguard-configuration) to know how to utilize networkwide adblocking of Pihole from anywhere outside of your local network by making raspberry pi into a self-sustained wireguard VPN server.
