LEDLIGHT - firmware for LED light controller
============================================

LED Light controller firmware for a board based on STM32 processor.

DEVELOPMENT TOOLCHAIN
---------------------

The project is developed on Linux Debian. The following tools may be used:

* `Sourcery CodeBench`_ (from Mentor Graphics) - `Lite Edition for
  arm-none-eabi`_.
* OpenOCD_: ``apt-get install openocd``
* Eclipse_

Eclipse Pugins
^^^^^^^^^^^^^^

============================== ========= =======================================================
         Plugin                 Version                     Repository Link
============================== ========= =======================================================
 CDT Main Features (kepler)     8.2.0+    http://download.eclipse.org/tools/cdt/releases/kepler
------------------------------ --------- -------------------------------------------------------
 C/C++ GDB Hardware Debugging   7.2.0+    http://download.eclipse.org/tools/cdt/releases/kepler
------------------------------ --------- -------------------------------------------------------
 GNU ARM C/C++ Support          0.5.4+    http://gnuarmeclipse.sourceforge.net/updates
============================== ========= =======================================================


Post Notes
^^^^^^^^^^

* Sourcery CodeBench is a **32-bit aplication**, so amd64 users may need to
  install some libraries in 32-bit version. For that, i386 arch should be
  enabled by::

    dpkg --add-architecture i386

* You may use the following script to crete symbolic links to CodeBench tools
  (I don't like to modify my $PATH)::

    sudo scripts/symlink-codebench [/path/to/codebench]
    
  I usually install CodeBench under
  ``/usr/local/codesourcery/codebench-lite-arm-eabi``, and the above script
  uses this as default.

DEVELOPMENT BOARDS
------------------

Note, that these boards/interfaces usually need additional udev rules if you
wish to use them as non-root user. I used to put them to
``/etc/udev/rules.d/60-openocd.rules`` file.

* `STM32F4 Discovery`_ Board, udev rule::

    ATTR{idVendor}=="0483", ATTR{idProduct}=="3748", GROUP="plugdev", MODE="0660" # STM32 STLink

* `HY-MiniSTM32V`_ plus Stellaris `EKS-LM3S8962`_ Evaluation Board (used as
  JTAG interface), udev rule for EKS-LM3S8962::

    ATTR{idVendor}=="0403", ATTR{idProduct}=="bcd9", GROUP="plugdev", MODE="0660" # Stellaris Evaluation Board

Once you add new udev rule, reload rules with::

    sudo udevadm control --reload-rules

and don't forget to reconnect your board/dongle if it was already connected to
PC.

TARGET BOARD
------------

USEFUL RESOURCES
----------------

* `ARM Microcontroller Firmware Development Framework`_ by Munts Technologies

.. _Sourcery CodeBench: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/overview
.. _Lite Edition for arm-none-eabi: http://www.mentor.com/embedded-software/sourcery-tools/sourcery-codebench/editions/lite-edition/arm-eabi
.. _Eclipse: http://eclipse.org/
.. _OpenOCD: http://openocd.sourceforge.net
.. _ARM Microcontroller Firmware Development Framework: http://tech.munts.com/MCU/Frameworks/ARM
.. _STM32F4 Discovery: http://www.st.com/web/en/catalog/tools/PF252419
.. _HY-MiniSTM32V: http://www.haoyuelectronics.com/Attachment/HY-MiniSTM32V/
.. _EKS-LM3S8962: http://www.ti.com/tool/ek-lm3s8962
.. <!--- vim: set expandtab tabstop=2 shiftwidth=2 syntax=rst: -->
