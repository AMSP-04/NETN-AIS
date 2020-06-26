# NETN-AIS

NATO Education and Training Network (NETN) Automatic Identification System (AIS) Module

Copyright (C) 2020 NATO/OTAN.
This work is licensed under a [Creative Commons Attribution-NoDerivatives 4.0 International License](LICENCE.md).

## Overview

All AIS message types are modelled as HLA Interaction Classes. The following figure shows the class structure, with the following two classes at the root of the structure:

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

Many of the class parameters in the NETN-AIS FOM module are optional. For each optional parameter a default value is defined (in the semantics of the respective parameter) that can be assumed by the receiving HLA federate application if no parameter value has been provided.

For example, to transmit a **Position Report Class A** message only the following parameters are required:

| Parameter name | Semantics                                               |
| -------------- | ------------------------------------------------------- |
| `MessageId`    | Message type identifier.                                |
| `UserId`       | The message is from the vessel identified by this MMSI. |

All other parameters are optional. However, to include a minimum amount of navigation data in the message and make the message useful, the following parameters should also be provided:

| Parameter name | Semantics                                                  |
| -------------- | ---------------------------------------------------------- |
| `Position`     | OPTIONAL (Default: not available). AIS (Lat,Lon) position. |
| `UTCtime`      | OPTIONAL (Default: not available). Time of the report.     |

Other parameters include true heading, course, rate of turn, etc. These are all optional, but can be provided when available.

Note that the AIS position in the NETN-AIS FOM module is defined as a `GeodeticLocation` datatype. This is different from ITU-R M.1371-5, where Longitude and Latitude are defined in 1/10 000 min and stored in a 28 and 27 bit field respectively. The purpose of this FOM module is to not bother the user with the message format in ITU-R M.1371-5, but rather let the user focus on the information that is exchanged in the simulation. The physical message format is not a concern of this FOM module, however the class and parameter structure is such that the mapping between the NETN-AIS FOM and ITU-R M.1371-5 is straightforward.

## Six-bit ASCII character string datatype

Several parameters in the FOM module are typed as six-bit character strings. For example, vessel name and callsign. The parameter datatype of a six-bit character string is `HLAASCIIstring` and the following table shows the ASCII character to be used for a each six-bit character.

| six-bit | dec  | char | six-bit | dec | char | six-bit | dec | char | six-bit | dec | char |
| ------ | ---- | ---- | ------ | ---- | ---- | ------ | ---- | ---- | ------ | ---- | ---- |
| 000000 | 0    | "@"  | 010000 | 16   | "P"  | 100000 | 32   | " "  | 110000 | 48   | "0"  |
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

## EpochTimeSecInt64 datatype

Time in AIS messages is represented as `EpochTimeSecInt64`. This value represents the number of seconds since the Epoch. The Epoch is:

- 1 Jan 1970 (when using `wall clock time` ) or

- the value `0` (when using `logical time`).

In most messages time is in relation to an AIS position update.

## RadioTransmitter

Optionally an AIS Radio Signal can be associated with a `RadioTransmitter` object instance. The non-optional attributes of the `RadioTransmitter` object instance may be assigned the following values:

| Attribute name                 | Description                                                  | Value                                                        |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `Frequency`                    | Center frequency of the radio transmissions.                 |  Channel A `161.975 MHz` (87B), or Channel B `162.025 MHz` (88B)  |
| `FrequencyBandwidth`           | Bandpass of the radio transmissions, specified in hertz.     | `25` kHz                                                     |
| `RadioIndex`                   | Specifies the identification number for each radio on a given host. This value shall not change during a simulation execution. | Per agreement. If the `RadioTransmitter` is the only radio for the vessel, the index `0` should be used. |
| `RadioSystemType`              | Entity type of the radio transmitter: Kind, Domain, Country, Category, Subcategory, Specific, Extra. This value shall not change during a simulation execution. | `7.3.0.37.0.0.0`                                             |
| `TransmittedPower`             | The average power being transmitted in units of decibel-milliwatts. | `12500` Milliwatts for class A, or `2000` Milliwatts for class B |
| `TransmitterOperationalStatus` | On/Off state of the transmitter as an enumeration.           | `Off`, `OnButNotTransmitting` or `OnAndTransmitting`         |
| `WorldLocation`                | Location of the antenna in world coordinates.                | The vessel position.                                         |

## Entity marking and callsign

As a best practice the vessel callsign (a 7 six-bit character string) should be based on (a) the `Callsign` attribute of the `NETN_SurfaceVessel` class or (b) the `Marking` attribute of the `SurfaceVessel` class, depending on what class is used. The size of these attributes is however different, as summarized in the following table:

| Attribute                                                    | Datatype             | Size      |
| ------------------------------------------------------------ | -------------------- | --------- |
| `BaseEntity.PhysicalEntity.Platform.SurfaceVessel.NETN_SurfaceVessel.Callsign` | ` HLAunicodeString ` | Unlimited |
| `BaseEntity.PhysicalEntity.Platform.SurfaceVessel.Marking`   | `MarkingStruct`      | 11 Octets |

When defining values for entity marking or callsign, the limitation for the vessel callsign should be taken into account.
