---
title: Development Software
description: Troubleshooting problems with the Arduino IDE, arduino-cli, Arduino Pro IDE, Arduino Web Editor, or Arduino IoT Cloud
---

---
### General troubleshooting advice
---

#### Install the latest version of the development software you're using
Arduino's development software is continuously being improved. By using an old version you may be missing out on the fix for the bug that's causing your problem.

Always download the Arduino IDE from <https://www.arduino.cc/en/Main/Software>. The version of the Arduino IDE available via Linux and macOS package managers is outdated and/or non-standard.

The hourly/nightly/alpha/beta builds of the development software are only intended to be used for beta testing. They are not recommended for normal usage because they are more likely to have bugs.

---
## Arduino IDE
---
### General
---

#### The Arduino IDE doesn't start
Start the Arduino IDE from the command line to get some output that may give helpful clues to the cause of the problem. Windows users should run arduino_debug.exe, found in the root of the Arduino IDE installation folder.


---
#### Text in the Arduino IDE looks like gibberish
<!-- https://github.com/arduino/Arduino/pull/9044 -->
For Windows only:
1. Close all Arduino IDE windows.
1. Open the file `arduino.l4j.ini`, located in the Arduino IDE installation folder, in a text editor.
1. Add this line:
    ```
    -Dawt.useSystemAAFontSettings=on
    ```
1. Save the file
1. Start the Arduino IDE. The text should now be normal.


---
#### My board is not listed in the **Tools > Board** menu
Additional boards may be installed via **Tools > Board > Boards Manager**. See: <https://www.arduino.cc/en/guide/cores>


---
#### Arduino Web Editor as an alternative
If you can't get the Arduino IDE to work, you can try using the [Arduino Web Editor](https://create.arduino.cc/editor) instead.


---
### Installation
---

#### Windows 8.1 or 10 is required, but I'm using an older version of Windows
The Arduino IDE is compatible with Windows versions 7 and newer (Windows XP is also supported with Arduino IDE 1.8.9 and older). It's only the "Windows app" version of the Arduino IDE that requires Windows 8.1 or newer. Please use either the "Windows Installer" or "Windows ZIP file for non admin install" download links at <https://www.arduino.cc/en/Main/Software>.


---
#### How do I do a complete uninstallation of the Arduino IDE?
The Arduino IDE uses files in three locations on your computer:
- IDE installation folder
- Sketchbook folder. The sketchbook folder location is shown in the Arduino IDE at **File > Preferences > Sketchbook location**.
- Data folder
  - On Windows:
    - Standard IDE: `C:\Users\{username}\AppData\Local\Arduino15`
    - Windows app: `C:\Users\{username}\Documents\ArduinoData\packages`
  - macOS: `/Users/{username}/Library/Arduino15`
  - Linux: `~/.arduino15`

When you use the uninstaller, only the IDE installation folder will be removed, so after reinstalling, the files left over in the other locations will still affect the new installation. Deleting the data folder can often solve problems with the Arduino IDE not starting or failing compilation in unexpected ways.


---
### Editor
---

#### The editor window is gray and I can't type anything in it
Do this:
1. **File > Preferences**
1. Uncheck the box next to "Use external editor".
1. Click the "OK" button.

---
#### When I type some function or statement, it doesn't turn orange
Keywords are highlighted in the Arduino IDE in several colors. One of these is a dark green difficult to distinguish from the standard black text.

Some library authors don't define their keywords. Keyword highlighting doesn't make any difference to the functioning of the code. It is merely cosmetic.


---
### Boards Manager
---

#### `Error Downloading...`
Verify that you can open the URL in your browser. Your computer must be connected to the Internet to use Boards Manager.

Make sure your firewall isn't blocking the Arduino IDE from accessing the Internet.

If you're using a network proxy, configure it in the "Network" tab of **File > Preferences**.

After clicking the "Close" button, if you check the contents of the black console window at the bottom of the Arduino IDE window, you will find a detailed error output for the failed download. You will need to scroll up the window to see it all.

If the error is about a URL other than `https://downloads.arduino.cc/packages/package_index.json`, verify that the URL you entered in **File > Preferences > Additional Boards Manager URLs** is correct. Open the URL in your browser to verify it can be accessed. If you have multiple URLs in that field, they should be separated by commas.


---
#### `file signature verification failed. File ignored`
1. (In the Arduino IDE) **File > Preferences**
1. Click the link at the line following "More preferences can be edited directly in the file". This will open the `Arduino15` (or similar name depending on OS) folder.
1. Delete all files in that folder except for preferences.txt. Please be very careful when deleting things on your computer. When in doubt, back up!
1. (In the Arduino IDE) Close the Boards Manager window if it's open.
1. **Tools > Board > Boards Manager**
1. Wait for the downloads to finish.


