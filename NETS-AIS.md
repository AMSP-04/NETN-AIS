# NETN-AIS

Copyright (C) 2019 NATO/OTAN.
This work is licensed under a [Creative Commons Attribution-NoDerivatives 4.0 International License](LICENCE.md).

## Overview

The following figure shows the class structure, with the following two classes at the root of the structure:

- `AisRadioSignal` is sub-classed from the RPR-FOM 2.0 class `RadioSignal` and represents an AIS radio signal. `AisRadioSignal` optionally includes a reference to a Transmitter object instance.
- `AisMessage` is sub-classed from `AisRadioSignal` and is the super class for all AIS message types. This class defines the AIS message parameters that are common across all sub-classes.

<img src="./images/AIS Interaction Class Tree.png" width="75%"/>

The modelled AIS message types are:

| Message type | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| 1            | **Position Report Class A**. Message type for a scheduled position report; Class A shipborne mobile equipment. This message transmits information pertaining to a ships navigation: Longitude and latitude, time, heading, speed, ships navigation status (under power, at anchor...). This message is transmitted every 2 to 10 seconds while underway, and every 3 minutes while at anchor. |
| 2            | Idem.                                                        |
| 3            | Idem.                                                        |
| 4            | **Base Station Report**. This message is to be used by fixed-location base stations to periodically report a position and time reference. |
| 5            | **Static and Voyage Related Data**. This message type is transmitted every 6 minutes and should only be used by Class A shipborne and SAR aircraft AIS stations when reporting static or voyage related data. |
| 6            | **Binary Addressed Message**. This message type is an addressed point-to-point message with unspecified binary payload. |
| 7            | **Binary Acknowledge**. This message type is a receipt acknowledgement to the senders of a previous messages of Message Type 6. Total length varies depending on the number of destination MMSIs included (1..4). |
| 8            | **Binary Broadcast Message**. This message type is a broadcast message with a binary payload. |
| 9            | **Standard SAR Aircraft Position Report**. Tracking information for search-and-rescue aircraft. |
| 10           | **UTC/Date Inquiry**. Request for UTC/Date information from an AIS base station. |
| 11           | **UTC/Date Response**. Identical to Message Type 4, with the semantics of a response to inquiry. This message type is only transmitted from a mobile station as a result of a UTC request message (Message Type 10). |
| 12           | **Addressed Safety-Related Message**. This is a point-to-point text message. The payload is interpreted as six-bit text. |
| 13           | **Safety-Related Acknowledgement**. This message type is a receipt acknowledgement to senders of previous messages of Message Type 12. |
| 14           | **Safety-Related Broadcast Message**. This is a broadcast text message. The payload is interpreted as six-bit text. |
| 17           | **DGNSS Broadcast Binary Message**. This message type is used to broadcast differential corrections for GPS. The data in the payload is intended to be passed directly to GPS receivers capable of accepting such corrections. |
| 18           | **Standard Class B CS Position Report**. A less detailed report than message types 1-3 for vessels using Class B transmitters. Omits navigational status and rate of turn. |
| 19           | **Extended Class B CS Position Report**. A slightly more detailed report than Message Type 18 for vessels using Class B transmitters. Omits navigational status and rate of turn. Fields are as in the common navigation block and the Message Type 5 message. |
| 21           | **Aid-to-Navigation Report**. Identification and location message to be emitted by aids to navigation such as buoys and lighthouses. |
| 24           | **Static Data Report**. Equivalent of Message Type 5 for ships using Class B equipment. Also used to associate an MMSI with a name on either class A or class B equipment. This message type may be in part A or part B format. According to the standard, parts A and B are expected to be broadcast in adjacent pairs. |
| 27           | **Long Range AIS Broadcast message**. This message type is primarily intended for long-range detection of AIS Class A equipped vessels (typically by satellite). This message has a similar content to Messages 1, 2 and 3, but the total number of bits has been compressed to allow for increased propagation delays associated with long-range detection. |

## Class parameters

