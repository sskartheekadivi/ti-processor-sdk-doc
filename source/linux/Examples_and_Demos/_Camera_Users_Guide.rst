.. http://processors.wiki.ti.com/index.php/Camera_Users_Guide

Camera Users Guide
====================

.. rubric:: Introduction
   :name: introduction

This users guide provides an overview of camera loopback example
integrated in matrix application on AM37x platform. This application can
be executed by selecting the "Camera" icon at the top-level matrix.

|

.. Image:: /images/Main_screen.png

|

.. rubric:: Supported Platforms
   :name: supported-platforms-camera-ug

-  AM37x

|

.. rubric:: Camera Loopback Example
   :name: camera-loopback-example

Camera example application demonstrates streaming of YUV 4:2:2 parallel
data from Leopard Imaging LI-3M02 daughter card with MT9T111 sensor on
AM37x EVM. Here, driver allocated buffers are used to capture data from
the sensor and display to any active display device (LCD/DVI/TV) in VGA
resolution (640x480).

Since the LCD is at inverted VGA resolution video buffer is rotated by
90 degree. Rotation is enabled using the hardware rotation engine called
Virtual Rotated Frame Buffer (VRFB), VRFB provides four virtual frame
buffers: 0, 90, 180 and 270. For statically compiled drivers, VRFB
buffer can be declared in the bootloader. In this example, data is
displayed through video1 pipeline and
"omap\_vout.vid1\_static\_vrfb\_alloc=y" bootargument is included in the
bootargs.

.. rubric:: Sensor Daughter card
   :name: sensor-daughter-card

Smart sensor modules have control algorithms such as AE, AWB and AF are
implemented in the module. Third parties like Leopard Imaging and E-Con
Systems provide smart sensor solutions for OMAP35x/AM37x Rev G EVM and
Beagle Board. SDK 5.02 integrates support for LI-3M02 daughter card
which has MT9T111 sensor, the sensor is configured in 8-bit YUV4:2:2
mode. `Leopard
Imaging <https://www.leopardimaging.com/3M_Camera_Module_Board.html>`__
daughter card LI-3M02 can be ordered from their website.

For additional support on daughter cards or sensor details please
contact the respective third parties

-  `Leopard Imaging <https://www.leopardimaging.com>`__

Contact: support@leopardimaging.com

-  `E-Con Systems <http://www.e-consystems.com>`__

Contact: `Harishankkar <mailto:harishankkar@e-consystems.com>`__

.. rubric:: Connectivity
   :name: connectivity

+----------------------+-----------------------------------------+------------+-----------+
| OMAP35xx/AM/DM37xx   | Signal Description                      | Comment    | IO Type   |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_hs              | Camera Horizontal Sync                  |            | IO        |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_vs              | Camera Vertical Sync                    |            | IO        |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_xclka           | Camera Clock Output a                   | Optional   | O         |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_xclkb           | Camera Clock Output b                   | Optional   | O         |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_[d0:12]         | Camera/Video Digital Image Data         |            | I         |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_fld             | Camera Field ID                         | Optional   | IO        |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_pclk            | Camera Pixel Clock                      |            | I         |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_wen             | Camera Write Enable                     | Optional   | I         |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_strobe          | Flash Strobe Contrl Signal              | Optional   | O         |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_global\_reset   | Global Reset - Strobe Synchronization   | Optional   | IO        |
+----------------------+-----------------------------------------+------------+-----------+
| cam\_shutter         | Mechanical Shutter Control Signa        | Optional   | O         |
+----------------------+-----------------------------------------+------------+-----------+

LI-3M02 daughter card is connected to the 26-pin J31 connector on the
EVM. 24MHz oscillator on the LI daughter card is used to drive the
sensor device, alternatively camera clock output generated by processor
ISP block can be used to drive the sensor pixel clock. Horizontal sync
(HS) and vertical sync (VS) lines are used to detect end of line and
field respectively. Sensor data line CMOS D4:D11 are connected to the
processor D4:D11.

.. Image:: /images/AM37xx_LI-3M02_Single_Camera_Approach.png

**Connectivity of LI-3M02 daughter card to AM/DM37x**

-  Note: H/V signal connectivity not required for ITU-R BT.656 input
   since synchronization signals are extracted from BT.656 bit stream

.. rubric:: Media Controller Framework
   :name: media-controller-framework

SDK 5.02 includes support for media controller framework, for additional
details of media controller framework and its usage please refer the
`PSP capture driver users
guide <http://processors.wiki.ti.com/index.php/UserGuideOmap35xCaptureDriver_PSP_04.02.00.07>`__

In order to enable streaming each links between each entity is
established. Currently only MT9T111/TV5146 => CCDC => Memory has been
validated.

