# ESP8266 hands-on
Hands on ESP8266

To list serial port devices:

    # dmesg | grep tty

Or if `platformio` is installed:

    $ pio device list

## Conda environment
Create conda environment:

    conda env create -f esp8266_env.yml