Many of the class parameters are optional. For each optional parameter a default value is defined that can be assumed by the receiving HLA federate application if no parameter value has been sent.

## Six-bit ASCII characters

Several parameters are coded as six-bit characters. For example, vessel name and callsign.  The parameter datatype of a six-bit character string is `HLAASCIIstring` and the following table shows the ASCII character to be used for a each six-bit character.

| 000000 | 0    | "@"  | 010000 | 16   | "P"  | 100000 | 32   | " "  | 110000 | 48   | "0"  |
| ------ | ---- | ---- | ------ | ---- | ---- | ------ | ---- | ---- | ------ | ---- | ---- |
| 000001 | 1    | "A"  | 010001 | 17   | "Q"  | 100001 | 33   | "!"  | 110001 | 49   | "1"  |
| 000010 | 2    | "B"  | 010010 | 18   | "R"  | 100010 | 34   | """  | 110010 | 50   | "2"  |
| 000011 | 3    | "C"  | 010011 | 19   | "S"  | 100011 | 35   | "\#" | 110011 | 51   | "3"  |
| 000100 | 4    | "D"  | 010100 | 20   | "T"  | 100100 | 36   | "$"  | 110100 | 52   | "4"  |
| 000101 | 5    | "E"  | 010101 | 21   | "U"  | 100101 | 37   | "%"  | 110101 | 53   | "5"  |
| 000110 | 6    | "F"  | 010110 | 22   | "V"  | 100110 | 38   | "&"  | 110110 | 54   | "6"  |
| 000111 | 7    | "G"  | 010111 | 23   | "W"  | 100111 | 39   | "\'" | 110111 | 55   | "7"  |
| 001000 | 8    | "H"  | 011000 | 24   | "X"  | 101000 | 40   | "("  | 111000 | 56   | "8"  |
| 001001 | 9    | "I"  | 011001 | 25   | "Y"  | 101001 | 41   | ")"  | 111001 | 56   | "9"  |
| 001010 | 10   | "J"  | 011010 | 26   | "Z"  | 101010 | 42   | "\*" | 111010 | 58   | ":"  |
| 001011 | 11   | "K"  | 011011 | 27   | "["  | 101011 | 43   | "\+" | 111011 | 59   | ";"  |
| 001100 | 12   | "L"  | 011100 | 28   | "\"  | 101100 | 44   | ","  | 111100 | 60   | "<"  |
| 001101 | 13   | "M"  | 011101 | 29   | "]"  | 101101 | 45   | "-"  | 111101 | 61   | "="  |
| 001110 | 14   | "N"  | 011110 | 30   | "\^" | 101110 | 46   | "."  | 111110 | 62   | ">"  |
| 001111 | 15   | "O"  | 011111 | 31   | "\_" | 101111 | 47   | "/"  | 111111 | 63   | "?"  |

## Transmitter

Optionally an AIS Radio Signal can be associated with a Transmitter object instance. The non-optional attributes of the Transmitter object instance may be assigned for example the following values:

| Attribute name                 | Description                                                  | Value                                                        |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `Frequency`                    | Center frequency of the radio transmissions.                 | 162 MHz                                                      |
| `FrequencyBandwidth`           | Bandpass of the radio transmissions, specified in hertz.     | 25000 Hz                                                     |
| `RadioIndex`                   | Specifies the identification number for each radio on a given host. This ID shall not change during an exercise. | Per agreement.                                               |
| `RadioSystemType`              | Kind, Country, Domain, Category, Nomenclature Version, and Nomenclature of the DIS Radio Type. This ID shall not change during an exercise. | Kind, Domain, Country, Category, Subcategory, Specific, Extra = 7.3.0.37.0.0.0 |
| `TransmittedPower`             | The average power being transmitted in units of decibel-milliwatts. | 12500 Milliwatts for class A, or 2000 Milliwatts for class B |
| `TransmitterOperationalStatus` | On/Off state of the transmitter as an enumeration.           | `on` or `off`                                                |
| `WorldLocation`                | Location of the antenna in world coordinates.                | Typically the vessel position.                               |

