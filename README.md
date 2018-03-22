# Why

The minimum margin is gigantic, lots of tape is wasted. Also many essential symbols are missing (badly drawn penises for example).

It seems that P-touch printers are all made of the same stuff, only the firmware and a few buttons change. The basic parts are:

* A mixed dot matrix/segment LCD
* A printing assembly consisting of a DC motor and a thermal print head
* A keyboard PCB
* A control PCB

# How

The "R5F100MHA" MCU is a flash-based Renesas RL78/G13, which doesn't do anything spectacular except being able to become one-time-programmable with a single fuse set. Erasing, programming and readback are all disabled. Thanks !

That MCU doesn't seem very popular and its dev tools look scary (as most stuff from Japan). Why not swap the whole control PCB to allow the use of a cheap, more popular MCU and eventually add connectivity or some external memory to be able to print anything ?

# What

* Control board: See pcb_annotated.png
* Keyboard: See keyboard.png (P-Touch E100), it's a simple scanned matrix.
* Display: Custom. BTG-12816D-TAWA-N-G-A1: BeyondTek Graphic 128x16 dots STN gray.
* Motor speed regulator: BA6220.
* Print head: Maybe Kyocera KSH64FA-STD.
* Print head driver: Maybe SII S4622A.

# Print head

Each line's pixels are clocked in a 64-bit shift register, then latched. The strobe signal enables the latch output which turns on the selected heating elements, transfering ink to the label. The heaters are powered with +9V and the driver chip with +5V.

1. +9V
2. +5V ?
3. Clock, rising edge, idle high
4. Data
5. B.E.O. ?
6. Latch, active low
7. NC ?
8. Strobe
9. GND ?
10. GND ?

The E100 uses quite a high clock speed: 1.5MHz. It could go up to 5MHz if the driver is indeed a S4622A. The 64 pixels are loaded in 43us. They're latched shortly after and Strobe goes high for 5ms (heating time).

It appears that the pixels are doubled, making the resolution 32 pixels instead of 64. Maybe for saving memory, or to extend the whole device's useful lifespan in case some heating elements burn out.

# Motor

The BA6620 analog chip "kicks" the motor for a quick start and regulates its speed by using more of less of the 9V supply. This makes the tape move at a constant speed whatever the batteries voltage is (in a reasonable range, say 9~7V).

Since the MCU has no way to know how fast the motor is going, it simply expects it to always be at the same speed and sends lines to the print head at steady intervals.

# Display

TODO
