LEDLIGHT - firmware for LED light controller based on STM32 MCU
===============================================================

LED Light controller firmware for a board based on STM32 processor.

DEVELOPMENT TOOLCHAIN
=====================

The project is developed on Linux Debian. The following tools are used:

* `Sourcery CodeBench`_ (from Mentor Graphics) - `Lite Edition for
  arm-none-eabi`_.

  I usually install it under ``/usr/local/codesourcery/codebench-lite-arm-eabi`` directory.
* OpenOCD_::

    apt-get install openocd

* Eclipse_

Eclipse plugins:

* `CDT Main Features (kepler)`_ 8.2.0 or newer,
* `C/C++ GDB Hardware Debugging (kepler)`_ 7.2.0 or newer,
* `GNU ARM Eclipse plugin`_.

Post notes:

* crete symbolic links to codebench compilers (I don't like to modify my $PATH)::

    sudo scripts/symlink-codebench [/path/to/codebench] 

Sourcery CodeBench is a 32-bit aplication, so *amd64* users may need to install some libraries in 32-bit version. For that, i386 arch should be enabled by::

    dpkg --add-architecture i386

DEVELOPMENT BOARD
=================

* STM32F4 Discovery Board, needs udev rule to be programmed without root privileges (put these lines to ``/etc/udev/rules.d/60-openocd.rules``, for example)::

    ATTR{idVendor}=="0483", ATTR{idProduct}=="3748", GROUP="plugdev", MODE="0660" # STM32 STLink

* HY-MiniSTM32V plus Stellaris Evaluation Board (used as JTAG interface), needs ::

    ATTR{idVendor}=="0403", ATTR{idProduct}=="bcd9", GROUP="plugdev", MODE="0660" # Stellaris Evaluation Board

Once you add new udev rule, reload rules with::

    sudo udevadm control --reload-rules

and don't forget to reconnect your board/dongle if it was already connected to PC. 

TARGET BOARD
============

USEFUL RESOURCES
================

* `ARM Microcontroller Firmware Development Framework`_ by Munts Technologies

.. _Sourcery CodeBench: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/overview
.. _Lite Edition for arm-none-eabi: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/arm-eabi
.. _Eclipse: http://eclipse.org/
.. _OpenOCD: http://openocd.sourceforge.net
.. _CDT Main Features (kepler): http://download.eclipse.org/tools/cdt/releases/kepler
.. _C/C++ GDB Hardware Debugging (kepler): http://download.eclipse.org/tools/cdt/releases/kepler
.. _GNU ARM Eclipse plugin: http://gnuarmeclipse.sourceforge.net/updates).
.. _ARM Microcontroller Firmware Development Framework: http://tech.munts.com/MCU/Frameworks/ARM) 
.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