::

    Media: Opened Media Device
    Enumerating media entities
    [1]:OMAP3 ISP CCP2
    [2]:OMAP3 ISP CCP2 input
    [3]:OMAP3 ISP CSI2a
    [4]:OMAP3 ISP CSI2a output
    [5]:OMAP3 ISP CCDC
    [6]:OMAP3 ISP CCDC output
    [7]:OMAP3 ISP preview
    [8]:OMAP3 ISP preview input
    [9]:OMAP3 ISP preview output
    [10]:OMAP3 ISP resizer
    [11]:OMAP3 ISP resizer input
    [12]:OMAP3 ISP resizer output
    [13]:OMAP3 ISP AEWB
    [14]:OMAP3 ISP AF
    [15]:OMAP3 ISP histogram
    [16]:mt9t111 2-003c
    [17]:tvp514x 3-005c
    Total number of entities: 17
    Enumerating links/pads for entities
    pads for entity 1=(0 INPUT) (1 OUTPUT)
    [1:1]===>[5:0]  INACTIVE

    pads for entity 2=(0 OUTPUT)
    [2:0]===>[1:0]  INACTIVE

    pads for entity 3=(0 INPUT) (1 OUTPUT)
    [3:1]===>[4:0]  INACTIVE
    [3:1]===>[5:0]  INACTIVE

    pads for entity 4=(0 INPUT)

    pads for entity 5=(0 INPUT) (1 OUTPUT) (2 OUTPUT)
    [5:1]===>[6:0]  ACTIVE
    [5:2]===>[7:0]  INACTIVE
    [5:1]===>[10:0] INACTIVE
    [5:2]===>[13:0] ACTIVE
    [5:2]===>[14:0] ACTIVE
    [5:2]===>[15:0] ACTIVE

    pads for entity 6=(0 INPUT)

    pads for entity 7=(0 INPUT) (1 OUTPUT)
    [7:1]===>[9:0]  INACTIVE
    [7:1]===>[10:0] INACTIVE

    pads for entity 8=(0 OUTPUT)
    [8:0]===>[7:0]  INACTIVE

    pads for entity 9=(0 INPUT)

    pads for entity 10=(0 INPUT) (1 OUTPUT)
    [10:1]===>[12:0]        INACTIVE

    pads for entity 11=(0 OUTPUT)
    [11:0]===>[10:0]        INACTIVE

    pads for entity 12=(0 INPUT)

    pads for entity 13=(0 INPUT)

    pads for entity 14=(0 INPUT)

    pads for entity 15=(0 INPUT)

    pads for entity 16=(0 OUTPUT)
    [16:0]===>[5:0] ACTIVE

    pads for entity 17=(0 OUTPUT)
    [17:0]===>[5:0] INACTIVE

    Enabling link [MT9T111]===>[ccdc]
    [MT9T111]===>[ccdc]     enabled
    Enabling link [ccdc]===>[video_node]
    [ccdc]===>[video_node]  enabled

    Capture: Opened Channel
    successfully format is set on all pad [WxH] - [640x480]
    Capture: Capable of streaming
    Capture: Number of requested buffers = 3
    Capture: Init done successfully


    Display: Opened Channel
    Display: Capable of streaming
    Display: Number of requested buffers = 3
    Display: Init done successfully

    Display: Stream on...
    Capture: Stream on...

.. rubric:: AM/DM37x ISP Configuration
   :name: amdm37x-isp-configuration

ISP CCDC block should be configured to enable 8-bit YUV4:2:2 parallel
data input, the registers below provide details of ISP and CCDC
registers in this mode.

**ISP Registers:**

::

    ISP_CTRL: 0x480BC040
    29C14C
    ISP_SYSCONFIG: 0x480BC004
    2000
    ISP_SYSSTATUS: 0x480BC008
    1
    ISP_IRQ0ENABLE: 0x480BC00C
    811B33F9
    ISP_IRQ0STATUS: 0x480BC010
    0
    ISP_IRQ1ENABLE: 0x480BC014
    0
    ISP_IRQ1STATUS: 0x480BC018
    80000300

**CCDC Registers:**

::

    CCDC_PID: 0x480BC600
    1FE01
    CCDC_PCR
    0
    CCDC_SYN_MODE: 0x480BC604
    31000
    CCDC_HD_VD_WID: 0x480BC60C
    0
    CCDC_PIX_LINES: 0x480BC610
    0
    CCDC_HORZ_INFO: 0x480BC614
    27F
    CCDC_VERT_START: 0x480BC618
    0
    CCDC_VERT_LINES: 0x480BC61C
    1DF
    CCDC_CULLING: 0x480BC620
    FFFF00FF
    CCDC_HSIZE_OFF: 0x480BC624
    500
    CCDC_SDOFST: 0x480BC628
    0
    CCDC_SDR_ADDR: 0x480BC62C
    1C5000
    CCDC_CLAMP: 0x480BC630
    10
    CCDC_DCSUB: 0x480BC634
    40
    CCDC_COLPTN: 0x480BC63
    0
    CCDC_BLKCMP: 0x480BC63C
    0
    CCDC_FPC: 0x480BC640
    0
    CCDC_FPC_ADDR: 0x480BC644
    0
    CCDC_VDINT: 0x480BC648
    1DE0140
    CCDC_ALAW: 0x480BC64C
    0
    CCDC_REC: 0x480BC650
    0
    CCDC_CFG: 0x480BC65
    8800
    CCDC_FMTCFG: 0x480BC658
    0
    CCDC_FMT_HORZ: 0x480BC65C
    280
    CCDC_FMT_VERT: 0x480BC660
    1E0
    CCDC_PRGEVEN0: 0x480BC684
    0
    CCDC_PRGEVEN1: 0x480BC688
    0
    CCDC_PRGODD0: 0x480BC68C
    0
    CCDC_PRGODD1: 0x480BC690
    0
    CCDC_VP_OUT: 0x480BC694
    3BE2800
    CCDC_LSC_CONFIG: 0x480BC698
    6600
    CCDC_LSC_INITIAL: 0x480BC69C
    0
    CCDC_LSC_TABLE_BA: 0x480BC6A0
    0
    CCDC_LSC_TABLE_OF: 0x480BC6A4
    0
