# Introduction

This contains the example code to be used with the Silicon Frog fpga-384.


----
# Setting up the toolchain

To compile the examples we require the [IceStorm](http://www.clifford.at/icestorm/) toolchain for lattice FPGAs.
First however we need to install its dependencies.

```
sudo apt-get update
sudo apt-get upgrade

sudo apt-get install build-essential clang bison flex libreadline-dev \
                     gawk tcl-dev libffi-dev git mercurial graphviz   \
                     xdot pkg-config python python3 libftdi-dev
```

With that done we can now build the tools that make up the toolchain.

```
git clone https://github.com/cliffordwolf/icestorm.git
cd icestorm
make
sudo make install
cd ..

git clone https://github.com/cseed/arachne-pnr.git
cd arachne-pnr
make
sudo make install
cd ..

git clone https://github.com/cliffordwolf/yosys.git
cd yosys
make
sudo make install
```


----
# Using the toolchain 

To build an example program for the FPGA-384 the following commands can be used:

```
# compile verilog code
yosys -q -p "synth_ice40 -blif example.blif" example.v

# invoke place and route
arachne-pnr -q -d 384 -P qn32 -c /usr/local/share/arachne-pnr/chipdb-384.bin -p example.pcf example.blif -o example.asc

# translate into binary form
icepack example.asc example.bin

# upload the binary to FPGA
fpga-384 -p example.bin
```


----
# Building the examples in this repo

To compile on the Raspberry Pi:
    $ cd Blink
    $ make
    $ ./fpga-384 fpga.bin

To compile for Arduino:
    $ cd Blink
    $ make

Then open the resulting project in the Arduino IDE or if you took the option to
install Arduino-mk you can run "make upload".


----
# Projects

### Blink

The simplest example with flashing LEDs.

### PWM

Fading the LED brightness using PWM.

### Gates

The host will clock the FPGA running through a set of logic gates (AND, OR, XOR, NOT)
and the LEDs will show the result of them.

The gate type is chosen dependent upon a bit pattern selection, and the output is the
logical gate application of the input.

Input logic A & B are red and green, result C is blue.

### Adder

The host will clock the FPGA running through a simple one bit full adder taking in a
single bit and a carry bit and will produce the result bit and a new carry bit.

Resulting bit and carry bit are shown on the leds.

### Parity

The host will clock the FPGA running through a sequence where four bits are used as an
input to find the even parity.

The resulting parity bit is shown on an led.

### Rand

The FPGA will provide a running Pseudo Random Number Generator of the Linear Feedback
Shift Register type.

A single bit is shown on an led which should flash with an unpredictable pattern.

### Pattern

Shows the application of an internal state machine. A binary pattern is fed into an
input which changes the internal state. The green LED lights when the full pattern is
correctly received.

### BCD

This shows Binary Coded Decimal logic.

### Decoder

Shows how to generate specific binary output, given a binary input. Can be used to
create a 3-8 decoder, for example.

### I2C

Shows how to create a device with a specified I2C address.

### Serial

Implements a low speed serial encoder / decoder. The serial port should work well
at 9600 baud.

Notes for Raspberry Pi: You may need to configure the relevant IO pins to be the serial lines.
    $ sudo raspi-config
    You will find the menu under the 'interfacing options' / 'serial'.
    Also, the test application neds to be run with sudo.


----
# Documents

- [Schematic Diagram](docs/FPGA384_Schematic.pdf)


----
# Licensing

See the [LICENSE.txt] file for more details.
