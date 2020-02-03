---
title: Uploading
description: Troubleshooting problems with uploading your sketch
---

---
### General troubleshooting advice
---

#### Make sure it's actually an upload problem
The Arduino IDE compiles your code before uploading, so a bug in your code will also cause the upload to fail. But that is not an upload error. If you do a **Sketch > Verify/Compile** and still get a failure then you need to fix the compilation error before you have any hope of uploading. See the ["Compiling" section of this guide](compiling.md) for help with compilation errors.


---
#### Check the verbose output
The first thing to do when you have an upload problem is to turn on verbose output:
1. **File > Preferences**
1. Check the box next to "Show verbose output during: upload".
1. Click the "OK" button.

Now try uploading again. This time you will see useful information about the upload displayed in the black console window at the bottom of the Arduino IDE/[Arduino Web Editor](https://create.arduino.cc/editor) window. Note that the text will likely be longer than can be seen at once in the console window so you need to use the scrollbar on the right side to view it all.


---
#### Make sure you have the right board selected in the **Tools > Board** menu
Selecting the wrong board can result in an upload failure. After selecting the correct board from the **Tools > Board** menu, additional custom menus may appear under the Tools menu (e.g. **Tools > Processor**). Make sure you have the correct selections in those menus as well.


---
### Specific issues
---

<a id="determine-port"></a>
#### How do I know which port is my Arduino board?

Sometimes the port will be labeled with the board name in the **Tools > Port** menu. Other times it will not. If you don't know which port is your Arduino, you can find it like this:

Arduino IDE:
1. Unplug your Arduino board from the computer.
1. **Tools > Port**
1. Note the ports, if any, listed in the menu.
1. Close the **Tools** menu.
1. Plug your Arduino board into the computer.
1. **Tools > Port** - The new port listed in the menu is your Arduino board.

Arduino Web Editor:
1. Unplug your Arduino board from the computer.
1. Click the board menu at the top of the [Arduino Web Editor](https://create.arduino.cc/editor) window.
1. Click "Other Board & Port".
1. Note the ports, if any, listed in the menu.
1. Close the "Other Board & Port" window.
1. Plug your Arduino board into the computer.
1. Click the board menu at the top of the [Arduino Web Editor](https://create.arduino.cc/editor) window.
1. Click "Other Board & Port". The new port listed in the menu is your Arduino board.


---
<a id="no-port"></a>
#### The **Tools > Port** menu is grayed out, or the port of my Arduino board doesn't appear in the **Tools > Port** menu, or Arduino Web Editor doesn't recognize my board
Make sure your USB cable is fully inserted into the socket on the Arduino board and the computer.

<!--
I have received two types of foam with my Arduino boards. One is very springy when compressed and has very high resistance (I can't measure it). The other doesn't bounce back after being compressed and has relatively low resistance. It is only the latter type of foam that causes the problems.
I have only been able to reproduce this issue with MKR1000, MKR FOX 1200, and MKR Vidor 4000. All other boards get a port even with the foam.
-->
Some Arduino boards come with their pins inserted into anti-static foam. This foam is conductive, so you need to remove it before using the board.

You may need to install the driver for the USB to TTL serial adapter chip on your Arduino. Common USB chips:
- Microchip ATmega16U2 (marked "MEGA16U2") or ATmega8U2 (marked "MEGA8U2"): The driver is included with the Arduino IDE in the drivers subfolder of the Arduino IDE.
- FTDI FT232 (marked "FT232..."): The driver is included with the Arduino IDE in the drivers subfolder of the Arduino IDE.
- WCH CH340 (marked "CH340G", the most common chip on clone boards): <http://www.wch.cn/download/CH341SER_EXE.html>
- Silicon Labs CP210x (e.g. CP2102 or CP2104): <http://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers>
- Prolific PL2303 (marked "PL-2303..."): <http://www.prolific.com.tw/US/ShowProduct.aspx?pcid=41>

You may be using a charge-only or defective USB cable. Try a different cable or test your cable on another device to confirm that it works for data.

<!--
Affected boards:
MKR WiFi 1010
MKR WAN 1310
MKR GSM 1400
MKR NB 1500
MKR Vidor 4000? (untested, but it has the same BQ24195L power management chip as the others)
-->
If using a MKR board, remove any connections to pins 11 and 12. Many of the MKR boards have a power management chip that is controlled over I2C. If the I2C pins (11 and 12) are used for other than I2C devices, the communication with the power management chip may be disrupted (often indicated by the "CHRG" light continuously flickering) and no port appears.

Remove all external wiring, shields, modules, etc. from your Arduino board to see if it is the cause of the problem.

If you have a native USB board (e.g., Leonardo, Micro, MKR, Nano 33 IoT, Nano 33 BLE), see ["Native USB board doesn't get a port, or is failing uploads"](#native-usb-reset).

If your board uses the ATmega16U2 USB to TTL serial adapter chip (marked "MEGA16U2" on the top of the chip), and you are using Windows, check Device Manager. If a device called "ATmega16u2 DFU" appears when you plug your board into the computer, this means the Arduino firmware is missing from the ATmega16U2. You can replace it by following these instructions:
<https://www.arduino.cc/en/Hacking/DFUProgramming8U2>


---
<a id="native-usb-reset"></a>
#### Native USB board doesn't get a port, or is failing uploads
If you have an ATmega32U4-based board (e.g., Leonardo, Micro, Pro Micro), and are using the Arduino IDE, try this:
1. **File > New**
1. **Sketch > Upload**
1. Watch the black console window at the bottom of the Arduino IDE window.
1. Wait until you see something like this:
    ```
    Sketch uses 444 bytes (1%) of program storage space. Maximum is 30720 bytes.
    Global variables use 9 bytes (0%) of dynamic memory, leaving 2039 bytes for local variables. Maximum is 2048 bytes.
    ```
1. Press and release the reset button (or momentarily connect a wire between the GND and RST pins on the Pro Micro). If you are using a Yun, you need to press and release the "32U4 RST" button quickly twice. The upload should now proceed successfully.

If you have an ATmega32U4-based board (e.g., Leonardo, Micro, Pro Micro), and are using the [Arduino Web Editor](https://create.arduino.cc/editor), try this:
1. Click the "New Sketch" button.
1. Make sure you can see the black console window at the bottom of the [Arduino Web Editor](https://create.arduino.cc/editor) window. If you can't see it, drag the status bar up until you can.
1. Press and release the reset button (or momentarily connect a wire between the GND and RST pins on the Pro Micro). If you are using a Yun, you need to press and release the reset button quickly twice.
1. Wait for the [Arduino Web Editor](https://create.arduino.cc/editor) to show a notification that your board has been connected.
1. Immediately press the right pointing arrow button (Upload).
1. Watch the black console window at the bottom of the Arduino IDE/[Arduino Web Editor](https://create.arduino.cc/editor) window.
1. Wait until you see something like this:
    ```
    Sketch uses 444 bytes (1%) of program storage space. Maximum is 30720 bytes.
    Global variables use 9 bytes (0%) of dynamic memory, leaving 2039 bytes for local variables. Maximum is 2048 bytes.
    ```
1. Press and release the reset button (or momentarily connect a wire between the GND and RST pins on the Pro Micro). If you are using a Yun, you need to press and release the reset button quickly twice. The upload should now proceed successfully.

If you have a SAMD-based board (e.g., MKR, Nano 33 IoT), or a Nano 33 BLE board, try this:
1. Press and release the reset button twice quickly. You should now see the onboard LED pulsing.
1. Select the port of your board from the Arduino IDE's **Tools > Port** menu.
1. **File > New**
1. **Sketch > Upload**

If the problem returns after you upload your previous sketch again, this indicates something in the sketch is breaking or stopping the USB code that runs in the background. Possible causes:
- Putting the microcontroller to sleep.
- Overriding the core library's `main()` function by defining one in the sketch.
- On a Nano 33 BLE board, Mbed OS has crashed (see ["My program stopped working on my Nano 33 BLE board and the onboard LED is blinking"](runtime.md#mbedos-crash)).


---
#### I don't know which **Tools > Programmer** menu selection to use
The **Tools > Programmer** menu selection is only used when you are doing **Tools > Burn Bootloader** or **Sketch > Upload Using Programmer**. When you are doing a standard upload, the **Tools > Programmer** menu selection is completely ignored, so it doesn't matter what you have it set to.


---
#### `avrdude: ser_open(): can't open device ...: The system cannot find the file specified.` or `Couldn't find a Board on the selected port` or `the selected serial port does not exist or your board is not connected`
<!--
Upload to non-existent port:
- Uno: avrdude: `ser_open(): can't open device "\\.\COM10": The system cannot find the file specified.`
- Mega: same as Uno
- Leonardo: `Couldn't find a Board on the selected port. Check that you have the correct port selected.  If it is correct, try pressing the board's reset button after initiating the upload.`
- MKR: `Couldn't find a Board on the selected port. Check that you have the correct port selected.  If it is correct, try pressing the board's reset button after initiating the upload.`

Upload to board w/o bootloader
- Leonardo: `Couldn't find a Board on the selected port. Check that you have the correct port selected.  If it is correct, try pressing the board's reset button after initiating the upload.`
-->
Select the port of your Arduino from the **Tools > Port** menu.

If you don't know which is the port, see ["How do I know which port is my Arduino board?"](#determine-port)

If the **Port** menu is grayed out, or the port of your Arduino does not appear on the menu, see ["The **Tools > Port** menu is grayed out, or the port of my Arduino board doesn't appear in the **Tools > Port** menu, or Arduino Web Editor doesn't recognize my board"](#no-port).


---
#### `Error uploading, check if the selected board is currently available`
<!-- Arduino Web Editor-specific error for when no port is selected -->
See ["The **Tools > Port** menu is grayed out, or the port of my Arduino board doesn't appear in the **Tools > Port** menu, or Arduino Web Editor doesn't recognize my board"](#no-port).


---
#### `avrdude: ser_open(): can't open device ...: Permission denied`
Follow the instructions at: <https://www.arduino.cc/en/guide/linux#toc6>

If the problem persists after that, try uninstalling modemmanager.


---
#### `avrdude: butterfly_recv(): programmer is not responding` or `avrdude: error: programmer did not respond to command` or `No device found on … An error occurred while uploading the sketch`
<!--
Upload to wrong port:
- Leonardo:
  - `avrdude: butterfly_recv(): programmer is not responding`
  - `avrdude: error: programmer did not respond to command`
- MKR: `An error occurred while uploading the sketch`
-->
Make sure you have selected the port of your Arduino from the **Tools > Port** menu.

If you don't know which port it is, see ["How do I know which port is my Arduino board?"](#determine-port).

If the **Tools > Port** menu is grayed or the port of your Arduino does not appear on the menu, see ["The **Tools > Port** menu is grayed out, or the port of my Arduino board doesn't appear in the **Tools > Port** menu, or Arduino Web Editor doesn't recognize my board"](#no-port).

If the correct port is already selected, see ["Native USB board doesn't get a port, or is failing uploads"](#native-usb-reset).


---
#### `avrdude: stk500_recv(): programmer is not responding`, `avrdude: stk500_getsync() attempt ...: not in sync` or `avrdude: stk500v2_ReceiveMessage(): timeout`, `avrdude: stk500v2_getsync(): timeout communicating with programmer`
<!--
Upload to wrong port:
- Uno:
  - `avrdude: stk500_recv(): programmer is not responding`
  - `avrdude: stk500_getsync() attempt ...: not in sync`
- Mega:
  - `avrdude: stk500v2_ReceiveMessage(): timeout`
  - `avrdude: stk500v2_getsync(): timeout communicating with programmer`

Upload to board w/o bootloader
- Uno:
  - `avrdude: stk500_recv(): programmer is not responding`
  - `avrdude: stk500_getsync() attempt 1 of 10: not in sync`
- Mega:
  - `avrdude: stk500v2_ReceiveMessage(): timeout`
  - `avrdude: stk500v2_getsync(): timeout communicating with programmer`
-->
If you have a clone or old official Nano: Select **Tools > Processor > ATmega328P (Old Bootloader)**.

If you have a new (>=2018) official Nano: Make sure you have Arduino AVR Boards 1.6.21 or newer installed (via **Tools > Board > Boards Manager**) and select **Tools > Processor > ATmega328P**.

Make sure you have selected the port of your Arduino board from the **Tools > Port** menu.

If you don't know which is the port, see ["How do I know which port is my Arduino board?"](#determine-port).

If the port of your Arduino does not appear on the menu, see ["The **Tools > Port** menu is grayed out, or the port of my Arduino board doesn't appear in the **Tools > Port** menu, or Arduino Web Editor doesn't recognize my board"](#no-port).

Remove any connections to pins 0 and 1 on your Arduino board. These pins are used for communication with your computer, including uploads. Connecting anything to these pins can interfere with uploads.

Remove all external wiring, shields, modules, etc. from your Arduino board to see if it is the cause of the problem.

If your board has an ATmega16U2 USB to TTL serial adapter chip (marked "MEGA16U2" on top), do a loopback test:
1. Disconnect power from the board.
1. Remove all connections and shields from the board.
1. Force the processor to remain in reset by connecting a jumper wire from the RESET to the GND pin.
1. Connect a jumper from the RX pin (Arduino pin 0) to the TX pin (Arduino pin 1).
1. Connect the board to your computer.
1. Select the port of your board from the **Tools > Port** menu.
1. **Tools > Serial Monitor**
1. Type some text into the input field at the top of the Serial Monitor window.
1. Press Enter. If the text you typed is shown in Serial Monitor's output window, the loopback test passed. If the text was not shown, the loopback test failed.
1. Close Serial Monitor.
1. Disconnect the board from the computer.
1. Remove the two jumper wires.

If the loopback test failed, the USB to TTL serial adapter is damaged and it's unlikely to be worth repairing your board.

If the loopback test passed, or if you skipped the loopback test, try doing a Burn Bootloader (see <a href="burn-bootloader.html#burn-bootloader" target="_blank">"How do I burn the bootloader?"</a>).

If you have problems while burning the bootloader, see the ["Burn Bootloader" troubleshooting page](burn-bootloader.md).

There is a bug in the "Old Bootloader" Nano and Pro Mini's bootloaders that causes an endless reset loop after a reset caused by the watchdog timer, which results in this error when uploading accompanied by the onboard LED blinking rapidly. The way to recover from this is to burn the bootloader (see <a href="burn-bootloader.html#burn-bootloader" target="_blank">"How do I burn the bootloader?"</a>). You can avoid having this problem happen again by installing a bootloader that doesn't have the bug. If you have an ATmega328P @ 16 MHz board, select **Tools > Board > Arduino Uno** before burning the bootloader and use the board as an Uno thereafter. If your board has a different configuration, use [MiniCore](https://github.com/MCUdude/MiniCore), and use the board with that MiniCore board selection thereafter.


---
#### `avrdude: ser_open(): can't open device ...: Access is denied.` or `Error touching serial port` or `openPort(); Exception type - Port busy.` or `Error opening serial port '...'. (Port busy)`
<!--
Upload when the port is open in another program:
- Uno: `avrdude: ser_open(): can't open device "\\.\COM9": Access is denied.`
- Mega:
  - `avrdude: ser_open(): can't open device "\\.\COM9": Access is denied.`
  - `avrdude: ser_recv(): read error: The handle is invalid.`
- Leonardo:
  - `Error touching serial port 'COM9'.`
  - `openPort(); Exception type - Port busy.`
- MKR:
  - `Error touching serial port 'COM9'.`
  - `openPort(); Exception type - Port busy.`
-->
Make sure the port isn't open in any other program.

Sometimes the port gets stuck open. Restarting your computer will resolve that issue.


---
#### `Cannot run program "___REMOVE___...`
See ["fork/exec ... : no such file or directory or exec: ... : file does not exist or Cannot run program "___REMOVE___..."](compiling.md#delete-arduino15)


---
#### `WARNING: invalid value for unused bits in fuse "fuse5"` or `avrdude: jtagmkII_initialize(): Cannot locate "flash" and "boot" memories in description`
This warning is normal, expected, and doesn't indicate any problem. Please ignore it.


---
#### `SAM-BA operation failed`
If you are using a MKR Vidor 4000, update the bootloader following these instructions: <https://forum.arduino.cc/index.php?topic=563513.msg3839894#msg3839894>


---
#### I can only upload once, then I need to burn bootloader before I can do the next upload
In order to upload, the bootloader needs to be activated. This is done by resetting the microcontroller. However, the bootloader only runs for a short time before timing out and switching to running your sketch so the reset has to be timed just right during the upload. Normally, we have an auto-reset circuit set up that resets the microcontroller at just the right time during the upload. The auto reset circuit consists of the DTR or RTS on the USB to TTL serial adapter chip connected to the reset pin via a 0.1 uF capacitor. You can use either DTR or RTS, it doesn't matter which. This converts the signal on the DTR or RTS pin into a reset pulse at the right moment of the upload.

If you don't have an auto-reset circuit, you'll need to manually reset your Arduino at just the right time during the upload:
1. **Sketch > Upload**
1. Watch the black console window at the bottom of the Arduino IDE window until you see something like this:
    ```
    Sketch uses 444 bytes (1%) of program storage space. Maximum is 30720 bytes.
    Global variables use 9 bytes (0%) of dynamic memory, leaving 2039 bytes for local variables. Maximum is 2048 bytes.
    ```
1. Press and release the reset button. The upload should now finish successfully.

The reason why the reset (automatic or manual) is not needed on the first upload after burning the bootloader is because the microcontroller's flash memory is erased during the **Burn Bootloader** process, which causes the bootloader to run constantly until the first upload is done.
