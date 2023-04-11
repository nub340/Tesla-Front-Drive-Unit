# How To Build a V8 Board From Scratch
The following is a step-by-step guide for building a V8 logic board. 

## Prerequesites
- A basic understanding and ability to work with Arduino-like microcontrollers
- Computer w/internet access
- Visual Studio Code with Platform IO extension installed
- ST-Link V2 USB adapter
- ST-Link Utility software
- USB to Serial TTL adapter
- Soldering iron and solder
- Hot air gun (optional)
- Some money

## Overview

### 1. Download the design files from [this](/) repository:
1. [Gerber.zip](gerber.zip) (PCB Design) - You may need to explore the repo history if you don't see it.
2. [Tesla_FDU_V8_BOM_JLC.xls](Tesla_FDU_V8_BOM_JLC.xls) (Bill of Materials)
3. [Tesla_FDU_V8_CPL.csv](Tesla_FDU_V8_CPL.csv) (Pick and Place)

### 2. Make or have a PCB made

There are at least three ways to go about it. Here is a quick summary of each approach:

1. **Made and assembled by JLCPCB (recommended)**

    The most popular approach is to have the PCBs made and assembled by an online service like JLCPCB. The board ultimately gets delivered to your door with all of the components soldered on and ready to go. The only thing left to do is to flash the ESP32 and STM32 modules with the latest firmware, solder on all of the connectors (x5), and finally the current sensors (x2). Usually you must order at least five boards, but you can choose to only assemble one of them if you want to reduce cost.

