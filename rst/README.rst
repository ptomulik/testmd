LEDLIGHT - firmware for LED light controller
============================================

LED Light controller firmware for a board based on STM32 processor.

Tools
-----

The project is developed on Linux Debian. The following tools are used:

============================ ============ =============================================
         Software              Version              Notes
============================ ============ =============================================
    `Sourcery CodeBench`_       gcc 4.7+     `Lite Edition/ARM EABI`_ [#n1.1]_ [#n1.2]_
    OpenOCD_                    0.7.0+       ``apt-get install openocd``
    Eclipse_                    4.3.0+
    SCons_                      2.3+         ``apt-get install scons``
============================ ============ =============================================

.. [#n1.1] Sourcery CodeBench is a **32-bit aplication**, so amd64 users may
  need to install some libraries in 32-bit version. For that, i386 arch should
  be enabled by::

               dpkg --add-architecture i386

.. [#n1.2] You may use the following script to crete symbolic links to
  CodeBench tools (I don't like to modify my $PATH):: 

      sudo scripts/symlink-codebench [/path/to/codebench]

  I install it under ``/usr/local/codesourcery/codebench-lite-arm-eabi``, and
  the above script uses this as default.

Eclipse Pugins
^^^^^^^^^^^^^^

============================== ========= ========================================================
         Plugin                 Version                     Repository Link
============================== ========= ========================================================
 CDT Main Features (kepler)     8.2.0+    http://download.eclipse.org/tools/cdt/releases/kepler
 C/C++ GDB Hardware Debugging   7.2.0+    http://download.eclipse.org/tools/cdt/releases/kepler
 GNU ARM C/C++ Support          0.5.4+    http://gnuarmeclipse.sourceforge.net/updates
 SConsolidator Plugins          0.6.0+    http://www.sconsolidator.com/update
============================== ========= ========================================================

Development Boards
------------------

======================== =======================================
         Board                            Notes
======================== =======================================
  `STM32F4-Discovery`_    JTAG accessible via USB cable
  `HY-MiniSTM32V`_        Needs external JTAG dongle.
======================== =======================================

To use JTAG interfaces as unprivileged (non-root) user, you may need to add
special udev rules. You may put them to ``/etc/udev/rules.d/60-openocd.rules``
file (create if absent). Here are example rules for some on-board interfaces:

* `STM32F4-Discovery`_::

    ATTR{idVendor}=="0483", ATTR{idProduct}=="3748", GROUP="plugdev", MODE="0660" # STM32 STLink

* `EKS-LM3S8962`_::

    ATTR{idVendor}=="0403", ATTR{idProduct}=="bcd9", GROUP="plugdev", MODE="0660" # Stellaris Evaluation Board

If new rules are added, reload rules with::

    sudo udevadm control --reload-rules

and don't forget to reconnect your board/dongle.

Compiling
---------

Compiling entire project
^^^^^^^^^^^^^^^^^^^^^^^^

Run scons script to compile entire project::

    scons

All generated files go to ``build/`` directory.

It may take a while, because several variants of firmware are generated.
If you have multiple CPUs (cores) run parallel build::

    scons -j6 # 6-jobs in parallel

Compiling only what is necessary for one particular board
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To compile ``ledlight`` application for single target board, invoke one of the
following commands:

============================= ======================================
        Target Board                    Command
============================= ======================================
 `HY-MiniSTM32V`_              ``scons hy-ministm32v``
 STM32F1-LEDLight              ``scons stm32f1-ledlight``
 `STM32F4-Discovery`_          ``scons stm32f4-discovery`` [#n4.1]_
============================= ======================================

.. [#n4.1] This target is currently not supported

Cleaning up build directory
^^^^^^^^^^^^^^^^^^^^^^^^^^^

To clean-up build dir, type::

    scons -c

Flashing and debugging
----------------------

Flashing and debugging from CLI 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Use GNU debugger and OpenOCD to flash application into your MCU. First step
is to enter debugger CLI::

    $ arm-none-eabi-gdb

then form GDB session attach to your target board using OpenOCD::

    (gdb) target remote | openocd -c "gdb_port pipe; log_output openocd.log" -f <file.cfg>

Substitute ``<file.cfg>`` with your OpenOCD configuration file.
We have some prefabricated configuration files for our test beds (see section
`Configuration files for OpenOCD`_).

Write your program to flash from debugger CLI::

    (gdb) load build/debug/ledlight-hy-ministm32v/ledlight.elf

Note: select appropriate binary file in the above line, it's just an example
for HY-MiniSTM32V board.

Finally reset your MCU::

    (gdb) monitor reset halt

if you want to debug, or::

    (gdb) monitor reset init

if you want to run your application.

Configuration files for OpenOCD
```````````````````````````````

Here are some ready to use OpenOCD configuration files:

==================== =====================================================
        Board                     OpenOCD config file
==================== =====================================================
 HY-MiniSTM32V        ``openocd/hy-ministm32-via-luminary.cfg`` [#n5.1]_
 STM32F4-Discovery    ``openocd/stm32f4-discovery.cfga``
==================== =====================================================

.. [#n5.1] Programmed via Luminary EKS-LM3S8962 (or other compatible board)

Useful resources
----------------

* `ARM Microcontroller Firmware Development Framework`_ by Munts Technologies

.. _Sourcery CodeBench: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/overview
.. _Lite Edition/ARM EABI: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/arm-eabi
.. _Eclipse: http://eclipse.org/
.. _OpenOCD: http://openocd.sourceforge.net
.. _ARM Microcontroller Firmware Development Framework: http://tech.munts.com/MCU/Frameworks/ARM
.. _STM32F4-Discovery: http://www.st.com/web/en/catalog/tools/PF252419
.. _HY-MiniSTM32V: http://www.haoyuelectronics.com/Attachment/HY-MiniSTM32V/
.. _EKS-LM3S8962: http://www.ti.com/tool/ek-lm3s8962
.. _SCons: http://www.scons.org
.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