---
#### The package I want to install doesn't appear in Boards Manager
For 3rd party hardware packages, it is first necessary to add the URL of their Boards Manager JSON file to **File > Preferences > Additional Boards Manager URLs**. If you have multiple URLs in that field, separate them with commas.


---
## arduino-cli
---

#### `Error creating instance: There were errors loading platform indexes`
You need to update the indexes with this command:
```shell
arduino-cli core update-index
```
If you are using a 3rd party core that requires a Boards Manager URL, specify that URL in the command using the `--additional-url` flag, or define it in arduino-cli.yaml.


---
#### `Error during install: finding platform dependencies: package ... not found`
You need to specify the core's Boards Manager URL via the `--additional-url` flag:
```shell
arduino-cli core install esp8266:esp8266 --additional-url https://arduino.esp8266.com/stable/package_esp8266com_index.json
```
Alternately, you can define the URL in arduino-cli.yaml:
```yaml
board_manager:
  additional_urls:
    - https://arduino.esp8266.com/stable/package_esp8266com_index.json
```


---
#### `arduino-cli board list` is showing my board as "Unknown", with no FQBN and Core
This is the expected behavior for boards that use a USB to TTL serial adapter chip with a generic VID/PID, such as FTDI FT232, CH340, CP2102, or PL2303. There is no way for [arduino-cli](https://github.com/arduino/arduino-cli) to know which board these chips are used on. You can still use the board with [arduino-cli](https://github.com/arduino/arduino-cli) by specifying the correct FQBN in the commands.


---
#### I can't set the options for my board that are shown in the Arduino IDE's **Tools** menu
You specify these options via the `--fqbn` flag. For example, to upload a sketch named Foo to a Nano board with the **Tools > Processor > ATmega328P (Old Bootloader)** option:
```shell
arduino-cli upload --fqbn arduino:avr:nano:cpu:atmega328old Foo
```
You can determine the option name and values by checking boards.txt, or examining the verbose compilation output shown in the console after compiling for the board in the Arduino IDE.


---
## Arduino Create
---

#### General troubleshooting advice
Log out of your Arduino account and log in again.

Clear your browser cache.

Try using a "private" or "incognito" window in your browser.

Disable browser extensions that might cause interference (e.g., ad or script blockers).


---
### Arduino Web Editor
---

#### `parse library.properties: library.properties not found`
Unlike the Arduino IDE, [Arduino Web Editor](https://create.arduino.cc/editor) requires even libraries in the 1.0 format to contain a library.properties file. In order to import this library, you'll need to:
1. Unzip the library's .zip file.
1. Delete the library's .zip file.
1. Create a file named library.properties in the library's root folder.
1. Open library.properties in a text editor.
1. Add text to the file following the Arduino Library Specification: <https://github.com/arduino/Arduino/wiki/Arduino-IDE-1.5:-Library-specification#libraryproperties-file-format>. Don't worry too much about exactly what you fill the fields with, it's mainly just the presence of a valid library.properties file that's important.
1. Save the file
1. Zip the library folder
1. Try the import again in [Arduino Web Editor](https://create.arduino.cc/editor).


---
#### Arduino IDE as an alternative
If you can't get the [Arduino Web Editor](https://create.arduino.cc/editor) working, an alternative is to use the Arduino IDE.


---
### Arduino Create Chrome app
---

#### The "Try Now" or "Buy" buttons for Arduino Create app in the Chrome Web Store are grayed out and disabled
If you are using the G Suite for Education, you are required to use one of the educational plans available from:
<https://create.arduino.cc/plans/chrome-app>


---
### Arduino IoT Cloud
---

#### `UNABLE TO CONNECT`, `We could not find any board.`
The supported boards for [Arduino IoT Cloud](https://www.arduino.cc/en/IoT/HomePage) are shown in the "Setup an IoT Board" section of this page:
<https://create.arduino.cc/getting-started/>
If your board is not listed on that page, you can't use it with [Arduino IoT Cloud](https://www.arduino.cc/en/IoT/HomePage).

See ["The **Tools > Port** menu is grayed out, or the port of my Arduino board doesn't appear in the **Tools > Port** menu, or Arduino Web Editor doesn't recognize my board"](uploading.md#no-port).


---
#### `We weren't able to configure the Crypto Chip. Timeout reached before we could configure your board.` or `We couldn't connect to the Arduino Cloud.`
<!-- I have received two types of foam with my Arduino boards. One is very springy when compressed and has very high resistance (I can't measure it). The other doesn't bounce back after being compressed and has relatively low resistance. I suspect it is only the latter type of foam that causes the problems. -->
Some Arduino boards come with their pins inserted into anti-static foam. This foam is conductive, so you need to remove it before using the board.

Try removing anything you have connected to the I2C pins (11 and 12 on MKR boards, A4 and A5 on Nano 33 IoT) on your board. The crypto chip is connected to those pins. Use of these pins with other than I2C devices can interfere with the communication with the crypto chip.

Make sure the board is not sitting on anything conductive that might be shorting out the pins.
