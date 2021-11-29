I have an AnyCubic Kossel Pulley 3D printer since April 2017. It has been working well. However, in August 2021, the 8-bit Trigorilla board died, hence initiating a motherboard replacement. On the same month, I got hold of an SKR 2 Rev B board, four TMC2209 v1.2 stepper motor drivers and a BTT ESP8266 WIFI Module ESP-12S. Everything else has remain more or less the same.

Below were the changes that I made to the relevant configuration files to get the printer set up and all the bugs flushed out. Hope this helps someone, as I was helped many times by other contributors.

Marlin Configuration - for AnyCubic Kossel Pulley DELTA Printer<br>
Period of main configuration: 10-11-2021 ~ 26-11-2021

1) Marlin Firmware<br>
==========<br>
1.1 Configuration Samples version date: 04-10-2021, extracted from: https://github.com/MarlinFirmware/Configurations/archive/refs/tags/2.0.9.2.zip<br>
1.2 Marlin firmware from Marlin Website, is Marlin 2.0.9.2

2) Changes made to the following:<br>
====================<br>
A. Configuration.h<br>
B. Configuration_adv.h (no changes yet - just review)<br>
C. platformio.ini<br>
D. pins_BTT_SKR_V2_0_common.h


A) For Configuration.h, the following were done:<br>
================================<br>
** line numbers are closely approximate.

line 33 #define ANYCUBIC_PROBE_VERSION 2<br>
line 39 #define ANYCUBIC_KOSSEL_ENABLE_BED 1<br>
line 113 #define SERIAL_PORT 3 (for Wifi module)<br>
line 134 #define SERIAL_PORT_2 -1 (enabled for USB Connection)<br>
line 150 #define MOTHERBOARD BOARD_BTT_SKR_V2_0_REV_B<br>
line 154 #define CUSTOM_MACHINE_NAME "ANYCUBIC Kossel SKR2"<br>
line 797 //#define DELTA_HOME_TO_SAFE_ZONE (default is ENABLED) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- to prevent printhead from going down 70mm after homing (in my case)<br>
line 848 #define DELTA_HEIGHT 296.20 (from 320 to 296.20, via manual calibration)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- after manually "paper-level" the height, the auto calibration grid works<br>
line 958 #define X_DRIVER_TYPE  TMC2209<br>
line 959 #define Y_DRIVER_TYPE  TMC2209<br>
line 960 #define Z_DRIVER_TYPE  TMC2209<br>
line 969 #define E0_DRIVER_TYPE TMC2209<br>
line 1068 #define DEFAULT_ACCELERATION          2000<br>
line 1070 #define DEFAULT_TRAVEL_ACCELERATION   2000<br>
line 1137 //#define Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- DISABLED in order to use V2 Probe on PE4 else it crashes into BED<br>
line 1371 #define MULTIPLE_PROBING 3 (default is disable & 2)<br>
line 1464 #define INVERT_X_DIR false (default is ) true<br>
line 1465 #define INVERT_Y_DIR false (default is ) true<br>
line 1466 #define INVERT_Z_DIR false (default is ) true<br>
line 1474 #define INVERT_E0_DIR false (default is ) true<br>
line 1681 #define AUTO_BED_LEVELING_BILINEAR (default)<br>
line 1719 #define ENABLE_LEVELING_FADE_HEIGHT (default=disable)<br>
line 1750 #define GRID_MAX_POINTS_X 5 (default is 9)<br>
line 1892 #define HOMING_FEEDRATE_MM_M { (50&#42;60), (50&#42;60), (50&#42;60) }

