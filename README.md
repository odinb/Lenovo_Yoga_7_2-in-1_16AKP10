# Lenovo Yoga 7, 2-in-1 16AKP10 with Manjaro

This guide is assuming Manjaro, and the below specific model of the Lenovo Yoga 7.
Things might (or might not) work on other combinations.

## Table of Contents
- [Hardware](#hardware)
- [Linux Compatibility](#linux-compatibility)
- [Working](#working)
- [Not Working](#not-working)
- [Kernel Requirements](#kernel-requirements)
- [Fixes](#fixes)

This is my working log of this combo. Things might change, and this guide might or might not be updated.

| Item | Status | Notes |
|-----|-----|-----|
| Touchpad | ✅ Working | No changes needed |
| Keyboard and Backlight | ✅ Working | No changes needed |
| Screen Brightness Buttons | ✅ Working | No changes needed |
| WebCam | ✅ Working | No changes needed |
| Microphone | ✅ Working | No changes needed |
| Mute Buttons for Audio and Microphone | ✅ Working | No changes needed |
| Volume Buttons for Audio | ⚠️ Partial  | Needs Kernel Quirk table fix, [Bug 221210](https://bugzilla.kernel.org/show_bug.cgi?id=221210) or workaround below |
| Audio Speakers | ⚠️ Partial  | Needs Kernel Quirk table fix, [Bug 221210](https://bugzilla.kernel.org/show_bug.cgi?id=221210) or workaround below |
| Audio EQ | ⚠️ Partial  | Needs PipeWire EQ Filter Chain fix below |
| FingerPrint Reader | ❌ Not Working | Needs more work apparently |


# Laptop: Lenovo Yoga 7 2-in-1 16AKP10 (83JU / LNVNB161216)
CPU: AMD Ryzen AI 5 340 w/ Radeon 840M<br />
BIOS version:<br />
- Original (at purchase): QXCN19WW
- Now: QXCN21WW

S/N:<br />
`sudo dmidecode -t 1 | grep "Serial Number"`

or if no dmidecode:<br />
`sudo cat /sys/devices/virtual/dmi/id/product_serial`

## BIOS settings:
Under "Security" in BIOS (F2 at power-on):
- Enhanced Windows Biometry Security: Disabled
- Pluton Security Processor: Enabled (Needed for sleep to work)
- Absolute Persistence Module Activation: Disabled
- Secure Boot: Disabled

## Filesystem at setup
When installing Manjaro OS (or other), keep the default btrfs for better snapshotting with Timeshift.

## User setup:
Change to no password needed for sudo for user "JohnDoe":<br />
`visudo -f /etc/sudoers`

Enable:<br />
Uncomment to allow members of group wheel to execute any command:<br />
`%wheel ALL=(ALL:ALL) ALL`

Same thing without a password:<br />
`%wheel ALL=(ALL:ALL) NOPASSWD: ALL`

Then:<br />
`visudo -f /etc/sudoers.d/10-installer`

Enable:<br />
`/etc/sudoers.d/10-installer`

Add user to group wheel:<br />
`usermod -aG wheel JohnDoe`

Verify:<br />
`id JohnDoe`

## Update System:

Add faster mirrors:<br />
`sudo pacman-mirrors --fasttrack`

Update full system:<br />
`sudo pacman -Syu`

To install package with pacman:<br />
`sudo pacman -S 'package-name'`

To remove package with pacman:<br />
`sudo pacman -Rns 'package-name'`

## Timeshift Setup:
- Daily: keep 5
- Weekly: keep 2

Before updates:<br />
`sudo pacman -S timeshift-autosnap`

Confirm:<br />
`[Yoga7-Manjaro ~]# cat /usr/share/libalpm/hooks/00-timeshift-autosnap.hook`

Edit:<br />
`sudo vi /etc/timeshift/timeshift.json`

Add/change:<br />
- "schedule_boot": "true" → Takes a snapshot at each boot (great safety net).
- "boot_grub_menu": "true" → GRUB shows available snapshots.
- "count_boot": "5" → Keeps last 5 boot snapshots, automatically cleans older ones.
- "count_hourly": "0" → Hourly snapshots are unnecessary if using boot + daily snapshots.

Then:<br />
`sudo update-grub`<br />
`sudo reboot`

## Add firmware-updates:
`sudo pacman -S yay fwupd`<br />
`sudo fwupdmgr refresh`<br />
or<br />
`sudo fwupdmgr refresh --force`<br />
`sudo fwupdmgr update`<br />
`sudo fwupdmgr get-updates` # Lenovo can see aggregate/anonymous statistics via LVFS, not per‑machine log.<br />
`sudo reboot`

## Check BIOS:
`sudo dmidecode -s bios-version`<br />
`QXCN19WW`

Verify with webpage if manual update needed, or wait for Lenovo to release (wishful thinking?):<br />
[Lenovo Yoga 7 2-in-1 16AKP10 - Type 83JU](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/yoga-series/yoga-7-2-in-1-16akp10/83ju)

## Verify suspend/sleep:
Logged into Manjaro, test sleep:<br />
`systemctl suspend`

Wake it and confirm:<br />
`journalctl -b 0 | grep -i "suspend\|resume" | grep "PM:"`

You should see both:<br />
- PM: suspend entry, and
- PM: suspend exit.

### Power Profiles:
`sudo pacman -S power-profiles-daemon`<br />
`sudo systemctl enable --now power-profiles-daemon`

Verify:<br />
`systemctl status power-profiles-daemon`

To switch profiles from terminal:<br />
`powerprofilesctl set power-saver` # For Power-Saver profile<br />
`powerprofilesctl set balanced` # For Balanced profile<br />
`powerprofilesctl set performance` # For Performance profile<br />
`powerprofilesctl get` # Check active profile<br />

### Power-savings
Install auto-cpufreq — will push idle wattage down further on battery:<br />
`yay -S auto-cpufreq` # Do not run as sudo<br />
`sudo systemctl enable --now auto-cpufreq`

Create /etc/auto-cpufreq.conf with this content:<br />
```
[charger]
governor = performance
energy_performance_preference = performance
turbo = auto

[battery]
governor = powersave
energy_performance_preference = power
turbo = auto
```

### Current power profile
`powerprofilesctl get`

### Battery status
`upower -i /org/freedesktop/UPower/devices/battery_BAT0`

### What's consuming power right now
`sudo powertop --time=5 2>/dev/null | head -40`

### TLP or auto-cpufreq installed?
`sudo pacman -Qs 'tlp|auto-cpufreq'`

### Lenovo battery threshold support via its own ACPI interface:
`sudo pacman -S tlp`
`sudo vi /etc/tlp.conf`

Find and set:<br />
```
START_CHARGE_THRESH_BAT0=0
STOP_CHARGE_THRESH_BAT0=1
```
For Lenovo IdeaPad/Yoga, 1 = conservation mode on (charges to ~60%), 0 = off (charges to 100%).<br />

Now fix the TLP service conflict with power-profiles-daemon.<br />
Also uncomment and set:<br />
`TLP_DISABLE_DEFAULTS=1`

This stops TLP from fighting with power-profiles-daemon over CPU settings, and only uses TLP for battery threshold management.

Mask power-profiles-daemon so TLP can start cleanly:<br />
`sudo systemctl mask power-profiles-daemon`<br />
`sudo systemctl stop power-profiles-daemon`<br />
`sudo systemctl enable --now tlp`<br />
`sudo systemctl status tlp`

Then verify the threshold is applied:<br />
`sudo tlp-stat -b`

Or:<br />
`sudo tlp-stat -b | grep -i threshold`

You should see:<br />
Supported features: charge threshold<br />
* vendor (ideapad_laptop) = active (charge threshold)

This is the most impactful long-term battery health setting — keeping the battery at 60% max when plugged in significantly extends its lifespan over years of use.

Machine should now be at around 13 hours which beats most Linux laptops.

The biggest real-world battery gains on this machine will come from screen brightness — the 16" 2K display is the #1 power consumer. Dropping from 100% to 50% (or therearound) brightness saves 2-3W easily.

## WiFi:
Confirm current status:<br />
`iw dev wlp3s0 info`

See full adapter capabilities:<br />
`iw phy phy0 info | grep -A5 "Frequencies\|HE\|EHT\|VHT\|Band"`

Check driver version:<br />
`modinfo rtw89_8922ae | grep -i "version\|description"`

Check what the driver currently supports:<br />
`iw dev wlp3s0 link | grep -i "EHT\|MLO\|be\|width"`

Output:<br />
```
rx bitrate: 864.8 MBit/s 80MHz EHT-MCS 8 EHT-NSS 2 EHT-GI 0
tx bitrate: 960.7 MBit/s 80MHz EHT-MCS 9 EHT-NSS 2 EHT-GI 0
```

EHT = Extremely High Throughput = WiFi 7. Connected at nearly 1 Gbps on WiFi 7 with 2 spatial streams. The driver is working almost perfectly.

### Summary of WiFi 7 status:
| Working | Item | Notes |
|-----|-----|-----|
✅ | EHT (WiFi 7 modulation) | Working |
✅ | 2x2 MIMO | Working |
✅ | 4096-QAM | Working |
✅ | ~1 Gbps on 5 GHz | Working |
❌ | MLO (multi-band bonding) | Not yet in driver |
❌ | 6 GHz band | Not yet in driver |

## Video/webcam:<br />
Check if it's detected<br />
`v4l2-ctl --list-devices`

To test, use KDE's built-in camera app:<br />
`sudo pacman -S kamoso`<br />
`kamoso`

## Microphone:
Record 5 seconds of audio:<br />
`arecord -d 5 -f cd /tmp/test.wav`

Play it back:<br />
`aplay /tmp/test/wav`

## Audio issues: ⚠️
See separate text-file for how to fix properly untill missing kernel fixup is added.<br />
[Bug 221210](https://bugzilla.kernel.org/show_bug.cgi?id=221210)

## Fingerprint Sensor (not yet working, ignoring for now ⚠️)
`lsusb` to get printout:<br />
`Bus 003 Device 002: ID 1c7a:0583 LighTuning Technology Inc. ETU905A88-E`

`sudo pacman -S fprintd`<br />
`sudo systemctl enable --now fprintd`<br />
`fprintd-enroll -f right-index-finger`

Test enrollment first:<br />
`fprintd-list $USER`

If it shows your enrolled finger, try locking the screen and using the fingerprint to unlock.<br />
If it enrolls but fails to match, unfortunately that's a known bug with this specific sensor on Linux that hasn't been fully resolved upstream yet.

Enrollment is lost after the screen locks or sleeps:<br />
This is a known bug with this exact sensor. The 1c7a:0583 device has been widely reported to fail — it enrolls successfully but enrollment is lost after the screen locks or sleeps.

If enrollment works, then enable it for KDE login and sudo:<br />
`sudo pam-auth-update --enable fprintd`

And for the lock screen specifically:<br />
`sudo nano /etc/pam.d/kde`

Add at the top:<br />
`auth sufficient pam_fprintd.so`

## Setup SMB automount:
`sudo vi /etc/samba/creds-share`

Content:
```
username=myuser
password=mypassword
```

then:<br />
`sudo chmod 600 /etc/samba/creds-share`

Create mount-point:<br />
`sudo mkdir -p /mnt/cwwk`<br />
You can call it whatever, change "cwwk" to what you want.<br />
Same thing goes for "//192.168.1.100/TrueNAS-share" which needs to change to your mount-location.

Then create (verify uid/gid with your user):<br />
`sudo vi /etc/systemd/system/mnt-cwwk.mount`
With this content:
```
[Unit]
Description=SMB Share Mount
After=network-online.target
Wants=network-online.target
ConditionPathExists=/mnt/cwwk

[Mount]
What=//192.168.1.100/TrueNAS-share
Where=/mnt/cwwk
Type=cifs
Options=credentials=/etc/samba/creds-share,iocharset=utf8,vers=3.0,_netdev,nofail,uid=1000,gid=1000,file_mode=0664,dir_mode=0775,noserverino
TimeoutSec=5

[Install]
WantedBy=remote-fs.target
```

Then create:<br />
`sudo vi /etc/systemd/system/mnt-cwwk.automount`
With this content:
```
[Unit]
Description=Automount SMB Share
After=network-online.target
Wants=network-online.target

[Automount]
Where=/mnt/cwwk
TimeoutIdleSec=600

[Install]
WantedBy=remote-fs.target

Enable and Start Automount:
systemctl daemon-reload
systemctl enable --now mnt-cwwk.automount
```

(or if edited: `sudo systemctl restart mnt-cwwk.automount`)

Test (files on SMB-server should be listed now):<br />
`ls /mnt/cwwk`

Also enable network-online.target (usually disabled by default on Manjaro):<br />
`sudo systemctl enable NetworkManager-wait-online.service`

For WiFi:<br />
Stop race-condition by storing password in config, not kwallet:<br />
`sudo vi /etc/NetworkManager/system-connections/TheSSID.nmconnection`

Change "psk-flags=1" to "psk-flags=0" and make sure the password is present:
```
[wifi-security]
auth-alg=open
key-mgmt=sae
psk=YOUR_ACTUAL_PASSWORD
psk-flags=0
```
Verify (where MySSID is your SSID):<br />
`nmcli connection show MySSID | grep -i psk`

Should show psk-flags: 0. Then reboot and WiFi should connect immediately at boot without waiting for KWallet.

## Create a Dolphin “Places” bookmark
Replace /mnt/cwwk with whatever you named it above.<br />
Open Dolphin.<br />
Navigate to /mnt/cwwk.<br />
Press Ctrl+D (or Bookmarks → Add to Places).<br />
This adds a persistent sidebar entry called “cwwk” (you can rename it).<br />
Since the share is mounted and owned by your user, it loads immediately without hanging.

Optional: Create a .desktop shortcut<br />
If you want a clickable shortcut elsewhere (like on Desktop):<br />
Create ~/Desktop/TrueNAS Share.desktop with:
```
[Desktop Entry]
Type=Link
Name=TrueNAS Share
Icon=folder-remote
URL=file:///mnt/cwwk/
```
Make it executable so some desktop environments recognize it:<br />
`chmod +x ~/Desktop/TrueNAS\ Share.desktop`

Clicking it opens the mounted share in Dolphin immediately.

## macOS theming on KDE Plasma:
Recommendation: MacSequoia if you want the latest look, WhiteSur if you want the most stable.<br />
All three use the same stack.

Install the full stack:<br />
### Kvantum engine (required for proper widget styling)
`sudo pacman -S kvantum`

### Clone MacSequoia (or swap for WhiteSur/MacVentura)
`cd ~`<br />
`git clone https://github.com/vinceliuice/MacSequoia-kde`<br />
`cd MacSequoia-kde`<br />
`sudo ./install.sh`

### WhiteSur icon pack (works with all three themes)
`cd ~`<br />
`git clone https://github.com/vinceliuice/WhiteSur-icon-theme`<br />
`cd WhiteSur-icon-theme`<br />
`sudo ./install.sh`

### WhiteSur cursor
`cd ~`<br />
`git clone https://github.com/vinceliuice/WhiteSur-cursors`<br />
`cd WhiteSur-cursors`<br />
`sudo ./install.sh`

### Apply in KDE Settings
System Settings → Appearance → Global Theme → select MacSequoia<br />
System Settings → Appearance → Application Style → set to Kvantum, then open kvantummanager and select MacSequoia<br />
System Settings → Appearance → Icons → WhiteSur<br />
System Settings → Appearance → Cursors → WhiteSur<br />

For the dock (makes it really feel like macOS):<br />
`sudo pacman -S latte-dock`

GTK apps (Chrome, Firefox etc.):<br />
`cd ~`
`git clone https://github.com/vinceliuice/WhiteSur-gtk-theme`
`cd WhiteSur-gtk-theme`
`sudo ./install.sh`

This makes GTK apps match the KDE theme so everything looks consistent.

## How to update BIOS on Lenovo Yoga Pro 7 from Linux
Lenovo support page for this model only provides "*.exe" installer for BIOS updates. There is no option in the BIOS itself to select a binary blob from USB stick, none whatsoever.

The way to do a BIOS update anyway below (2 ways):
### Booting with LiveCD/LiveUSB
- [Hiren's BootCD Preinstallation Environment](https://www.hirensbootcd.org/)
 4 GB USB stick
- [Ventoy](https://ventoy.net/en/index.html)
- The [BIOS update installer from Lenovo](https://pcsupport.lenovo.com/pl/en/products/laptops-and-netbooks/yoga-series/yoga-pro-7-14asp9/downloads/driver-list/component?name=BIOS%2FUEFI&id=5AC6A815-321D-440E-8833-B07A93E0428C). Make sure you pick the one for your specific machine!

The steps are as follows:
- Install Ventoy on the USB stick
- Copy over the BootCD's iso onto Ventoy partition
- Copy over the BIOS update onto Ventoy partition
- Boot from Ventoy USB stick
- Find the BIOS update installer on the portable (aka LiveUSB) Windows and run it.

### Flash from within Linux (not tried yet)
You don't actually need WinPE, Hiren's BootCD PE or weird hack tools for any of this, you can use fwupd just fine:
- Download the "BIOS Updater" Windows executable file from Lenovo's support website
- Unzip that executable file with either 7z or a desktop archive manager (Ark works)
- Get the ~34MiB .fd file, this is the actual firmware image
- Look up your system firmware device ID: doas fwupdtool get-devices | grep -A 1 "System Firmware"
- Prepare the firmware flash process with: doas fwupdtool install-blob [filename].fd [system firmware device ID]

When the tool asks you to reboot, let it do so.
The laptop will update the firmware first using H2OFFT and then the internal flashing utility (and so it'll restart two times).

Once everything's done, you're back to GRUB/rEFInd


==========
