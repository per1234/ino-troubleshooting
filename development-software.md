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
   ```INI
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
  - Windows
    - Vista and newer:
      - Standard IDE: `C:\Users\{username}\AppData\Local\Arduino15`
      - Windows app: `C:\Users\{username}\Documents\ArduinoData\packages`
    - XP: `C:\Documents and Settings\{username}\Local Settings\Arduino15`
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

Try opening the site in an incognito window:

- If using the Google Chrome browser

1. Click the button with the three vertical dots at the top right corner of Chrome.
1. Click "New incognito window".
1. Open <https://create.arduino.cc/>

- If using the Firefox browser

1. File > New Private Window
1. Open <https://create.arduino.cc/>

Does that fix the problem? If so, to use Arduino Create in a regular window, try this:

1. Log out of your Arduino account:
1. Click your profile picture at the top right of any arduino.cc window.
1. Click "Sign out".
1. Clear the browser cache:

- If using the Google Chrome browser
  1. Click the button with the three vertical dots at the top right corner of Chrome.
  1. Click "History".
  1. Click "History".
  1. From the menu on the left side of the browser window, click "Clear browsing data".
  1. Uncheck all boxes except the one to the left of "Cached images and files", which you should check.
  1. Click the "Clear data" button.
- If using the Firefox browser
  1. **History > Clear Recent History...**
  1. Uncheck all boxes except the one to the left of "Cache", which you should check.
  1. Click the "Clear Now" button.

1. Sign in to your Arduino account:
1. Open https://create.arduino.cc/
1. At the top right corner of the Arduino Create page, click "Sign in".

If the problem persists after that, it might be caused by a browser extension. Disable browser extensions that might cause interference (e.g., ad or script blockers).

---

#### Where is the Arduino Create Plugin for Raspberry Pi or other ARM platforms?

Arduino doesn't provide a plugin version for the Raspberry Pi. You'll need to compile and install the plugin yourself. You can find instructions here:
<https://github.com/arduino/arduino-create-agent#development>

There is no installer, so you'll need to do that yourself, including installing the certificates. There's some information about that here:
<https://forum.arduino.cc/index.php?topic=411290>
Note that topic is from 2016, so using the pre-compiled plugin @facchinm offers is not recommended, since it's outdated now.

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

#### How do I make Arduino Web Editor use a specific version of a library?

1. From the menu on the left side of the Arduino Web Editor window, click "Libraries".
1. Click the "Library Manager" button.
1. In the "Search Library" field, type the name of the library you want to specify the version of.
1. Click the star to the right of the library name, so that it is colored in.
1. Click the "Done" button.
1. Click the "Favorites" tab.
1. In the list of favorites, hover your mouse over the name of the library.
1. Click the downward facing triangle on the right side of the "Include" button.
1. From the dropdown menu, select the version number you want to use. This will cause the sketch you currently have open in Arduino Web Editor to use that version of the library.

---

#### Arduino Web Editor is using the wrong library

Every one of the thousands of libraries in the Arduino Library Manager index come pre-installed in the Arduino Web Editor. This is very convenient, but it also means that for any common file name you use in an `#include` directive, there will often be multiple libraries that contain a file of that name. When this occurs, Arduino Web Editor attempts to automatically pick the best library, but doesn't always get it right. Fortunately, libraries you import will be given preference over the pre-installed libraries. So the solution is to import the library you want to use:

1. Download the library you want to use. If the library is on GitHub and you don't know how to download it, see [How do I download a library from GitHub?](miscellaneous.md#download-from-github)
1. From the menu on the left side of the Arduino Web Editor window, click "Libraries".
1. Click the upward pointing arrow button (Import).
1. If you get a pop up about importing your sketchbook, click "Import".
1. Select the downloaded library file.
1. Click the "Open" button.
1. Wait for Arduino Web Editor to indicate the library has been successfully imported.

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

#### General troubleshooting advice for adding a board

Check the output in the Arduino Create Debug Console:

1. In your system tray (AKA "notification area" on Windows), click the Arduino logo.
1. Click "Open debug console". This should open the Arduino Create Agent Debug Console in your browser.
1. Uncheck the box next to "Toggle List". This suppresses some unhelpful output that will only make it harder to find the useful information.
1. Leaving the Arduino Create Agent Debug Console browser tab open, attempt to add a new board via Arduino Create.
1. Once the process fails, switch to the Arduino Create Agent Debug Console browser tab and examine the output for clues to the cause of the failure.

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
