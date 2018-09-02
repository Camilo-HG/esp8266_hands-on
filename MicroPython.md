# [MicroPython](http://micropython.org/)

From documentation:

> **MicroPython** is a lean and efficient implementation of the **Python 3** programming language that includes a small subset of the Python standard library and is optimised to run on microcontrollers and in constrained environments.

> **MicroPython** is packed full of advanced features such as an interactive prompt, arbitrary precision integers, closures, list comprehension, generators, exception handling and more. Yet **it is compact enough to fit and run within just 256k of code space and 16k of RAM**.
**MicroPython** aims to be as compatible with normal Python as possible to **allow you to transfer code** with ease **from the desktop to a microcontroller** or embedded system.

> **MicroPython is a full Python compiler and runtime** that runs on the bare-metal. You get an **interactive prompt (the REPL)** to execute commands immediately, along with the ability **to run and import scripts from the built-in filesystem**. The REPL has history, tab completion, auto-indent and paste mode for a great user experience.

## MicroPython for ESP8266

Take a look on [Quick reference for the ESP8266](http://docs.micropython.org/en/latest/esp8266/esp8266/quickref.html).

We are gonna follow [MicroPython tutorial for ESP8266](http://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/index.html)

Download the [firmware for the ESP8266](http://micropython.org/download#esp8266).

#### First: erase your device with [esptool.py](https://github.com/espressif/esptool/)

As `sudo`, run:

    # pip install esptool

Then

    # esptool.py --port /dev/ttyUSB0 erase_flash

And finally

    # esptool.py --port /dev/ttyUSB0 --baud 460800 write_flash --flash_size=detect 0 esp8266-version.bin

For some boards with a particular FlashROM configuration (e.g. some variants of a NodeMCU board) you may need to use the following command to deploy the firmware (note the `-fm dio` option):

    # esptool.py --port /dev/ttyUSB0 --baud 460800 write_flash --flash_size=detect -fm dio 0 esp8266-version.bin

**I had to use last one.**

## [Getting a MicroPython REPL prompt](http://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/repl.html)

The REPL is always available on the UART0 serial peripheral, which is connected to the pins GPIO1 for TX and GPIO3 for RX. The baudrate of the REPL is 115200. If your board has a USB-serial convertor on it then you should be able to access the REPL directly from your PC. Otherwise you will need to have a way of communicating with the UART.

To access the prompt over USB-serial you need to use a terminal emulator program. First, install **picocom:**

    # apt install picocom

Then, run:

    # picocom /dev/ttyUSB0 -b115200

Expected output looks like:

    picocom v3.1

    port is        : /dev/ttyUSB0
    flowcontrol    : none
    baudrate is    : 115200
    parity is      : none
    databits are   : 8
    stopbits are   : 1
    escape is      : C-a
    local echo is  : no
    noinit is      : no
    noreset is     : no
    hangup is      : no
    nolock is      : no
    send_cmd is    : sz -vv
    receive_cmd is : rz -vv -E
    imap is        :
    omap is        :
    emap is        : crcrlf,delbs,
    logfile is     : none
    initstring     : none
    exit_after is  : not set
    exit is        : no

    Type [C-a] [C-h] to see available commands
    Terminal ready

Then press `Enter` a few times. You should see the **Python REPL prompt**, indicated by `>>>`. **Then you can work!!**

But be careful. Let's say you want to *turn on* the LED attached in your **ESP8266**. In the root web page [MicroPython](http://micropython.org/), you will see next example you can run in **REPL prompt**:

    import pyb

    # turn on an LED
    pyb.LED(1).on()

    # print some text to the serial console
    print('Hello MicroPython!')

But when you try:

    >>> import pyb

Output is:

    >>> import pyb
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ImportError: no module named 'pyb'

This happens because the `pyb module` exists for **PyBoard** in the **MicroPython** family of ports. So, the **MicroPython compiled for ESP8266** does not have a `pyb module` and you must use the `machine module` instead:

    >>> import machine
    >>> pin = machine.Pin(2, machine.Pin.OUT)
    >>> pin.on()
    >>> pin.off()

Note that `on` method of a Pin **might** turn the LED **off** and `off` might turn it **on** (or vice versa), depending on how the LED is wired on your board. To resolve this, `machine.Signal class` is provided.

## WiFi

After a fresh install and boot the device configures itself as a **WiFi access point (AP)** that you can connect to. The **ESSID** is of the form **MicroPython-xxxxxx** where the **x’s** are replaced with part of the **MAC (Media Access Control) address** of your device (so will be the same everytime, and most likely different for all **ESP8266** chips). The `password` for the **WiFi** is `micropythoN` (note the upper-case N). Its IP address will be `192.168.4.1` once you connect to its network.
