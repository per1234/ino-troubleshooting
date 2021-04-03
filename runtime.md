---
title: Runtime
description: Troubleshooting problems that occur while your program is running on the Arduino board
---

---

### General troubleshooting advice

---

#### Turn on warnings and read them to help with troubleshooting

Compiler warnings can point out problems in your code that would otherwise be difficult to spot, but warnings are disabled by default in the Arduino IDE so you need to turn them on:

1. **File > Preferences**
1. Set "Compiler warnings" to "All".
1. Uncheck the box next to "Show verbose output during: compilation".
1. Compile your sketch.
1. After the compilation finishes, scroll the black console window up and read any text that is shown in red.

Compiler warnings follow a standard form: `{filename}:{line number}:{column number}: warning: {warning message}`. On the next line, the line of code where the warning occurred is shown. On the next line, a caret (^) points to the column where the warning occurred in the code.

You should always pay attention to warnings, and fix them in your code. However, warnings don't always indicate a serious problem. When the warning is caused by code in a library, often the best course is to ignore it and carry on.

---

#### Add serial output for debugging

Add [`Serial.println()`](https://www.arduino.cc/reference/en/language/functions/communication/serial/println) calls at strategic locations in your sketch, then run the program with Serial Monitor open to see what is happening. You can print the values of variables, or just messages to indicate which part of the program is running.

Serial debug output will increase the memory usage and slow the execution speed of your program, so you may want to be able to disable the debug output when it's not needed. Here's a useful system for doing that:

```c++
#define DEBUG true  // set to true for debug output, false for no debug output
#define DEBUG_SERIAL if(DEBUG)Serial
```

Then use debug output like this:

```c++
DEBUG_SERIAL.println("Some debug output");
```

When DEBUG is set to false, the compiler will optimize the calls using `DEBUG_SERIAL` out of the code because it knows they will never run.

If you want your program to wait for Serial Monitor to be opened before running when using native USB boards (e.g., Leonardo, Micro, MKR, Nano 33 IoT, Nano 33 BLE), add this line:

```c++
#if DEBUG == true
  while(!Serial);
#endif  //DEBUG == true
```

In the rare case where your debug system needs to read serial input, you would use similar code:

```c++
#if DEBUG == true
  if(Serial.available()) {
    x = Serial.read();
  }
#endif  //DEBUG == true
```

This can easily be extended to allow multiple levels of debug output, still with no overhead when it's disabled:

```c++
#define DEBUG_ERROR true
#define DEBUG_ERROR_SERIAL if(DEBUG_ERROR)Serial

#define DEBUG_WARNING true
#define DEBUG_WARNING_SERIAL if(DEBUG_WARNING)Serial

#define DEBUG_INFORMATION true
#define DEBUG_INFORMATION_SERIAL if(DEBUG_INFORMATION)Serial

void setup() {
  Serial.begin(9600);
  while (!Serial);
  DEBUG_ERROR_SERIAL.println("This is an error message");
  DEBUG_WARNING_SERIAL.println("This is a warning message");
  DEBUG_INFORMATION_SERIAL.print("The state of pin 5 is ");
  DEBUG_INFORMATION_SERIAL.println(digitalRead(5) ? "HIGH" : "LOW");
  Serial.println("This is standard program output");
}

void loop() {}
```

You can use the Boolean debug macros to switch on and off other parts of your code too.

---

<a id="high-low"></a>

#### `HIGH` doesn't always mean "on". `LOW` doesn't always mean "off".

`HIGH` and `LOW` are just arbitrary pin states. There is no reason why a `HIGH` must be associated with a button being pressed, an LED being on, etc. A `LOW` could just as easily be associated with those events, and often is. It all depends on the circuit attached to the pin.

---

### Specific issues

---

<a id="while-not-serial"></a>

#### My program only runs when I have Serial Monitor open

Boards without native USB capabilities (e.g., Uno, classic Nano, Mega) are automatically reset when you open Serial Monitor, so you will always see the serial output from the very start of the program. But the native USB boards (e.g., Leonardo, Micro, MKR, Nano 33 IoT, Nano 33 BLE) don't reset when Serial Monitor is opened. This means that any serial output that runs before you open Serial Monitor is never seen.

This is why it's common to see a line like this in Arduino sketches:

```c++
while(!Serial);
```

The purpose of that code is to cause the program to wait in a loop at that line until Serial Monitor is opened. This way, no serial output is missed. However, if you want your program to run without Serial Monitor open, you must remove that line.

Boards without native USB are not affected by this line (`Serial` is always `true`).

---

#### I'm not seeing any output in Serial Monitor

If you are using a board with native USB capabilities (e.g., Leonardo, Micro, MKR, Nano 33 IoT, Nano 33 BLE), add this line to your sketch before you print any output to `Serial`:

```c++
while(!Serial);
```

For more information, see ["My program only runs when I have Serial Monitor open"](#while-not-serial).

If you're using a boards without native USB capabilities (e.g., Uno, classic Nano, Mega), make sure the baud rate menu near the bottom right corner of Serial Monitor is set to the baud rate you set in your [`Serial.begin()`](https://www.arduino.cc/reference/en/language/functions/communication/serial/begin) call.

---

#### I'm getting gibberish/backwards question marks in Serial Monitor

If you're using a boards without native USB capabilities (e.g., Uno, classic Nano, Mega), make sure the baud rate menu near the bottom right corner of Serial Monitor is set to the baud rate you set in your [`Serial.begin()`](https://www.arduino.cc/reference/en/language/functions/communication/serial/begin) call.

---

#### My sketch runs when board is connected with the USB cable, but not with other power source

The barrel jack or VIN pin on Arduino boards is connected to a voltage regulator. The maximum current capability of the voltage regulator is much less than that of a computer's USB port. The voltage regulator is able to power the Arduino board, but if other components connected to the board are drawing too much current it will cause the voltage regulator to shut down.

---

#### Button presses are being read when I'm not pressing the button

If a pin is in `INPUT` mode and not connected to anything, it is "floating". This means that it is in a state where it can be influenced by electrical noise and could be `HIGH`, `LOW`, or switching back and forth between the two levels. The solution to this is to use a pull-up or pull-down resistor to hold the pin in a known state when the circuit is open. You can use an external resistor for this purpose, but more convenient is to use the internal pull-up resistors of your Arduino board's microcontroller. These are activated by putting the pin in `INPUT_PULLUP` mode:

```c++
pinMode(pin, INPUT_PULLUP);
```

A pull-up resistor means that the pin will read `HIGH` when the button is not pressed. This means you need to wire the button between the pin and ground. When the button is pressed, the pin will read `LOW`. This may seem counterintuitive at first, but remember: [HIGH doesn't always mean "on". LOW doesn't always mean "off"](#high-low).

---

#### A variable is not holding the value it was set to

You can declare multiple variables of the same name at different [scopes](https://www.arduino.cc/reference/en/language/variables/variable-scope--qualifiers/scope/). For example:

```c++
byte foo = 6; // a global variable named foo

void setup() {
  Serial.begin(9600);
  byte foo = 42  // a variable local to setup() named foo
  Serial.println(foo);  // this line refers to the local variable named foo, which has the value 42
}

void loop() {
  Serial.println(foo);  // the local variable named foo is out of scope, so this foo refers to the  global variable named foo, which has the value 6
}
```

This is called "variable shadowing", and can be very confusing. It's important to understand the difference between a variable declaration:

```c++
byte foo = 6;
```

and a variable definition:

```c++
foo = 42;
```

A local variable holds its value only while it is in [scope](https://www.arduino.cc/reference/en/language/variables/variable-scope--qualifiers/scope/). If you want a variable to retain the same value it was set to each time the function runs, you need to make it a global or declare it [static](https://www.arduino.cc/reference/en/language/variables/variable-scope--qualifiers/static/).

---

#### My sketch isn't working as expected with input from Serial Monitor

Check the setting of the Serial Monitor's line ending menu. It's common for Arduino code to be written in a way that doesn't correctly handle line endings (in which case "No line ending" is the correct setting), or requires a specific line ending.

Data sent via Serial Monitor is ASCII encoded. If you send a number via Serial Monitor, the character representation of that number is sent, not the decimal representation. For example, say you send the number 4. This code:

```c++
if(Serial.read() == 4) {
```

will evaluate as `false`, because the ASCII value of the character 4 is 52, not 4. But this code:

```c++
if(Serial.read() == '4') {
```

will evaluate as `true` because `'4'` is the character 4, which is what you sent.

---

#### Communication using the SoftwareSerial library is not working

Check the library's documentation (<https://www.arduino.cc/en/Reference/SoftwareSerial>) to see if you are using an RX pin that is supported for your board.

Don't use high baud rate, as it won't work reliably. It's recommended to start with 9600 baud to be sure of reliable communication.

---

<a id="mbedos-crash"></a>

#### My program stopped working on my Nano 33 BLE board and the onboard LED is blinking

A 3X long, 3X short blink pattern indicates Mbed OS has crashed. The board's port will no longer appear under the **Port** menu (see ["Native USB board doesn't get a port, or is failing uploads"](uploading.md#native-usb-reset)).

Debug information is printed on `Serial1` when Mbed OS crashes. You can view the debug output by following these instructions:

1. Unplug your Nano 33 BLE board from the computer or power supply.
1. Make the following connections between a USB to TTL serial adapter (AKA "FTDI") and your Nano 33 BLE:

   | Adapter | Nano 33 BLE |
   | ------- | ----------- |
   | RX      | TX1         |
   | GND     | GND         |

   You will also need to power the Nano 33 BLE. You can do this via the USB socket, or power it from the USB to TTL serial adapter's VCC pin. If the serial adapter's VCC pin is outputting 5 V, connect it to the Nano 33 BLE's VIN pin. If the serial adapter's VCC pin is outputting 3.3 V, connect it to the Nano 33 BLE's 3.3 V pin.

1. Connect the serial adapter to your computer with a USB cable.
1. Select the port of the serial adapter from the Arduino IDE's **Tools > Port** menu.
1. **Tools > Serial Monitor**
1. Set the baud rate menu at the bottom right corner of Serial Monitor to 115200.
1. Press and release the reset button on the Nano 33 BLE. You should now see the "MbedOS Error Info" output in the Serial Monitor output field.

---

#### I'm using Windows and keyboard or mouse emulation is not working with my SAMD-based Arduino board

<!--
- https://github.com/arduino/ArduinoCore-samd/issues/423
- https://github.com/arduino/ArduinoCore-samd/releases/tag/1.8.2
 -->

1. Upload a sketch that uses the Keyboard or Mouse library to your Arduino board.
1. Open Windows Device Manager.
1. Under the "Ports" tree, you should see your Arduino board listed with a yellow triangle. Right click on it.
1. Check the box next to "Delete the driver software for this device".
1. Click "Uninstall Device".
1. Unplug the Arduino board's USB cable.
1. Plug the Arduino board's USB cable back in.

Keyboard and mouse emulation should now start working.

---

#### MKR WiFi 1010/Nano 33 IoT/Uno WiFi Rev2/MKR Vidor 4000/MKR1000's WiFi does not work

Try updating the WiFi module's firmware:

1. Update to the newest version of the Arduino IDE: <https://www.arduino.cc/en/Main/Software>
1. **Sketch > Include Library > Manage Libraries...**
1. Wait for the download to finish.
1. In the "Filter your search..." field, type "wifinina" (if you are using MKR WiFi 1010, Nano 33 IoT, Uno WiFi Rev2, or MKR Vidor 4000) or "wifi101" (if you are using MKR1000).
1. Press Enter.
1. Click on WiFiNINA or WiFi101.
1. If you see an "Update" button, click it and wait for the update to finish.
1. Click the "Close" button.
1. **File > Examples > WiFiNINA > Tools > CheckFirmwareVersion** (or if you have the MKR1000, **File > Examples > WiFi101 > CheckWifi101FirmwareVersion**)
1. Upload the sketch to your board.
1. **Tools > Serial Monitor**
1. If the output in Serial Monitor indicates you already have the latest version of the firmware installed, no need to update it. Otherwise, continue with the following instructions.
1. **Tools > WiFi101 / WiFiNINA Firmware Updater**
1. Select the port of your Arduino board from the "Select port of the WiFi module" menu.
1. Click the "Open Updater sketch" button
1. Upload the sketch to your board.
1. Open the "WiFi101 / WiFiNINA Firmware Updater" window.
1. Click the "Update Firmware" button.
1. Wait for the update to finish.
1. Close the "WiFi101 / WiFiNINA Firmware Updater" window.

---

#### GSM communication not working on MKR GSM 1400

<!-- from https://store.arduino.cc/mkr-gsm-1400 -->

During cellular transmissions, the peak current required by the board will exceed 500 mA. This is in excess of what can be sourced by a standard USB port, so it is mandatory to have a 1500 mAh or higher LiPo battery connected to your board all the time. The current provided by the USB port will be supplemented by the battery. When powering the board via the VIN pin, a 5 V power supply that can supply at least 2 A is required.