2. **Made by JLCPCB, assembled by YOU (budget/challenge)** 

    In this approach, you have empty PCBs made by JLCPCB, and assemble the board(s) yourself. You will need to purchase all of the components seperately using the "Bill of Materials" (BOM) file as your guide. You can actually upload the BOM file to [LSCS](https://www.lcsc.com/bom) to quickly order all of the parts. You will then need to solder on all of the components, most of them surface mount (SMT). This approach is a lot more work, but can reduce your costs even further. 

3. **Made by YOU, assembled by YOU (extreme/not necessary)**

    You *could* make the actual PCB yourself if you have the right equipment and knowhow. With services today like JLCPCB, it probably doesn't make too much sense to do this. However, if you feel up to the task it could be prove to be the most cost-effective and I'd love to see someone make one that way!

    ### To order fully assembled boards from JLCPCB.

    1. Go to `jlcpcb.com` and click the "Instant Quote" button.
    2. Add/upload PCB design file: `Gerber.zip`
    3. Customize PCB options (optional)
    4. Select "PCB Assembly" option  
    5. Upload Bill of Materials file: `Tesla_FDU_V8_BOM_JLC.xls`
    6. Upload Pick and Place file:  `Tesla_FDU_V8_CPL.csv`
    7. Resolve any component stock issues (i.e. select replacments)
    8. Finalize & order boards

### 3. Order PCB connectors and current sensors (per board)
*NOTE: Sensors are optional if you're re-using the existing sensors*
- 2x Current Sensors: https://www.mouser.com/ProductDetail/Melexis/MLX91209LVA-CAA-002-SP?qs=f9yNj16SXrLPMXdIg%2FG8eQ%3D%3D
- 2x 2-pin elbow connector: https://www.mouser.com/ProductDetail/306-S02BA-AIT2-1AK
- 1x 6-pin elbow connector: https://www.mouser.com/ProductDetail/306-S06B-AIT2-1AK
- 1x 20-pin Molex connector: https://www.mouser.com/ProductDetail/538-75757-5101
- 1x 18-pin low profile socket: https://www.mouser.com/ProductDetail/200-CES11201LD
- 1x 20-pin Molex harness connector: https://www.mouser.com/ProductDetail/538-33472-2002
- 20x Molex pins: https://www.mouser.com/ProductDetail/538-33012-2002-LP 

### 4. Program ESP32 Wifi Module
- Ensure the Platform IO Core CLI and VSCode Extension are installed
- Clone [esp32-web-interface](https://github.com/Pete9008/esp32-web-interface/tree/johu) repo, and open in VSCode
- Switch to `johu` branch!
- **Make sure STM32 module is erased or held in reset mode!**
- Ensure board is powered off
- Connect USB Serial Adapter to 6-pin ESP32 header: **Tx->Tx**, **Rx->Rx**, **GND->GND**.
- Ensure `PROG` and `GND` on board are bridged in order to boot into programming mode. You can use a 6-pin header with a jumper on these two pins.
- In **VSCode** > **Platform IO Tab** > **Project Tasks** > **Release** > **General**, click `Monitor` and wait for monitor terminal to open & connect
- Now apply +12V power to main connector. PINS: 13 & 19
- You should see text like, "waiting for download" appear in the monitor terminal.
- If you don't see "waiting for download" text, make sure `PROG` and `GND` are bridged and 6-pin header has good connection. Leave the `Monitor` terminal open and reboot the board. Also make sure STM32 is NOT running (see above). Proceed to next step once you see the text.
- In **VSCode** > **Platform IO Tab** > **Project Tasks** > **Release** > **General**, click `Upload` task and wait for success message
- After success, module will reboot back into normal mode. Re-enter programming mode again by rebooting the board with jumper still in place. Once again, you should see, "waiting for download" text.
- Manually close the monitor terminal instance, and any other terminal instance that might be connected to the ESP32.
- In **VSCode** > **Platform IO tab** > **Release** > **Platform**, click `Upload Filesystem Image` task and wait for success message
- If this fails, double-check that you don't have any other process connected to the ESP32
- You should see a Wifi network named ESP_XXXXXX. You should now be able to connect to it and see the web interface.

### 5. Program STM32 Module
- Download STM32 assets from the [stm32-sine](https://github.com/jsphuebner/stm32-sine/releases) repository
    - stm32-bootloader.hex
    - stm32-sine.hex
    - You may need to browse history of assets to find latest versions
- Connect ST-Link 2 Adapter to 3-pin STM32 header: **SWCLK->CLK**, **SWDIO->DAT**, **GND->GND**
- Run [**ST-Link Utility**](https://www.st.com/en/development-tools/stsw-link004.html)
- **ST-Link Utility** > **Connect**
- **ST-Link Utility** > **Open** > select the `stm32-sine.hex` file 
- **ST-Link Utility** > **Target** > **Program & verify** > confirm
- Repeat last two steps with `stm32-loader.hex` file
- Red LED on board should start blinking

### 6. Verify Programming
- Connect to the ESP_XXXXXX wifi network
- Browse to `192.168.4.1`
- Verify the web interface is visible and funtioning
- Verify you can see abd modify parameters
- Verify you can save chnges to flash

### 7. Solder on all the board connectors
- Solder 18-pin IGBT connector to **UNDERSIDE** of board
- Solder 20-pin main wiring harness connector to **UNDERSIDE** of board
- Solder both 2-pin thermistor connectors
- Solder single 6-pin thermistor connector

### 8. Remove stock Tesla logic board & current sensors from Inverter
- Remove the inverter from the Tesla SDU 
- Unscrew the two green T20 Torx screws from the orange plastic phase wire guide
- Remove the orange plastic phase wire guide from the three orange phase wires. Carefully work it **down**, towards the inverter body until you can get it off without breaking it.
- Unplug the two remaining plastic connectors
- Unscrew the five remaining T20 Torx screws
- Unscrew the three yellow T15 Torx screws immediately surrounding the blue Molex connector retaining clip. 
- Pop the board free from the 18-pin IGBT connector underneath using a plastic trim tool
- Carefully remove the logic board tray up and away from the inverter.

### 9. Current Sensors

Now comes the hard part. Once again, there are at least three main approaches:

1. Re-use the existing sensors, leaving them in-place in the sensor rings (ideal but most challenging)
    - Carefully scrape away the resin coating on the PCB and base of sensor legs, on both sides of the board as best as you can.
    - Desolder the sensors from the board, using solder wick, flux, etc
    - Lift the board off of the sensor legs, leaving the sensors embeded in-place in the sensor rings
    - Align the 8 tiny holes on the Open Inverter board with the 8 tiny sensor legs, and lower the board so that the legs go perfectly thru the holes in the board
    - Solder the sensor legs to the board

2. Re-use the existing sensors, removing them from and re-installing them into the sensor rings (easier)
    - Use a hot air gun to blow hot air on the current sensors embedded in the sensor ring resin. 
    - Be careful not to get it too hot, but hot enough so that the sensors come out of goop in the rings while still being connected to the board
    - Carefully scrape away the resin coating on the PCB and base of sensor legs, on both sides of the board as best as you can.
    - Desolder and remove the sensors from the Tesla board, using solder wick, flux, etc
    - Place the sensors back in the sensor ring holes left behind. 
    - Align the 8 tiny holes on the Open Inverter board with the 8 tiny sensor legs, and lower the board so that the legs go perfectly thru the holes in the board
    - Solder the sensor legs to the board

3. Install new sensors (easiest)
    - Use a hot air gun to blow hot air on the current sensors embedded in the sensor ring resin. 
    - Be careful not to get it too hot, but hot enough so that the sensors come out of goop in the rings while still being connected to the board
    - Place the new sensors in the sensor ring holes left behind Be sure to orient the sensors correctly. The 45 degree angle should be facing away from the board. 
    - Align the 8 tiny holes on the Open Inverter board with the 8 tiny sensor legs, and lower the board so that the legs go perfectly thru the holes in the board
    - Solder the sensor legs to the board

- Double check all of the connectors and make sure everything looks aligned correctly, no bent pins, etc. When satisfied, re-install the board back into the inverter. 
- Congratulations! You inverter has been unlocked and can be re-installed back into the motor. Have fun! 
