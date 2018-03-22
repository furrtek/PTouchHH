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

The BA6620 analog chip "kicks" the motor for a quick start and regulates its speed by using more of less of the 9V supply. This makes the tape move at a constant speed whatever the battery voltage is (in a reasonable range, say 9~7V).

Since this printer doesn't do barcodes, they saved on costs and didn't give a way to the MCU to know how fast the motor is going. It simply expects it to always run at the same speed and sends lines to the print head at steady intervals.

# Power

The power button is independent from the keyboard matrix. It shorts a line to ground when pressed and powers up the MCU, which then keeps itself alive via a /powerdown output. It can either go to sleep mode (low power) or kill itself by setting that output low.

@Vikbez tried delaying the motor control signal to avoid wasting 10km of tape each time he needed a label, but surprisingly that made it impossible to turn on the printer. The schematics revealed that the voltage divider used by the MCU to measure battery voltage was only powered when the motor was on (to prevent it from always draining current). When the printer is turned on, the motor is powered during a very shot period, just enough to measure voltage but not enough to make it spin. Since the motor signal was delayed for longer than that period, the voltage divider wasn't being powered during measurment so the MCU thought the batteries were dead: it instantly turned off.

# Display

TODO
