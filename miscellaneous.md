---
title: Miscellaneous
description: Troubleshooting other problems
---

#### The LED on my Arduino board is red and/or blinking. What's wrong?
An LED being red doesn't mean it's a signal of a problem. The built-in LEDs on Arduino boards are generally always the same color. They don't change color to red to indicate an issue. Some manufacturers will use different colored LEDs than others.

New Arduino boards often arrive with a program running that blinks the on-board LED. This is not an indication of a problem.


---
#### How can I download my sketch from my Arduino board?
While it is possible to download the compiled program from the Arduino board, it is not possible to recover your original sketch from that file. You could get the assembly language listing from the compiled program, but this will have no resemblance to your sketch. It would be faster to just write your sketch all over again than to convert the assembly back into a standard Arduino sketch.

If you only want to get the compiled program so you can upload it in an unmodified state to other Arduino boards that use the same configuration as your current board, then this is worth doing. It can't be done via the Arduino IDE; you'll need to use the upload tool directly from the command line. Below is an example of how to do this with an AVR-based Arduino board (e.g., Uno, Nano, Leonardo, Mega). A similar procedure could likely be used with boards of other architectures, but you'll need to consult the upload tool's documentation to understand the exact modifications that need to be made to the command.

First, we're going to do a dummy upload in the Arduino IDE in order to get it to help us generate the avrdude command used to read the program from the Arduino board:
1. Plug your Arduino board into your computer.
1. Select your board from the Arduino IDE's **Tools > Board** menu.
1. Select your board's port from the **Tools > Port** menu.
1. **VERY IMPORTANT**: Unplug your board from your computer.
1. **File > Preferences**
1. Check the box next to "Show verbose output during > upload".
1. Click "OK".
1. **Sketch > Upload**
1. Wait for the upload to fail.
1. Scroll up the black console window at the bottom of the Arduino IDE window until you see the avrdude command that was generated for the upload. It will look something like this:
    ```Batchfile
    E:\Arduino\hardware\tools\avr\bin\avrdude -CE:\Arduino\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -carduino -PCOM17 -b115200 -D -Uflash:w:C:\Users\per\AppData\Local\Temp\arduino_build_91864/sketch_jan22b.ino.hex:i
    ```
1. Select the full text of the upload command.
1. Press **Ctrl + C**. This will copy the upload command to the clipboard.

Next, you need to modify the upload command to read the hex file out of your Arduino board:
1. Start a text editor program.
1. In the text editor window, press **Ctrl + V**. This will paste the command into the text editor. The end of the command will look something like this:
    ```Batchfile
    -D -Uflash:w:C:\Users\per\AppData\Local\Temp\arduino_build_91864/sketch_jan22b.ino.hex:i
    ```
    That is the part of the command that tells it to write.
1. Replace that part of the command with [the command that tells AVRDUDE to read](https://www.nongnu.org/avrdude/user-manual/avrdude_4.html#Option-Descriptions):
    ```Batchfile
    -Uflash:r:readfile.hex:i
    ```
    That will cause the read file to be named "readfile.hex", which will be saved to whichever folder you run the command from. So now the full command looks something like this:
    ```Batchfile
    E:\Arduino\hardware\tools\avr\bin\avrdude -CE:\Arduino\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -carduino -PCOM17 -b115200 -D -Uflash:r:readfile.hex:i
    ```
1. If the paths in the command contain spaces, wrap the paths in quotes.
1. Plug your Arduino board into your computer.
1. Copy and paste the command from the text editor to the command line and run it.
1. Wait for the command to finish successfully.

You can follow a similar procedure to write the file to another Arduino board. Remember that this file was compiled specifically for the Arduino board you read it from. You can't use it with an Arduino board that has a different configuration. For example, if you read it from an Uno, it is compiled for an ATmega328P running at 16 MHz with a boot section of 0.5 kB.

First, we're going to do a dummy upload in the Arduino IDE in order to get it to help us generate the avrdude command used to write the program to the Arduino board:
1. Plug the Arduino board you want to write to into your computer.
1. Select your board from the Arduino IDE's **Tools > Board** menu.
1. Select your board's port from the **Tools > Port** menu.
1. **File > Preferences**
1. Check the box next to "Show verbose output during > upload".
1. Click "OK".
1. **Sketch > Upload**
1. Wait for the upload to finish.
1. Scroll up the black console window at the bottom of the Arduino IDE window until you see the avrdude command that was generated for the upload. It will look something like this:
    ```Batchfile
    E:\Arduino\hardware\tools\avr\bin\avrdude -CE:\Arduino\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -carduino -PCOM17 -b115200 -D -Uflash:w:C:\Users\per\AppData\Local\Temp\arduino_build_91864/sketch_jan22b.ino.hex:i
    ```
1. Select the full text of the upload command.
1. Press **Ctrl + C**. This will copy the upload command to the clipboard.

Next, you need to modify the upload command to write the hex file you read from your other Arduino board:
1. Start a text editor program.
1. In the text editor window, press **Ctrl + V**. This will paste the command into the text editor. The end of the command will look something like this:
    ```Batchfile
    -D -Uflash:w:C:\Users\per\AppData\Local\Temp\arduino_build_91864/sketch_jan22b.ino.hex:i
    ```
    That is the part of the command that tells it to write.
1. Replace the filename in that part of the command with the name of the file you read:
    ```Batchfile
    -Uflash:w:readfile.hex:i
    ```
    So now the full command looks something like this:
    ```Batchfile
    E:\Arduino\hardware\tools\avr\bin\avrdude -CE:\Arduino\hardware\tools\avr/etc/avrdude.conf -v -patmega328p -carduino -PCOM17 -b115200 -D -Uflash:w:readfile.hex:i
    ```
1. If the paths in the command contain spaces, wrap the paths in quotes.
1. Plug your Arduino board into your computer.
1. Copy and paste the command from the text editor to the command line and run it.
1. If you are uploading to an ATmega23U4-based board (e.g., Leonardo, Micro, Yun), you'll need to press and release the reset button on the board immediately after running the command. If you are using a Pro Micro, use a wire to momentarily connect the RST and GND pins. If you are using a Yun, press and release the "32U4 RST" button quickly twice.
1. Wait for the upload to finish successfully.
