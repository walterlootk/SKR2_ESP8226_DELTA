I have an AnyCubic Kossel Pulley 3D printer since April 2017. Sometime in August 2021 I got hold of an SKR 2 Rev B board and four TMC2209 drivers. Everything else has remain more or less the same.
It was also during this time the 8-bit Trigorilla board died, hence initiating the SKR2 replacement on my AnyCubic. Below were the changes that I made to the relevant configuration files to get the printer set up and all the bugs flushed out. Hope this helps someone, as I was helped many times by other contributors.

Marlin Configuration - for AnyCubic Kossel Pulley DELTA Printer
Period of mainconfiguration: 10-11-2021 ~ 26-11-2021

1) Marlin Firmware
==================
1.1 Configuration Samples version date: 04-10-2021, extracted from: https://github.com/MarlinFirmware/Configurations/archive/refs/tags/2.0.9.2.zip
1.2 Marlin firmware from Marlin Website, is Marlin 2.0.9.2


2) Changes made to the following:
================================
A. Configuration.h
B. Configuration_adv.h (no changes yet - just review)
C. platformio.ini
D. pins_BTT_SKR_V2_0_common.h


A) For Configuration.h, the following were done:
================================================
** line numbers are closely approximate.

line 33 #define ANYCUBIC_PROBE_VERSION 2
line 39 #define ANYCUBIC_KOSSEL_ENABLE_BED 1
line 113 #define SERIAL_PORT 3 (for Wifi module)
line 134 #define SERIAL_PORT_2 -1 (enabled for USB Connection)
line 150 #define MOTHERBOARD BOARD_BTT_SKR_V2_0_REV_B
line 154 #define CUSTOM_MACHINE_NAME "ANYCUBIC Kossel SKR2"
line 797 //#define DELTA_HOME_TO_SAFE_ZONE (default is ENABLED) 
           - to prevent printhead from doing down 70mm after homing
line 958 #define X_DRIVER_TYPE  TMC2209
line 959 #define Y_DRIVER_TYPE  TMC2209
line 960 #define Z_DRIVER_TYPE  TMC2209
line 969 #define E0_DRIVER_TYPE TMC2209
line 1068 #define DEFAULT_ACCELERATION          2000
line 1070 #define DEFAULT_TRAVEL_ACCELERATION   2000
line 1464 #define INVERT_X_DIR false (default is ) true
line 1465 #define INVERT_Y_DIR false (default is ) true
line 1466 #define INVERT_Z_DIR false (default is ) true
line 1474 #define INVERT_E0_DIR false (default is ) true
line 1681 #define AUTO_BED_LEVELING_BILINEAR (default)
line 1750 #define GRID_MAX_POINTS_X 9 (default)
line 1892 #define HOMING_FEEDRATE_MM_M { (60*60), (60*60), (60*60) }

B) For Configuration_adv.h, the following were noted/changed.
   Guideline from https://www.lpomykal.cz/anycubic-kossel-skr-1-3-upgrade/
============================================================
line 1142 #define MICROSTEP_MODES { 16, 16, 16, 16, 16, 16 } // [1,2,4,8,16] *unchanged*
line 2598   #define X_CURRENT       800    *unchanged*
line 2616   #define Y_CURRENT       800    *unchanged*
line 2634   #define Z_CURRENT       800    *unchanged*
line 2697   #define E0_CURRENT      950    from 800      
line 4044   //#define WIFISUPPORT          (default)

C) For platformio.ini, this was updated:
====================================
line 17 default_envs = BIGTREE_SKR_2

D) For pins_BTT_SKR_V2_0_common.h
==================================
1) Commented out line 41 "#define FLASH_EEPROM_EMULATION" 

2) I added #define Z_MIN_PIN Z_STOP_PIN so it looks like this now:

line 129 //
line 130 // Z Probe (when not Z_MIN_PIN)
line 131 // 
line 132 #ifndef Z_MIN_PROBE_PIN
line 133  #define Z_MIN_PROBE_PIN                   PE4
line 134  #define Z_MIN_PIN Z_STOP_PIN
line 135 #endif

E) Wiring of my existing Delta's Mechanical Endstops
====================================================
Common and NC (default Anycubic 2017) wiring, therefore I am using GND/PC1(X), GND/PC3(Y), GND/PC0(Z) on the SKR 2.0 board


G) Compilation of BTT ESP8266-ESP-12S Wifi module [4MB]
==================================================
Go to Installing PlatformIO IDE Extension on VS Code, if you have not done so.

Go further down to create a New Project or add New Folder
- this is to make sure you do not mix this project with the SKR2 motherboard project

For Board: Espressif ESP8266 ESP-12E
 - although I am using a ESP-12S BTT module

ESP3D-WEBUI / ESP3D-2.1.1
=========================
Compile the source code
 - make sure it is error-free

Copy firmware.bin file to the mSDCard

Rename the firmware.bin to esp3d.bin

Insert mSDcard into SKR2 mSDcard slot and re-boot the SKR2 board

Check the new WiFi SSID and connect your PC to it

Load your browser and Type in http://192.168.0.1
 - the password for the SSID is 12345678 (default)
 - at this point, the printer's LCD panel shows "New Client"
 - you will see a non-complete GUI menu with "index.html" or "index.html.gz file" missing message

Click on the "Interface" at the web menu
 - it brings you to Github. Do not panic
 - download the ESP3D-Webui source code (at the bottom of the screen)
 - unzip the source.zip

Look out for the index.html.gz file at the root folder of this unzipped folder
Upload it to the ESP-12S module
 - within the same ESP3D web menu, there are "Select" / "Upload" functions
 - upload the "index.html.gz"

Refresh the same ESP3D page.
A whole new WEBUI screen appears (because of the index.html.gz"
Click the Configuration tab, SSP3D Setting tab.
Here, make some important changes, 
 - Select "client station", 
 - Input your inhouse wifi SSID and Password
   - this is so that your Delta's wifi module now accesses your local network as a client, rather than having its own AP ip address.
 - Make other changes, if you want to
 - you should be able to connect to your Delta within your own network

Finally ESP3D-WEBUI set up.

<!---
walterlootk/walterlootk is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
