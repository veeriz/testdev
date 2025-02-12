# Summary:

1. How to connect the wires: ................................................................................................................ 2 

2. Reference ........................................................................................................................................... 2 

3. Message header description ............................................................................................................. 3

4. General Data Format ......................................................................................................................... 3

5. Default Settings ................................................................................................................................. 4 

6. Key Contact state (01h) ..................................................................................................................... 5

7. LED COMMAND: ................................................................................................................................ 6

a. Set single LED state (01h) .......................................................................................................................... 6 

b. Set multiple LED state................................................................................................................................ 7

Configuration commands .............................................................................................................................. 9

8. Set LED indicators brightness level (02h) .......................................................................................... 9 

9. Set backlight level (03h) .................................................................................................................... 9 

10. Set startup keys message(28h) ........................................................................................................ 10

11. Get software revision (2Ah) ............................................................................................................. 10

12. Set startup LED show (34h) ............................................................................................................. 10

13. Set LED management mode (37h) ................................................................................................... 11

14. Set Destination Address (6Eh) ......................................................................................................... 11

15. Baud rate setting (6Fh) .................................................................................................................... 12

16. Set Source Address (70h) ................................................................................................................. 12

17. Periodic key-state transmission (71h) ............................................................................................. 13

18. Event state transmission (72h) ........................................................................................................ 13

19. LED Acknowledgment (73h) ............................................................................................................ 14

20. Address Claim at boot (74h) ............................................................................................................ 15

21. Heartbeat (75h) ............................................................................................................................... 16

22. Key-state message period (77h) ...................................................................................................... 17

23. Start Demo mode(7Ah).................................................................................................................... 17

24. Default backlight brightness level (7Bh) .......................................................................................... 17

25. Default LED indicators brightness level (7Ch) ................................................................................. 18

26. Default backlight color (7Dh) ........................................................................................................... 18

27. Set CAN protocol ............................................................................................................................. 18

APPENDIX: DEMO Mode instructions .......................................................................................................... 19

28. Revision history ............................................................................................................................... 20

## 1. How to connect the wires: 


| PIN  | COLOUR  | FUNCTION  |
| -- | -- | -- |
| 1  | Blue  | CAN L  |
| 2  | White  | CAN H  |
| 3  | Black  | Negative battery  |
| 4  | Red  | Vbatt. (12-24V)  |



