Python Code to drive the Maplin/OWI USB Robot arm

  Tested on:
  * Linux
  * OSX (Lion, Mountain Lion)
  May work on Windows,.

Requirements
============

[![Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/eoinwoods/robot_arm?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
* Python 2.7 or 3.0
* Libusb
* pyusb

Usage
=====
As a library:

    >>> import arm

To initialise libusb and the arm

    >>> arm = usb_arm.Arm()

This will tell you if all the dependencies work, and will throw an exception if it fails to find the arm and connect
 to it.

Now lets test it by turning on the LED

    >>> arm.move(usb_arm.LedOn)

It will turn on for 1 second, and automatically turn off. The move() function automatically turns off after each
move. You can optionally specify another time, but since the Maplin arm doesn't have any sensors, beware that if
it reaches limits before the time finishes, then it won't stop.

Actual movement
---------------

    >>> arm.move(usb_arm.ElbowUp)

The elbow will move up.
The movements possible:

GripsOpen
GripsClose
WristUp
WristDown
ElbowUp
ElbowDown
ShoulderUp
ShoulderDown
BaseClockWise
BaseCtrClockWise

Stop

Combining Movements
-------------------
Movements are based upon the BitPattern class, and you can feed arbitrary bitpatterns to it, but all those the
arm is currently capable of are represented above.

However, you may want to make more than one movement at the same time. You can do this by combining patterns with the
or operator:

    >>> arm.move(usb_arm.ElbowDown | usb_arm.BaseClockWise, 0.5)

The arm should turn clockwise and bring the elbow up simultaneously for half a second.

Gear Lash
---------

The unmodified arm has a few flaws - it has fairly loose gear chains in the "servos" it uses for the movements.
To see what I mean try the following:

    >>> arm.move(usb_arm.ShoulderUp, 0.5)
    >>> arm.move(usb_arm.ShoulderDown, 0.5)

You will note the arm moves, but when it returns, it does not quite return to the same position - there is an error,
which you will need to account for as you use the arm and in programmed sequences.

You should now know enough to move the arm to any location.

Sequences of Actions
--------------------
You can create programmed sequences of actions for the robot. However, before you issue one of these, ensure you
know the position of the arm, and wont move it past its limits - which could cause damage to it,

Sequences are created as arrays of commands. Each command is an array of the bitpattern, followed by the
optional time (defaulting to 1 second):

    >>> actions = [[usb_arm.ElbowDown, 0.5], [usb_arm.GripsClose, 0.5], [usb_arm.ElbowUp]]

To issue the action list:

    >>> arm.doActions(actions)

Note you can ctrl-c stop the movements.
There are a couple of canned actions already in the module:

    block_left
    block_right
    left_and_blink

Troubleshooting
===============

Linux - permissions
-------------------

You will either need to run as root (not recommended) or modify your system to allow all users access to the device.

    sudo nano /etc/udev/rules.d/42-usb-arm-permissions.rules

and add:

    SUBSYSTEM=="usb", ATTR{idVendor}=="1267", ATTR{idProduct}=="0000", MODE:="0666"

Plug in the device and you should be able to access it. Tested on Ubuntu and Mint Linux versions.



Related Work
============

* The original reverse engineering of the UBS protocol was done by 
[Vadim Zaliva](http://www.crocodile.org/lord/) and published on [his blog] (http://notbrainsurgery.livejournal.com/38622.html)
* An alternative Objective-C control program is [here](https://armctrl.codeplex.com)
* Maplin's product information on their [web site](http://www.maplin.co.uk/robotic-arm-kit-with-usb-pc-interface-266257)
* OWI (manufacturer) information [here](http://www.owirobots.com/cart/catalog/OWI-535USB-ROBOTIC-ARM-KIT-with-USB-PC-INTERFACE-Assembled-103.html)

