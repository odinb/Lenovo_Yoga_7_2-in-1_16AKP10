# Lenovo Yoga 7, 2-in-1 16AKP10 with Manjaro

This guide is assuming Manjaro, and the below specific model of the Lenovo Yoga 7.
Things might (or might not) work on other combinations.

<p align="center">
  <img src="https://github.com/odinb/Lenovo_Yoga_7_2-in-1_16AKP10/blob/main/Lenovo_Yoga7_2-in-1_16AKP10_tent.avif" width="1200">
</p>

## Table of Contents
- [Laptop: Lenovo Yoga 7 2-in-1 16AKP10 (83JU / LNVNB161216)](#laptop-lenovo-yoga-7-2-in-1-16akp10-83ju--lnvnb161216)
  - [Specifications](#specifications)

- [BIOS settings](#bios-settings)
- [Filesystem at setup](#filesystem-at-setup)
- [User setup](#user-setup)
- [Update System](#update-system)
- [Timeshift Setup](#timeshift-setup)
- [Add firmware-updates](#add-firmware-updates)
- [Check BIOS](#check-bios)
- [Keyboard Backlight](#keyboard-backlight)
- [Verify suspend/sleep](#verify-suspendsleep)

- [Power Management](#power-management)
  - [Mask power-profiles-daemon](#mask-power-profiles-daemon)
  - [Configure auto-cpufreq](#configure-auto-cpufreq)
  - [Configure TLP (battery threshold only)](#configure-tlp-battery-threshold-only)
  - [Battery threshold script](#battery-threshold-script)
  - [Verify Settings](#verify-settings)
  - [Additional Battery & Power-info](#additional-battery--power-info)

- [WiFi](#wifi)
  - [Summary of WiFi 7 status](#summary-of-wifi-7-status)

- [Bluetooth Audio](#bluetooth-audio)
- [Video/webcam](#videowebcam)
- [Microphone](#microphone)
- [TouchScreen & Auto-rotate](#TouchScreen--Auto-rotate)
- [Audio Setup](#audio-setup)
- [PipeWire EQ Filter Chain](#pipewire-eq-filter-chain)
- [Fingerprint Sensor (not yet working, ignoring for now)](#fingerprint-sensor-not-yet-working-ignoring-for-now)
- [Setup SMB automount](#setup-smb-automount)
- [Create a Dolphin “Places” bookmark](#create-a-dolphin-places-bookmark)

- [macOS theming on KDE Plasma](#macos-theming-on-kde-plasma)
  - [Kvantum engine (required for proper widget styling)](#kvantum-engine-required-for-proper-widget-styling)
  - [Clone MacSequoia (or swap for WhiteSur/MacVentura)](#clone-macsequoia-or-swap-for-whitesurmacventura)
  - [WhiteSur icon pack (works with all three themes)](#whitesur-icon-pack-works-with-all-three-themes)
  - [WhiteSur cursor](#whitesur-cursor)
  - [Apply in KDE Settings](#apply-in-kde-settings)

- [How to update BIOS on Lenovo Yoga Pro 7 when running Linux](#how-to-update-bios-on-lenovo-yoga-pro-7-when-running-linux)
  - [Booting with LiveCD/LiveUSB](#booting-with-livecdliveusb)
  - [Flash from within Linux (not tried yet)](#flash-from-within-linux-not-tried-yet)


This is my working "lazy-moose" for this combo. Things might change, and this guide might or might not be updated.

<p align="center">
  <img src="https://github.com/odinb/Lenovo_Yoga_7_2-in-1_16AKP10/blob/main/Lenovo_Yoga7_2-in-1_16AKP10_squid.avif" width="700">
</p>

| Item | Status | Notes |
|-----|-----|-----|
| Touchpad | ✅ Working | No changes needed |
| TouchScreen | ✅ Working | No changes needed |
| Auto Screen Rotation | ✅ Working | Needs: iio-sensor-proxy + kscreen |
| Keyboard and Backlight | ✅ Working | No changes needed |
| Screen Brightness Buttons | ✅ Working | No changes needed |
| WebCam | ✅ Working | No changes needed |
| Bluetooth | ✅ Working | Needs: bluez + bluez-utils |
| Microphone | ✅ Working | No changes needed |
| Mute Buttons for  and Microphone | ✅ Working | No changes needed |
| Volume Buttons for  | ✅ Working  | Needs Kernel 6.18.26 or quirk table fix, [Bug 221210](https://bugzilla.kernel.org/show_bug.cgi?id=221210) or workaround below |
|  Speakers | ✅ Working  | Needs Kernel 6.18.26 or quirk table fix, [Bug 221210](https://bugzilla.kernel.org/show_bug.cgi?id=221210) or workaround below |
|  EQ | ⚠️ Partial  | Needs PipeWire [EQ Filter Chain](#-setup--eq) fix |
| FingerPrint Reader | ❌ Not Working | Needs more work apparently |


# Laptop: Lenovo Yoga 7 2-in-1 16AKP10 (83JU / LNVNB161216)
## Specifications
| Key Specs | |
|------|-------|
| Brand/Product | Lenovo Yoga 7 2-in-1 - 16" 2K LCD Touchscreen Laptop - AMD Ryzen AI 5 340 - 16GB Memory - 512GB SSD |
| Model Number | 83JU0001US |
| UPC / BSIN / SKU | 198155424168 / JJGSHC52HW / 6615774|
| Screen | IPS, 16 inches, 1920 x 1200 (WUXGA) 60Hz |
| Brightness | 300 nits |
| Touch Screen | Yes |
| Processor / GPU | AMD Ryzen AI 340 / AMD Radeon 840M |
| CPU Base Clock | 2 GHz |
| Storage | SSD, 512 GB, PCIe 4.0 |
| System Memory (RAM) | 16 GB LPDDR5X, 7500 MHz |
| Number Of Memory Slots | 0 |
| Display Connectors | 1x HDMI 2.1, USB-C |
| Battery | Up to 15 hours, Lithium-polymer |
| 2-in-1 Design | Yes |
| Backlit Keyboard | Yes |
| Customizable Keyboard Lighting | No |
| Touchpad Type | CIRQ1080 Multi-touch, Precision |
|  Technology | High Definition (HD) , Realtek ALC3306 codec |
| Speaker Type | 4 stereo speakers, 2W x2 (woofers), 2W x2 (tweeters), optimized with Dolby Atmos, Smart Amplifier (AMP) |
| Security Features | Privacy camera, Fingerprint reader |
| Windows AI | Copilot+ PC |
| Customizable Case Lighting | No |
| GPS Enabled | No |
| Optical Drive Type | None |
| Numeric Keypad | Yes |
| Cable Lock Slot | None |
| Casing Material | Aluminum |
| Color | Seashell |
| Year of Release | 2025 |
| ENERGY STAR Certified | Yes |
| EPEAT Qualified / Level | Yes / Gold |
| Warranty - Parts / Labor | 1 year / 1 year |

| Processor | |
|------|-------|
| Processor | AMD Ryzen AI 5 340 |
| Year of Release | 2025 |
| CPU Base Clock Frequency | 2 gigahertz |
| CPU Boost Clock Frequency | 4.8 gigahertz |
| Number of CPU Cores / Threads | 6-core / 12-Threads|
| CPU Cache Memory Level | L2 (6 MB), L3 (16 MB) |
| Neural Processing Unit (NPU) | Yes |
| Maximum NPU Performance | 50 trillions operations per second |

| Graphics | |
|------|-------|
| Graphics Type | Integrated |
| Graphics GPU | AMD Radeon 840M |

| Display | |
|------|-------|
| Screen | IPS, 16 inches, 1920 x 1200 (WUXGA) 60Hz |
| Brightness | 300 nits |
| Touch Screen | Yes |
| Color Gamut (NTSC) | 45 percent |

| Camera | |
|------|-------|
| Front-Facing Camera | Bison Electronics|
| Front Facing Camera Video Resolution | 1920x1080p @ 30fps |
| Integrated IR camera | 640x360 @ 30fps, 8-bit
| Built-In Microphone | Yes |

| Connectivity | |
|------|-------|
| Display Connector(s) | 1 x HDMI 2.1 up to 4K/60Hz, Display connection via USB port |
| Number of HDMI Outputs (Total) | 1 |
| DisplayPort Over USB-C | DisplayPort 1.4 |
| USB Ports | 1 x USB-A 3.2, 2 x USB-C 3.2 |
| Number of USB-C Charging Ports | 2 |
| Headphone Jack | Yes |
| Microphone Input | Yes |
| Wireless Connectivity | Wi-Fi (RTL8922AE), Bluetooth (RTL8922AU) |
| Wireless Standard | Wi-Fi 7, BE 5.3 |
| Number Of Ethernet Ports | 0 |
| Number of M.2 Expansion Slots | 1 |

| Power | |
|------|-------|
| Power Supply Input | USB-C |
| Power Supply Maximum Wattage | 65 watts |
| Battery Life (up to) | 15 hours |
| Battery Cells | 4-cell |
| Battery Capacity | 4525 milliampere hours |
| Battery Chemistry | Lithium-polymer |

| Dimensions | |
|------|-------|
| Product Height | 0.67 inches |
| Product Width | 10.12 inches |
| Product Depth | 14.21 inches |
| Product Weight | 3.96 pounds |

BIOS version:<br />
- Original (at purchase): QXCN19WW
- Upgraded to: QXCN22WW

S/N:<br />
`sudo dmidecode -t 1 | grep "Serial Number"`

or if no dmidecode:<br />
`sudo cat /sys/devices/virtual/dmi/id/product_serial`

<p align="center">
  <img src="https://github.com/odinb/Lenovo_Yoga_7_2-in-1_16AKP10/blob/main/Lenovo_Yoga7_2-in-1_16AKP10_OverViewPic.avif" width="600">
</p>

| # | Left Side | # | Right Side |
|-----|-----|-----|-----|
| 1. | HDMI® 2.1, up to 4K/60Hz | 4. | USB-A (USB 5Gbps), Always On |
| 2. | USB-C® (USB 10Gbps), with USB PD 45-65W & DP 2.1 UHBR10 | 5. | USB-C® (USB 10Gbps), with USB PD 45-65W & DP 1.4a |
| 3. | Headphone / microphone combo jack (3.5mm) | 6. | microSD card reader |
|    |  | 7. | Power button |

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
`QXCN22WW`

Verify with webpage if manual update needed, or wait for Lenovo to release (wishful thinking?):<br />
[Lenovo Yoga 7 2-in-1 16AKP10 - Type 83JU](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/yoga-series/yoga-7-2-in-1-16akp10/83ju)

## Keyboard Backlight:
The keyboard backlight is handled by the ideapad_acpi kernel driver and exposed via /sys/class/leds/platform::kbd_backlight. KDE detects it automatically — no additional packages required.

Verify detection:<br />
`ls /sys/class/leds/ | grep kbd`<br />

Should show: platform::kbd_backlight

Control in KDE:<br />
System Settings → Power Management → Keyboard Backlight<br />
The Fn+Space shortcut also cycles brightness levels directly.

## Verify suspend/sleep:
Logged into Manjaro, test sleep:<br />
`systemctl suspend`

Wake it and confirm:<br />
`journalctl -b 0 | grep -i "suspend\|resume" | grep "PM:"`

You should see both:<br />
- PM: suspend entry, and
- PM: suspend exit.

## Power Management:
Install packages:<br />
`sudo pacman -S tlp`<br />
`yay -S auto-cpufreq` # Do NOT run as sudo

### Mask power-profiles-daemon:<br />
Prevents conflicts with auto-cpufreq and TLP.<br />
`sudo systemctl stop power-profiles-daemon`<br />
`sudo systemctl mask power-profiles-daemon`

### Configure auto-cpufreq:
```
sudo tee /etc/auto-cpufreq.conf << 'EOF'
[charger]
governor = performance
energy_performance_preference = performance
turbo = auto

[battery]
governor = powersave
energy_performance_preference = power
turbo = auto
EOF
```

### Configure TLP (battery threshold only):<br />
`sudo vi /etc/tlp.conf`

Set these values, leave everything else commented out:<br />
`TLP_ENABLE=1`<br />
`TLP_DISABLE_DEFAULTS=1`<br />
`TLP_DEFAULT_MODE=AC`<br />
`TLP_PERSISTENT_DEFAULT=0`<br />
TLP_DISABLE_DEFAULTS=1 prevents TLP from fighting auto-cpufreq over CPU settings. TLP only manages the battery threshold on this setup.

Enable services:<br />
`sudo systemctl enable --now tlp`<br />
`sudo systemctl enable --now auto-cpufreq`

### Battery threshold script:
```
sudo tee /usr/local/bin/battery-threshold.sh << 'EOF'
#!/bin/bash
CONSERVATION=/sys/bus/platform/drivers/ideapad_acpi/VPC2004:00/conservation_mode
CAPACITY=$(cat /sys/class/power_supply/BAT0/capacity)

if [ "$CAPACITY" -ge 80 ]; then
echo 1 > "$CONSERVATION"
logger "battery-threshold: ${CAPACITY}% >= 80, conservation ON"
elif [ "$CAPACITY" -le 60 ]; then
echo 0 > "$CONSERVATION"
logger "battery-threshold: ${CAPACITY}% <= 60, conservation OFF"
fi
EOF
sudo chmod +x /usr/local/bin/battery-threshold.sh
```

Battery threshold systemd timer service:
```
sudo tee /etc/systemd/system/battery-threshold.service << 'EOF'
[Unit]
Description=Battery charge threshold control

[Service]
Type=oneshot
ExecStart=/usr/local/bin/battery-threshold.sh
EOF
```
Battery threshold systemd timer:
```
sudo tee /etc/systemd/system/battery-threshold.timer << 'EOF'
[Unit]
Description=Run battery threshold check every minute

[Timer]
OnBootSec=60
OnUnitActiveSec=60

[Install]
WantedBy=timers.target
EOF
sudo systemctl enable --now battery-threshold.timer
```

### Verify Settings:
`sudo tlp-stat -b | grep -i "threshold\|conservation\|charge"`<br />
`cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor`<br />
`sudo journalctl -u battery-threshold.service --since "5 minutes ago"`

This is the most impactful long-term battery health settings — keeping the battery between 60-80% max when plugged in significantly extends the lifespan of the battery over years of use.

Machine should now be at around 13 hours which beats most Linux laptops.

The biggest real-world battery gains on this machine will come from screen brightness — the 16" 2K display is the #1 power consumer. Dropping from 100% to 50% (or therearound) brightness saves 2-3W easily.

### Additional Battery & Power-info:
Battery Discharge/power info:<br />
`upower -i /org/freedesktop/UPower/devices/battery_BAT0`<br />
or minimised:<br />
`upower -i /org/freedesktop/UPower/devices/battery_BAT0 | grep "energy-rate\|state"`

Power info:<br />
`sudo powertop`

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

##  
Bluetooth  (Bose QC45 / A2DP) 🎧 ➡️ 🔊 :<br />
`sudo pacman -S bluez bluez-utils` # Already included in Manjaro but needed bluez-utils for bluetoothctl

Enable the service:<br />
`sudo systemctl enable --now bluetooth`

Pair via KDE Bluetooth panel — A2DP stereo works out of the box once connected.

Verify A2DP (with compatible headset connected):<br />
`pactl list sinks short | grep bluez`<br />
Should show float32le 2ch 48000Hz — that is stereo A2DP.

## Video/webcam:<br />
Check if it's detected<br />
`v4l2-ctl --list-devices`

To test, use KDE's built-in camera app:<br />
`sudo pacman -S kamoso`<br />
`kamoso`

## Microphone:
Record 5 seconds of :<br />
`arecord -d 5 -f cd /tmp/test.wav`

Play it back:<br />
`aplay /tmp/test/wav`

## TouchScreen & Auto-rotate
To see what is currently detected, check if tablet mode switch is working:<br />
cat /sys/bus/platform/devices/*/tablet_mode 2>/dev/null

In the output, you should see "accel_3d".<br />
If you do, the fix: install iio-sensor-proxy, and for KDE also do kscreen.<br />
This is the daemon that reads the IIO accelerometer and tells GNOME/KDE/etc. to rotate the screen.<br />
```
sudo pacman -S iio-sensor-proxy kscreen
# if not on KDE this will do: sudo pacman -S iio-sensor-proxy
sudo systemctl enable --now iio-sensor-proxy
```
Then verify it sees the sensor:<br />
`monitor-sensor`

Tilt the laptop — you should see orientation values changing in the output (normal, bottom-up, left-up, right-up). Hit Ctrl+C when done.
Try tent-mode, it should now work.

Even if the sensor works, your desktop might have "Orientation Lock" turned on.

If you are on KDE Plasma: Go to System Settings > Display and Monitor > Display Configuration. You should see an "Orientation" drop-down. If iio-sensor-proxy is working, an "Automatic" option should appear here.

If you still have issues, try restarting plasma-kscreen:<br />
`systemctl --user restart plasma-kscreen.service`

## Audio Setup
Working after OS-install with kernel 6.18.26 or newer. Kernel 6.18.28 or newer recommended to also patch "Copy Fail (CVE-2026-31431)" and "dirty frag (CVE-2026-43284, CVE-2026-43500)"<br />
See separate page [ALC287](alc287.md) for how to fix properly the manual way if needed.<br />
[Bug 221210](https://bugzilla.kernel.org/show_bug.cgi?id=221210)

## PipeWire EQ Filter Chain
This adds a permanent equalizer in PipeWire for the speakers — no extra apps needed, works on every boot.

🔊 🔊 🔊

Following scripts also adds functionality to:
- ✅ Speakers → use/via speaker EQ
- ✅ 3.5mm plug → use/via headphone EQ
- ✅ USB DAC plug → DAC direct (no EQ)
- ✅ USB DAC unplug → back to use/via speaker EQ

1. Create the config:
```
# Create PipeWire config directory
mkdir -p ~/.config/pipewire/pipewire.conf.d

# Create EQ configuration
cat > ~/.config/pipewire/pipewire.conf.d/eq-laptop.conf << 'EOF'
context.modules = [
  { name = libpipewire-module-filter-chain
    args = {
      node.description = "Laptop Speaker EQ"
      media.name = "Laptop Speaker EQ"
      filter.graph = {
        nodes = [
          { type=builtin name=eq1  label=bq_peaking   control={ "Freq"=80.0   "Q"=0.7 "Gain"=2.0 } }
          { type=builtin name=eq2  label=bq_peaking   control={ "Freq"=120.0  "Q"=1.0 "Gain"=2.5 } }
          { type=builtin name=eq3  label=bq_peaking   control={ "Freq"=250.0  "Q"=1.0 "Gain"=1.0 } }
          { type=builtin name=eq4  label=bq_peaking   control={ "Freq"=400.0  "Q"=1.0 "Gain"=2.0 } }
          { type=builtin name=eq5  label=bq_peaking   control={ "Freq"=900.0  "Q"=2.0 "Gain"=-2.5 } }
          { type=builtin name=eq6  label=bq_peaking   control={ "Freq"=2000.0 "Q"=1.5 "Gain"=2.5 } }
          { type=builtin name=eq7  label=bq_peaking   control={ "Freq"=2800.0 "Q"=2.0 "Gain"=1.5 } }
          { type=builtin name=eq8  label=bq_peaking   control={ "Freq"=4000.0 "Q"=1.5 "Gain"=-2.0 } }
          { type=builtin name=eq9 label=bq_peaking   control={ "Freq"=6000.0 "Q"=1.2 "Gain"=-3.5 } }
          { type=builtin name=eq10 label=bq_highshelf control={ "Freq"=8000.0 "Gain"=-3.0 } }
        ]
        links = [
          { output="eq1:Out"  input="eq2:In" }
          { output="eq2:Out"  input="eq3:In" }
          { output="eq3:Out"  input="eq4:In" }
          { output="eq4:Out"  input="eq5:In" }
          { output="eq5:Out"  input="eq6:In" }
          { output="eq6:Out"  input="eq7:In" }
          { output="eq7:Out"  input="eq8:In" }
          { output="eq8:Out"  input="eq9:In" }
          { output="eq9:Out" input="eq10:In" }
        ]
      }
      capture.props = {
        node.name = "effect_input.eq_speaker"
        media.class = "Audio/Sink"
        audio.channels = 2
        audio.position = [ FL FR ]
      }
      playback.props = {
        node.name = "effect_output.eq_speaker"
        node.target = "alsa_output.pci-0000_04_00.6.HiFi__Speaker__sink"
        audio.channels = 2
        audio.position = [ FL FR ]
      }
    }
  }
  { name = libpipewire-module-filter-chain
    args = {
      node.description = "Headphones EQ"
      media.name = "Headphones EQ"
      filter.graph = {
        nodes = [
          { type=builtin name=eq1  label=bq_lowshelf  control={ "Freq"=105.0  "Gain"=4.0 } }
          { type=builtin name=eq2  label=bq_peaking   control={ "Freq"=380.0  "Q"=0.7 "Gain"=-3.0 } }
          { type=builtin name=eq3  label=bq_peaking   control={ "Freq"=1000.0 "Q"=1.5 "Gain"=-1.5 } }
          { type=builtin name=eq4  label=bq_peaking   control={ "Freq"=2500.0 "Q"=1.2 "Gain"=2.5 } }
          { type=builtin name=eq5  label=bq_peaking   control={ "Freq"=4500.0 "Q"=1.5 "Gain"=2.0 } }
          { type=builtin name=eq6  label=bq_peaking   control={ "Freq"=6000.0 "Q"=2.0 "Gain"=-1.5 } }
          { type=builtin name=eq7  label=bq_highshelf control={ "Freq"=9000.0 "Gain"=-2.0 } }
        ]
        links = [
          { output="eq1:Out" input="eq2:In" }
          { output="eq2:Out" input="eq3:In" }
          { output="eq3:Out" input="eq4:In" }
          { output="eq4:Out" input="eq5:In" }
          { output="eq5:Out" input="eq6:In" }
          { output="eq6:Out" input="eq7:In" }
        ]
      }
      capture.props = {
        node.name = "effect_input.eq_headphones"
        media.class = "Audio/Sink"
        audio.channels = 2
        audio.position = [ FL FR ]
      }
      playback.props = {
        node.name = "effect_output.eq_headphones"
        node.target = "alsa_output.pci-0000_04_00.6.HiFi__Headphones__sink"
        audio.channels = 2
        audio.position = [ FL FR ]
      }
    }
  }
]
EOF
```
EQ will run on speakers to compensate for the poor output.<br />
EQ on headphones on the 3.5mm will run a gentle harman curve (mild bass boost, slight upper-mid lift — works well on most headphones).

2. Now the WirePlumber default sink settings:
Fallback script for if the WirePlumber auto-switch Lua script fails:
```
# Create WirePlumber config directory
mkdir -p ~/.config/wireplumber/wireplumber.conf.d

# Create default sink configuration
cat > ~/.config/wireplumber/wireplumber.conf.d/default-sink.conf << 'EOF'
wireplumber.settings = {
  default.audio.sink = "effect_input.eq_speaker"
}
EOF
```
3. Now the WirePlumber auto-switch script:
```
# Create WirePlumber auto-switch configuration
cat > ~/.config/wireplumber/wireplumber.conf.d/auto-switch-eq.conf << 'EOF'
wireplumber.components = [
  {
    name = "custom/auto-switch-eq.lua"
    type = "script/lua"
    provides = "custom.auto-switch-eq"
  }
]

wireplumber.profiles = {
  main = {
    custom.auto-switch-eq = required
  }
}
EOF
```
4. Now the WirePlumber EQ-auto-switch script:
```
# Create WirePlumber EQ-auto-switch directory
mkdir -p ~/.local/share/wireplumber/scripts/custom/

# Create WirePlumber EQ-auto-switch configuration
cat > ~/.local/share/wireplumber/scripts/custom/auto-switch-eq.lua << 'EOF'
log = Log.open_topic("s-auto-switch-eq")

local headphone_sink = "alsa_output.pci-0000_04_00.6.HiFi__Headphones__sink"

local om_meta = ObjectManager {
  Interest { type = "metadata",
    Constraint { "metadata.name", "=", "default" }
  }
}

local om_hp = ObjectManager {
  Interest { type = "node",
    Constraint { "node.name", "=", headphone_sink }
  }
}

local om_dac = ObjectManager {
  Interest { type = "node",
    Constraint { "media.class", "=", "Audio/Sink" },
    Constraint { "device.bus", "=", "usb", type = "pw" }
  }
}

local function set_default_sink(name)
  local metadata = om_meta:lookup {
    Constraint { "metadata.name", "=", "default" }
  }
  if metadata then
    log:info("Switching default sink to: " .. name)
    metadata:set(0, "default.configured.audio.sink", "Spa:String:JSON",
      Json.Object { ["name"] = name }:to_string())
  else
    log:warning("Could not find default metadata object")
  end
end

local function get_fallback_sink()
  if om_hp:lookup() then
    return "effect_input.eq_headphones"
  else
    return "effect_input.eq_speaker"
  end
end

-- 3.5mm headphone events
om_hp:connect("object-added", function(_, node)
  if not om_dac:lookup() then
    log:info("Headphones plugged in - switching to headphone EQ")
    set_default_sink("effect_input.eq_headphones")
  end
end)

om_hp:connect("object-removed", function(_, node)
  if not om_dac:lookup() then
    log:info("Headphones unplugged - switching to speaker EQ")
    set_default_sink("effect_input.eq_speaker")
  end
end)

-- DAC events (any USB audio device)
om_dac:connect("object-added", function(_, node)
  local name = node.properties["node.name"]
  log:info("USB DAC plugged in: " .. name .. " - switching to DAC")
  set_default_sink(name)
end)

om_dac:connect("object-removed", function(_, node)
  log:info("USB DAC unplugged - switching to fallback")
  set_default_sink(get_fallback_sink())
end)

-- Handle devices already present at script load time
om_meta:connect("object-added", function(_, metadata)
  local dac = om_dac:lookup()
  if dac then
    local name = dac.properties["node.name"]
    log:info("USB DAC already present at startup: " .. name)
    set_default_sink(name)
  elseif om_hp:lookup() then
    log:info("Headphones already present at startup")
    set_default_sink("effect_input.eq_headphones")
  end
end)

om_meta:activate()
om_hp:activate()
om_dac:activate()
EOF
```
5. Now the Login autostart script — sets default sink:<br />
Priority order matches the Lua script: USB DAC → 3.5mm headphones → speakers.<br />
This only matters if you boot with a DAC already plugged in, since the Lua script handles hot-plug during the session.
```
# Set default sink
cat > ~/.local/bin/set-default-sink.sh << 'EOF'
#!/bin/bash
sleep 3

# Get EQ sink IDs
HP_SINK=$(wpctl status | grep "effect_input.eq_headphones" | awk '{print $1}' | tr -d '.')
SP_SINK=$(wpctl status | grep "effect_input.eq_speaker" | awk '{print $1}' | tr -d '.')

# Check what's plugged in (USB DAC takes priority)
DAC_SINK=$(pactl list sinks short | grep "usb" | grep -v "eq" | awk '{print $1}')

if [ -n "$DAC_SINK" ]; then
    # USB DAC present — get its full node name and set as default
    DAC_NAME=$(pactl list sinks short | grep "usb" | grep -v "eq" | awk '{print $2}')
    wpctl set-default "$DAC_SINK"
elif [ -n "$HP_SINK" ] && wpctl status | grep -q "HiFi__Headphones__sink"; then
    wpctl set-default "$HP_SINK"
else
    wpctl set-default "$SP_SINK"
fi
EOF
chmod +x ~/.local/bin/set-default-sink.sh
```
To activate, it will be needed to logout user, and then log back in.

6. Now the KDE autostart entry
```
# KDE autostart entry
cat > ~/.config/autostart/set-default-sink.desktop << 'EOF'
[Desktop Entry]
Type=Application
Name=Set Default Audio Sink (EQ)
Exec=/home/brodersen/.local/bin/set-default-sink.sh
Terminal=false
X-KDE-AutostartPhase=2
EOF
```
7. Restart audio stack:<br />
`systemctl --user restart pipewire pipewire-pulse wireplumber`

8. Verify:<br />
`pactl list sinks short` # Should show effect_input.eq as RUNNING when audio plays<br />
`wpctl status | grep -i "sink\|default"` # effect_input.eq should have * (default marker)

## Troubleshooting Audio
Volume control stops working:<br />
`wpctl set-volume @DEFAULT_AUDIO_SINK@ 0.5`<br />
`wpctl set-mute @DEFAULT_AUDIO_SINK@ 0`

EQ not applying (audio sounds flat):
Check EQ sink exists:<br />
`pactl list sinks short`<br />
Set it as default (use the ID shown for effect_input.eq):<br />
`wpctl set-default <ID>`<br />
Restart Brave/Firefox to reconnect to new default sink

Speaker level reset to 64%:<br />
`amixer -c 1 sset 'Speaker' 100%`<br />
`sudo alsactl store`


## Fingerprint Sensor
⚠️ Not working after OS-install, ignoring for now.<br />
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
Replace with your actual credentials
```
sudo tee /etc/samba/creds-share << 'EOF'
username=myuser
password=mypassword
EOF
```

then:<br />
`sudo chmod 600 /etc/samba/creds-share`

Create mount-point:<br />
`sudo mkdir -p /mnt/cwwk`<br />
You can call it whatever, change "cwwk" to what you want.<br />
Same thing goes for "//192.168.1.100/TrueNAS-share" which needs to change to your mount-location.

Then create (verify uid/gid with your user):<br />
```
sudo tee /etc/systemd/system/mnt-cwwk.mount << 'EOF'
[Unit]
Description=TrueNAS SMB Share

[Mount]
What=//192.168.1.100/TrueNAS-share
Where=/mnt/cwwk
Type=cifs
Options=credentials=/etc/samba/creds-share,iocharset=utf8,vers=3.0,uid=1000,gid=1000,file_mode=0664,dir_mode=0775,noserverino,nofail,x-systemd.device-timeout=5s
TimeoutSec=10
EOF
```

Then create:<br />
```
sudo tee /etc/systemd/system/mnt-cwwk.automount << 'EOF'
[Unit]
Description=TrueNAS SMB Share Automount

[Automount]
Where=/mnt/cwwk
TimeoutIdleSec=280

[Install]
WantedBy=multi-user.target
EOF
# Enable and Start Automount:
systemctl daemon-reload
systemctl enable --now mnt-cwwk.automount
```

(or if edited/updated: `sudo systemctl restart mnt-cwwk.automount`)

Test (files on SMB-server should be listed now):<br />
`ls /mnt/cwwk`

Also enable network-online.target (usually disabled by default on Manjaro):<br />
`sudo systemctl enable NetworkManager-wait-online.service`

For WiFi:<br />
Stop race-condition by storing password in config, not kwallet.<br />
Replace "TheSSID" with your actual SSID:<br />
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

## How to update BIOS on Lenovo Yoga Pro 7 when running Linux
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
You don't actually need WinPE, Hiren's BootCD PE or weird hack tools for any of this, you can use `fwupd` just fine:
- Download the "BIOS Updater" Windows executable file from Lenovo's support website. [BIOS update installer from Lenovo](https://pcsupport.lenovo.com/pl/en/products/laptops-and-netbooks/yoga-series/yoga-pro-7-14asp9/downloads/driver-list/component?name=BIOS%2FUEFI&id=5AC6A815-321D-440E-8833-B07A93E0428C). Make sure you pick the one for your specific machine!
- Unzip that executable file with either 7z or a desktop archive manager (Ark works)
- Get the ~34MiB .fd file, this is the actual firmware image
- Look up your system firmware device ID: `doas fwupdtool get-devices | grep -A 1 "System Firmware"`
- Prepare the firmware flash process with: `doas fwupdtool install-blob [filename].fd [system firmware device ID]`

When the tool asks you to reboot, let it do so.
The laptop will update the firmware first using H2OFFT and then the internal flashing utility ( it will restart twice).

Once everything's done, you're back to GRUB/rEFInd