![](https://web-api.textin.com/ocr_image/external/3c0756389da73703.jpg)


![](https://web-api.textin.com/ocr_image/external/966b8553bb84d580.jpg)

Each end of the CAN bus is terminated with 120Ω resistors in compliance with the standard to minimize signal reflections on the bus. You may need to place a 120Ω resistor between CAN-L and CAN-H. 


![](https://web-api.textin.com/ocr_image/external/1ba83b576d737fd7.jpg)

Warning: to avoid breakage do not tighten the backshell nuts with a torque exceeding 0.8 Nm!

## 2. Reference 

PKP2600SI 


![](https://web-api.textin.com/ocr_image/external/2d6eb18d0e99d968.jpg)

## 3. Message header description

The 29-bit CAN identifier used in J1939 is structured in the following way. 


| Priority  | Reserved  | Data Page  | PDU Format  | PDU Specific  | Source Address  |
| -- | -- | -- | -- | -- | -- |
| 3 bits  | 1 bit  | 1 bit  | 8 bits  | 8 bits  | 8 bits  |


The proprietary format used by PKP keypads is defined as follows: 

Priority=6.

Reserved=0.

Datapage=0.

PDU Format = EFh (the message is addressable). 

PDU Specific = Destination Address.

Parameter Group Number: 

61184 (EF00h) [PROPRIETARY A] used for configuration messages and LED command a (see page 6).

42752 (A700h) [AUXILIARY I/O #2] 

 Specifically used for LED command b (see page 7-8). 

42496 (A600h) [AUXILIARY I/O #3]

(See ISO 11783-3 for further details).

An example of CAN identifier of messages sent to the keypad is 18EF2100h where:

21h is the destination address (keypad) 

00h is the source address. 

An example of CAN identifier of messages sent by the keypad is 18EFFF21h where:

FFh refers to broadcast messages (no specific destination address) 

21h is the source address (keypad).

## 4. General Data Format 

The proprietary protocol has defined a general format for the data fields in the PGN 61184. The format consists of: 

1 header field (2 bytes) 

1 command byte 

5 bytes (the remaining field) are defined specifically for each command. 

The data length is 8 bytes, unused bits and bytes are set to all 1’s (0xFF). 


| Byte 0  | 04h  |
| -- | -- |
| Byte 1  | 1Bh  |
| Byte 2  | Command  |
| Byte 3-7  | Data required for each specific command  |


5. Default Settings 


| Setting  | Default state or level  | How to change  |
| -- | -- | -- |
| CAN bus baud rate  | 250 kbit/s  | Command 6Fh  |
| Source address  | 21h  | Command 70h  |
| Keypad Identifier  | 21h  | Command 70h  |
| Destination address  | FFh  | Command 6Eh  |
| Heartbeat  | Disabled  | Command 75h  |
| Periodic key-state message transmission  | Disabled  | Command 71h  |
| Key-state message period  | 100ms  | Command 77h  |
| Event state transmission  | Enabled  | Command 72h  |
| Address claim message at boot  | Disabled  | Command 74h  |
| Default LED indicators brightness level  | 3Fh  | Command 7Ch  |
| Default backlight brightness level  | OFF  | Command 7Bh  |
| Startup LED show  | Complete LED Sequence  | Command 34h  |
| Default backlight color  | Amber  | Command 7Dh  |
| LED acknowledgment  | Disabled  | Command 73h  |


## 6. Key Contact state (01h)

This message is sent by the keypad to indicate the state of the keys. The default destination address is set to FFh: broadcast message. See chapter 2 for Key number reference. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 01h  | Key Contact state  |
| Byte 3  | XXh  | XX: Key number  |
|  Byte 4  |  YYh  | Key Contact state 00: Switch OFF (Key released) 01: Switch ON (Key pressed)  |
| Byte 5  | ZZh  | Keypad Identifier (default 21h)  |
| Byte 6,7  | FFh  | Not used  |


 Examples: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 01 01 21 FF FF  | Key 1 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 01 00 21 FF FF  | Key 1 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 02 01 21 FF FF  | Key 2 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 02 00 21 FF FF  | Key 2 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 03 01 21 FF FF  | Key 3 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 03 00 21 FF FF  | Key 3 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 04 01 21 FF FF  | Key 4 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 04 00 21 FF FF  | Key 4 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 05 01 21 FF FF  | Key 5 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 05 00 21 FF FF  | Key 5 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 06 01 21 FF FF  | Key 6 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 06 00 21 FF FF  | Key 6 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 07 01 21 FF FF  | Key 7 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 07 00 21 FF FF  | Key 7 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 08 01 21 FF FF  | Key 8 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 08 00 21 FF FF  | Key 8 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 09 01 21 FF FF  | Key 9 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 09 00 21 FF FF  | Key 9 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 0A 01 21 FF FF  | Key 10 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 0A 00 21 FF FF  | Key 10 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 0B 01 21 FF FF  | Key 11 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 0B 00 21 FF FF  | Key 11 OFF  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 0C 01 21 FF FF  | Key 12 ON  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 01 0C 00 21 FF FF  | Key 12 OFF  |


If the Event state transmission is enabled, the Key Contact state message is sent when a key is switched. 

If the periodic key-state transmission is enabled (see Command 71h for further details), at each given time interval a Key Contact state message is sent for each button of the keypad. 

## 7. LED COMMAND: 

## a. Set single LED state (01h)

The following message sent to the keypad allows to set one single LED indicator with the color and state shown in the table below. 

See chapter 2 for Key and LED number reference. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 01h  | Set single LED state  |
| Byte 3  | XXh  | XX: PKP Key number  |
| Byte 4  | YYh  | LED Color 00h: off 01h: red 02h: green 03h: blue 04h: yellow 05h: cyan 06h: magenta 07h: white/light blue 08h: amber/orange 09h: yellow/green  |
|   Byte 5  |   ZZh  | LED State 00h: off 01h: on 02h: blink 03h: alternate blink  |
|      Byte 6  |      WWh  | LED Secondary Color (only for alt blink) 00h: off 01h: red 02h: green 03h: blue 04h: yellow 05h: cyan 06h: magenta 07h: white/light blue 08h: amber/orange 09h: yellow/green  |
| Byte 7  | FFh  | Not used  |


Examples: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 01 01 01 01 00 FF  | LED Key1 red on  |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 01 01 00 00 00 FF  | LED Key1 off  |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 01 02 03 02 00 FF  | LED Key2 blinks blue  |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 01 06 01 01 00 FF  | LED Key6 red on  |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 01 08 02 01 00 FF  | LED Key8 green on  |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 01 0B 01 03 02 FF  | LED Key11 blinks red and green in alternate mode  |


## b. Set multiple LED state 

This command used as an alternative to the one described in the previous sub-chapter allows to manage more LED indicators at once. See the command 37h to enable it. 

The LED state and color can be controlled by using either or both the CAN data frames (shown in the tables 1 and 2 below) depending on the light mode you want to set: the available functions are summarized in the table 3 at page 8. 

The message to be sent to the keypad is structured so that each of four bit field within the 64 data bits controls a corresponding key-LED. 

TABLE 1:


| PGN  | A700h  | A700h  | AUXILIARY I/O #2  |
| -- | -- | -- | -- |
| Identifier  | 18A72100h  | 18A72100h  | CAN FRAME: color 1  |
| Byte 0  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 0  | Xh  | LED 1  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 0  | Yh  | LED 2  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 1  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 1  | Xh  | LED 3  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 1  | Yh  | LED 4  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 2  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 2  | Xh  | LED 5  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 2  | Yh  | LED 6  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 3  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 3  | Xh  | LED 7  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 3  | Yh  | LED 8  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 4  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 4  | Xh  | LED 9  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 4  | Yh  | LED 10  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 5  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 5  | Xh  | LED 11  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 5  | Yh  | LED 12  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 6,7  | FFh  | Not used  |   |



![](https://web-api.textin.com/ocr_image/external/6f57abdbadd32620.jpg)

TABLE 2:


| PGN  | A600h  | A600h  | AUXILIARY I/O #3  |
| -- | -- | -- | -- |
| Identifier  | 18A62100h  | 18A62100h  | CAN FRAME: color 2  |
| Byte 0  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 0  | Xh  | LED 1  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 0  | Yh  | LED 2  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 1  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 1  | Xh  | LED 3  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 1  | Yh  | LED 4  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 2  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 2  | Xh  | LED 5  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 2  | Yh  | LED 6  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 3  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 3  | Xh  | LED 7  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 3  | Yh  | LED 8  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 4  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 4  | Xh  | LED 9  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 4  | Yh  | LED 10  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 5  | YXh  | YXh  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 5  | Xh  | LED 11  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 5  | Yh  | LED 12  |  Value to  be assigned  to X and Y LED color 0 0 0 1 b 1h Red 0 0 1 0 b 2h Green 0 0 1 1 b 3h Blue 0 1 0 0 b 4h Yellow 0 1 0 1 b 5h Cyan 0 1 1 0 b 6h Magenta 0 1 1 1 b 7h White/light blue 1 0 0 0 b 8h Amber/orange 1 0 0 1 b 9h Yellow/green 1 0 1 0 b Ah 1 0 1 1 b Bh 1 1 0 0 b Ch Not used 1 1 0 1 b Dh 1 1 1 0 b Eh   |
| Byte 6,7  | FFh  | Not used  |   |


TABLE 3:


| CAN frame: color 1  | CAN frame: color 2  | LED state  |
| -- | -- | -- |
| 0h  | 0h  | LED OFF  |
| Value from 1h to 9h  | 0h  | LED ON with the chosen color  |
| 0h  | Value from 1h to 9h  | LED ON with the chosen color  |
| Value from 1h to 9h  | Same value of color 1  | LED ALT BLINKS with the chosen color  |
| Same value of color 2  | Value from 1h to 9h  | LED ALT BLINKS with the chosen color  |
| Value from 1h to 9h  | Different value from color 1  | LED ALT BLINKS between the two different colors  |
| Different value from color 2  | Value from 1h to 9h  | LED ALT BLINKS between the two different colors  |
| Value from 1h to 9h  | Fh  | LED BLINKS with the chosen color  |
| Fh  | Value from 1h to 9h  | LED BLINKS with the chosen color  |


 Examples: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18A72100h  | Ext  | 11 01 00 00 00 00 FF FF  | LED #1,2,3 red ON  |
| To Keypad  | 18A62100h  | Ext  | 00 00 00 00 00 96 FF FF  | LED #12 yellow/green ON; LED #11 magenta ON  |
| To Keypad  | 18A72100h  | Ext  | 00 02 00 00 00 00 FF FF  | LED #3 alt blinks green  |
| To Keypad  | 18A62100h  | Ext  | 00 02 00 00 00 00 FF FF  | LED #3 alt blinks green  |
| To Keypad  | 18A72100h  | Ext  | 00 00 00 04 00 00 FF FF  | LED #7 blinks yellow and cyan in alternate mode  |
| To Keypad  | 18A62100h  | Ext  | 00 00 00 05 00 00 FF FF  | LED #7 blinks yellow and cyan in alternate mode  |
| To Keypad  | 18A72100h  | Ext  | 00 00 30 00 00 00 FF FF 00 00 F0 00 00 00 FF FF  | LED #6 blinks blue  |
| To Keypad  | 18A62100h  | Ext  | 00 00 30 00 00 00 FF FF 00 00 F0 00 00 00 FF FF  | LED #6 blinks blue  |
| To Keypad  | 18A72100h  | Ext  | 00 00 00 00 FF 00 FF FF  | LED # 9,10 blink white  |
| To Keypad  | 18A62100h  | Ext  | 00 00 00 00 77 00 FF FF  | LED # 9,10 blink white  |
| To Keypad  | 18A72100h  | Ext  | 22 22 22 22 22 22 FF FF  | All LED green ON  |



![](https://web-api.textin.com/ocr_image/external/a21207fc6dcf6a3d.jpg)

## Configuration commands 

In this section it is shown a list of control messages to configure the keypad and/or modify default settings.The third byte serves as the command byte. Where applicable, changes take effect immediately and are stored in non-volatile memory address unless otherwise noted. 

Note: for some commands the set values are kept at the startup.

## 8. Set LED indicators brightness level (02h)

This message sets the value of the LED indicators brightness. The value can be set from 0 to 3Fh (min-100%) of the LED dimming range. 

NOTE: this setting has temporary effect and at the startup comes back to the default level. If the default value is desired to change, please refer to the Command 7Ch. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 02h  | Set LED indicators brightness level  |
| Byte 3  | XXh  | XX: Dim Value (default 3Fh) From 00h (min) to 3Fh (100%)  |
| Byte 4,7  | FFh  | Not used  |


Example:


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 02 20 FF FF FF FF  | Brightness set to 50%  |


## 9. Set backlight level (03h)

This message sets the value of the backlight brightness. The value can be set from 0 to 3Fh (0-100%) of the brightness range. 

NOTE: this setting has temporary effect and at the startup comes back to the default level. If the default value is desired to change, please refer to the Command 7Bh. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 03h  | Set backlight level  |
| Byte 3  | XXh  | XX: Value (default 00h) From 00h (OFF) to 3Fh (100%)  |
| Byte 4,7  | FFh  | Not used  |


Example:


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 03 10 FF FF FF FF  | Backlight set to 25%  |


## 10. Set startup keys message(28h)

 This command enables the transmission of the state of the keys during power up. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 28h  | Set startup keys message  |
|  Byte 3  |  XXh  | XX: 00h Disabled (default) 01h Enabled  |
| Byte 4,7  | FFh  | Not used  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 28 01 FF FF FF FF  | Startup keys message enabled  |


## 11. Get software revision (2Ah)


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 2Ah  | Get software revision  |
| Byte 3,7  | FFh  | Not used  |


 Answer: 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 2Ah  | Get software revision  |
| Byte 3,6  | XXh XXh XXh XXh  | SW revision ASCII  |
| Byte 7  | 00h  | Not used  |


Example 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 2A FF FF FF FF FF  | Get software revision  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B 2A 56 32 2E XX XX  | V2.XX  |


## 12. Set startup LED show (34h)


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 34h  | Set startup LED show  |
|   Byte 3  |   XXh  | XX: 00h OFF 01h Complete LED show (default) 02h Fast flash  |
| Byte 4,7  | FFh  | Not used  |


Example:


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 34 02 FF FF FF FF  | Startup fast flash enabled  |


## 13. Set LED management mode (37h)


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 37h  | Set startup LED show  |
|  Byte 3  |  XXh  | XX: 00h single LED state enabled (default) 02h single/multiple LED state enabled  |
| Byte 4,7  | FFh  | Not used  |


Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 37 02 FF FF FF FF  | Single/multiple LED state enabled  |


## 14. Set Destination Address (6Eh)

This message is used to set the addressee node of the Key Contact state transmitted by the keypad. The default destination address is FFh (broadcast). 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 6Eh  | Set Destination Address  |
|  Byte 3  | XXh  | XX: CAN Destination Address From 00h to FFh FEh: reserved  |
| Byte 4,7  | FFh  | Not used  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 6E 00 FF FF FF FF  | CAN destination address set to 00h  |



![](https://web-api.textin.com/ocr_image/external/c15fcd8905fa889f.jpg)

## 15. Baud rate setting (6Fh)

This message is used to change the baud rate of the CAN bus. Connecting only one keypad to the bus when changing the baud rate is recommended. If an invalid value is chosen, then no change is done to the stored value. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 6Fh  | Baud rate setting  |
| Byte 3  | 02h  | 500kbit/s  |
| Byte 3  | 03h  | 250kbit/s  |
| Byte 4,7  | FFh  | Not used  |


Example 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 6F 02 FF FF FF FF  | Baud rate set to 500kbit/s  |


## 16. Set Source Address (70h)

This message is used to change the keypad CAN Source Address and/or the Keypad Identifier. Either or both the Source Address or Keypad Identifier may be changed independently. Connecting only one keypad to the bus when changing the keypad address is recommended. If an invalid value is chosen, then no change is done to the stored value. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 70h  | Set Source Address  |
| Byte 3  | XXh  | XX: CAN Source Address From 01h to FDh FEh: reserved FFh: no change  |
| Byte 4  | YYh  | YY: Keypad Identifier From 00h to FDh FEh: reserved FFh: no change  |
| Byte 5,7  | FFh  | Not used  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 70 02 01 FF FF FF  | Source address set to 02h; Keypad identifier set to 01h  |


## 17. Periodic key-state transmission (71h)

This message enables or disables the periodic transmission of the state of the keys.

When enabled, one contact state message is periodically sent for each button of the keypad. 

The period is set to 100ms as default value but can be changed by command 77h. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 71h  | Periodic key-state transmission  |
|  Byte 3  |  XXh  | XX: 00h Disabled (default) 01h Enabled  |
| Byte 4,7  | FFh  | Not used  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 71 01 FF FF FF FF  | Periodic key-state transmission enabled  |


## 18. Event state transmission (72h)

This message enables or disables event driven key state transmissions. When this feature is enabled, the keypad transmits the state of one key each time the key contact changes state (pressed or released). 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 72h  | Event state transmission  |
|  Byte 3  |  XXh  | XX: 00h Disabled 01h Enabled (default)  |
| Byte 4,7  | FFh  | Not used  |


Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 72 00 FF FF FF FF  | Event state transmission disabled  |



![](https://web-api.textin.com/ocr_image/external/136edc1eff97a070.jpg)

## 19. LED Acknowledgment (73h)

This command enables or disables the transmission of the LED Acknowledgement message.When this feature is enabled the keypad transmits an acknowledgement message each time a single LED state is set by using the specific command (see chapter 7a.LED COMMAND). 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 73h  | LED Acknowledgement  |
|  Byte 3  |  XXh  | XX: 00h Disabled (default) 01h Enabled  |
| Byte 4,7  | FFh  | Not used  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 73 01 FF FF FF FF  | LED acknowledgment enabled  |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 01 01 01 03 02 FF  | Set single LED state  |
| From Keypad  | 18EFFF21h  | Ext  | 00 03 01 01 02 FF FF FF  | LED Ack message  |


##  LED Acknowledgment message: 


| Byte 0  | 00h  |   |
| -- | -- | -- |
| Byte 1  | XXh  | XX: LED state  |
| Byte 2  | YYh  | YY: LED number  |
| Byte 3  | PPh  | PP: Primary color  |
| Byte 4  | ZZh  | ZZ: Secondary color  |
| Byte 5,7  | FFh  | Not used  |


## 20. Address Claim at boot (74h)

## This message enables or disables the address claim procedure.


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 74h  | Address claim at boot  |
|  Byte 3  |  XXh  | XX: 00h Disabled (default) 01h Enabled  |
| Byte 4,7  | FFh  | Not used  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 74 01 FF FF FF FF  | Address Claim enabled  |


## Address claiming procedure: 

 Under normal operation, the keypad application sends an Address Claim parameter group at start up and waits up to 250 ms for the other devices connected to the same network to send a message containing the device’s address and name. The keypad checks every response and compares the names to see who has the highest priority. If a device is already using the address and has a higher priority, then a new address is selected, and the process starts over. If the keypad has a higher priority than the device in use then it waits for other systems to reply, while the responding device will have to change its address and send an address claim itself. If no message is received after the time (250ms) is up, then the device has claimed the address. 

## Address claim parameter group:

Priority=6.

Destination Address should always be the Global Address FFh 

Parameter Group Number (PGN) = 60928(EE00h).

Data Length = 8

Data=NAMEfield

Example:


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| From Keypad  | 18EEFF21h  | Ext  | 3F 42 6F 1A 00 82 3C C0  |   |


## 21. Heartbeat (75h)

This command enables or disables the transmission of Heartbeat message. This message is designed to indicate to other devices on the bus the keypad continues to function. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 75h  | Heartbeat  |
|  Byte 3  |  XXh  | XX: 00h Disabled (default) 01h Enabled  |
| Byte 4  | YYh  | YY: Period in milliseconds ÷ 10 From 05h (50ms) to FEh (2.54 sec)  |
| Byte 5,7  | FFh  | Not used  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 75 01 32 FF FF FF  | Heartbeat enabled with 500ms period.  |


##  Heartbeat generated message: 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | F9h  | Heartbeat message  |
| Byte 3  | XXh  | XX: Message counter, incremented each message sent  |
| Byte 4  | K8 K7 K6 K5 K4 K3 K2 K1  | Button state indicators Each bit represents a button state 0: OFF 1: ON  |
| Byte 5  | 00 00 00 00 K12 K11 K10 K9  | Button state indicators Each bit represents a button state 0: OFF 1: ON  |
| Byte 6  | FFh  | Not used  |
| Byte 7  | 21h  | Keypad identifier  |


 Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B F9 03 80 00 FF 21  | Heartbeat message with button 8 pressed.  |
| From Keypad  | 18EFFF21h  | Ext  | 04 1B F9 03 00 08 FF 21  | Heartbeat message with button 12 pressed  |


## 22. Key-state message period (77h)

 This command sets the period time for the PERIODIC KEY-STATE TRANSMISSION (71h). 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 77h  | Key-state message period  |
| Byte 3  | XXh  | XX: Period in milliseconds ÷ 10 From 05h (50ms) to FEh (2.54 sec)  |
| Byte 4,7  | FFh  | Not used  |


Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 77 0A FF FF FF FF  | Period set to 100ms  |


## 23. Start Demo mode(7Ah)

This message enables the Demo mode function. Demo mode is a special feature that consists in different LED states for each button pressing. Refer to the appendix “Demo mode instructions” to try these special features. Disconnect and reconnect the keypad after sending the message to enter this mode. To exit the Demo mode, send the Disable Demo mode command or another command message. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 7Ah  | Start Demo mode  |
|  Byte 3  |  XXh  | XX: 00h Disabled (Default) 01h Enabled  |
| Byte 4,7  | FFh  | Not used  |


Example:


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 7A 01 FF FF FF FF  | Demo mode enabled  |


## 24. Default backlight brightness level (7Bh)

This message sets the default value of the backlight brightness. The level can be set from 0 to 3Fh (0-100%) of the brightness range. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 7Bh  | Default backlight brightness level  |
| Byte 3  | XXh  | XX: Value From 00h (OFF) to 3Fh (100%)  |
| Byte 4,7  | FFh  | Not used  |


Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 7B 20 FF FF FF FF  | Default backlight level set to 50%  |


## 25. Default LED indicators brightness level (7Ch)

This message sets the default value of the LED indicators brightness. The value can be set from 0 to 3Fh (min-100%) of the LED dimming range. 


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 7Ch  | Default LED indicators brightness level  |
| Byte 3  | XXh  | XX: Value From 00h (min) to 3Fh (100%)  |
| Byte 4,7  | FFh  | Not used  |


Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 7C 10 FF FF FF FF  | Default LED indicators brightness level set to 25%  |


## 26. Default backlight color (7Dh)

 This message sets the default value of the backlight.


| Byte 0  | 04h  | Header bytes  |
| -- | -- | -- |
| Byte 1  | 1Bh  | Header bytes  |
| Byte 2  | 7Dh  | Set backlight color  |
|     Byte 3  |     XXh  | XX: color 01h: red 02h: green 03h: blue 04h: yellow 05h: cyan 06h: magenta 07h: white/light blue 08h: amber/orange 09h: yellow/green  |
| Byte 4,7  | FFh  | Not used  |


Example: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 7D 02 FF FF FF FF  | Default backlight color to green  |


## 27. Set CAN protocol 

This set of messages are used to change to the desired CANbus protocol. 

• Change from J1939 to CANopen: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 18EF2100h  | Ext  | 04 1B 80 00 FF FF FF FF  | Change to CANopen  |


• Change from CANopen to J1939: 


| Direction  | Identifier  | Format  | Message  | Data  |
| -- | -- | -- | -- | -- |
| To Keypad  | 615h  | Std  | 2B FF 20 01 01  | Change to J1939  |


## APPENDIX: DEMO Mode instructions

In DEMO Mode you can try the following functions by pressing buttons on the PKP2600SI. 

Entering this mode, you turn on backlight red; for the key 1 each time you press the button you can change the color of backlight with this sequence: 

1. Red; 

2. Green;

3. Blue; 

4. Yellow; 

5. Cyan; 

6. Magenta; 

7. White/light blue; 

8. Amber; 

9. Yellow/green; 

10. OFF.

Pressing key 2, you can increase LED and backlight brightness.

Pressing key 3, you can decrease LED and backlight brightness.

If you press the key 4, there are different steps in this sequence: 

1. Complete LED show of all colors; 

2. Backlight active with keys on in sequence (it is possible to change the color of keys by pressing button 1); 

3. Alternate blinking of LED keys number 1-7 with red color; 2-8 with amber color; 3-9 with yellow; 4-10 with green color; 5-11 with cyan color and 6-12 with white/light blue color. 

In the case you press the other keys there are no events. 


![](https://web-api.textin.com/ocr_image/external/e795ba5f897b5284.jpg)

### 28. Revision history 


| Date  | Manual Revision  | Comment  | Related SW version  |
| -- | -- | -- | -- |
| 11/12/2017  | 1.0  | First release PKP2600SI J1939 manual  | SW2.x  |
| 18/09/2018  | 1.1  | Second release: - added an alternative command for managing the LED state  | SW x.x  |
| 21/02/2019  | 1.2  | Third release: - added warning note at page 2  | SW x.x  |
| 07/05/2019  | 1.3  | Fourth release: - In compliance with the SAE J1939 standard, in the command {70h} the value FEh has been excluded from the list of the addresses assignable by the user to the keypad  | SW x.x  |
| 22/06/2020  | 1.4  | Fifth release: -Demo mode instructions chapter reviewed -Added multiple LED state command  | SW x.x  |
| 28/09/2020  | 1.5  | Sixth release: -Added command 6Eh  | SW x.x  |