B) For Configuration_adv.h, the following were noted/changed.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Guideline from https://www.lpomykal.cz/anycubic-kossel-skr-1-3-upgrade/<br>
=================================<br>
line 589 #define E0_AUTO_FAN_PIN FAN1_PIN (for Hotend FAN1, see line 601)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- as FAN_PIN is default defined for FAN0 -Parts Fan<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- using FAN1_PIN instead of PB6 is easier to read<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- COOLER_AUTO_FAN_PIN is for laser fan use only<br>
line 601 #define EXTRUDER_AUTO_FAN_TEMPERATURE 50 (default value)<br>
line 602 #define EXTRUDER_AUTO_FAN_SPEED 255   // 255 == full speed (default value)<br>
line 1142 #define MICROSTEP_MODES { 16, 16, 16, 16, 16, 16 } // [1,2,4,8,16] *unchanged*<br>
line 2598   #define X_CURRENT       800    *unchanged*<br>
line 2616   #define Y_CURRENT       800    *unchanged*<br>
line 2634   #define Z_CURRENT       800    *unchanged*<br>
line 2697   #define E0_CURRENT      950    from 800   <br>   
line 4044   //#define WIFISUPPORT          (default)

C) For platformio.ini, this was updated:<br>
=======================<br>
line 17 default_envs = BIGTREE_SKR_2

D) For pins_BTT_SKR_V2_0_common.h<br>
====================<br>
1) Commented out line 41 "#define FLASH_EEPROM_EMULATION"

2) I added #define Z_MIN_PIN Z_STOP_PIN so it looks like this now:

line 129 //<br>
line 130 // Z Probe (when not Z_MIN_PIN)<br>
line 131 // <br>
line 132 #ifndef Z_MIN_PROBE_PIN<br>
line 133  #define Z_MIN_PROBE_PIN                   PE4<br>
line 134  #define Z_MIN_PIN Z_STOP_PIN<br>
line 135 #endif

E) Wiring of my existing Delta's Mechanical Endstops<br>
===================================<br>
Common and NC (default Anycubic 2017) wiring, therefore I am using GND/PC1(X), GND/PC3(Y), GND/PC0(Z) on the SKR 2.0 board

F) Installation of the BTT ESP8266 ESP-12S Module
========================<br>
1. Switch off the power to the Delta printer
2. Follow the colour code of RED and BLACK and plug into the WIFI receptor

G) Compilation of BTT ESP8266-ESP-12S Wifi module [4MB]<br>
=================================<br>
1. Go to Installing PlatformIO IDE Extension on VS Code, if you have not done so.

2. Go further down to create a New Project or add New Folder<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- this is to make sure you do not mix this project with the SKR2 motherboard project

3. For Board: Espressif ESP8266 ESP-12E<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- although I am using a ESP-12S BTT module

H) ESP3D-WEBUI / ESP3D-2.1.1<br>
=================<br>
1. Compile the source code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- make sure it is error-free

2. Copy firmware.bin file to the mSDCard

3. Rename the firmware.bin to esp3d.bin

4. Insert mSDcard into SKR2 mSDcard slot and re-boot the SKR2 board

5. Check the new WiFi SSID and connect your PC to it

6. Load your browser and Type in http://192.168.0.1<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- the password for the SSID is 12345678 (default)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- at this point, the printer's LCD panel shows "New Client"<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- you will see a non-complete GUI menu with "index.html" or "index.html.gz file" missing message

7. Click on the "Interface" at the web menu<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- it brings you to Github. Do not panic<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- download the ESP3D-Webui source code (at the bottom of the screen)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- unzip the source.zip

8. Look out for the index.html.gz file at the root folder of this unzipped folder<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Upload it to the ESP-12S module<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- within the same ESP3D web menu, there are "Select" / "Upload" functions<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- upload the "index.html.gz"

9. Refresh the same ESP3D page.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- A whole new WEBUI screen appears (because of the index.html.gz"<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Click the Configuration tab, SSP3D Setting tab.
 
10. Here, make some important changes, <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Select "client station" <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Input your inhouse wifi SSID and Password<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;(this is so that your Delta's wifi module now accesses your local network as a client, rather than having its own AP ip address)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Make other changes, if you want to, such as DHCP or Static IP address<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- you should be able to connect to your Delta within your own network

Finally ESP3D-WEBUI set up.

<!---
walterlootk/walterlootk is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
