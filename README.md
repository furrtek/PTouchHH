# Why

The minimum margin is gigantic, lots of tape is wasted. Also many essential symbols are missing (badly drawn penises for example).

It seems that P-touch printers are all made of the same stuff, only the firmware and a few buttons change. The basic parts are:

* A mixed dot matrix/segment LCD
* A printing assembly consisting of a DC motor and a thermal print head
* A keyboard PCB
* A control PCB

# How

The "R5F100MHA" MCU is a flash-based Renesas RL78/G13, which doesn't do anything spectacular except being able to turn one-time-programmable with a single fuse set. Erasing, programming and readback are all disabled. Thanks !

That MCU doesn't seem very popular and its dev environment looks scary (as most stuff from Japan). Why not swap the whole control PCB to allow the use of a cheaper, more popular MCU and eventually add connectivity or some external memory to be able to print anything ?

# What

* Keyboard: See keyboard.png (P-Touch E100), it's a simple scanned matrix.
* Display: Custom. BTG-12816D-TAWA-N-G-A1: BeyondTek Graphic 128x16 dots STN gray.
* Motor speed regulator: BA6220.
* Print head: Maybe Kyocera KSH64FA-STD.
