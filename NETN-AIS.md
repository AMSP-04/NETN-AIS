# NETN-AIS

NATO Education and Training Network (NETN) Automatic Identification System (AIS) Module

Copyright (C) 2020 NATO/OTAN.
This work is licensed under a [Creative Commons Attribution-NoDerivatives 4.0 International License](LICENCE.md).

## Introduction

The Automatic Identification System (AIS) is a world-wide automated tracking system used on vessels and by Vessel Traffic Services (VTS) for identifying and locating vessels by electronically exchanging messages with other nearby vessels and VTS stations. Twenty-seven (27) message types are defined in ITU-R M.1371-5 annexe 8 (see https://www.itu.int). 

Common message types include the Position Report Class A (Message types 1, 2, and 3) and the Static and Voyage Related Data (Message type 5). A common standard protocol for live AIS messages exchange is NMEA-0183, an electrical and data specification for communication between marine electronics (see https://www.nmea.org).

The AIS FOM module defines all of the message types specified in ITU-R M.1371-5 annexe 8, except for rarely used message types or message types that are more related to traffic analysis in an AIS network (i.e. 15, 16, 20, 22, 23, 25 and 26). The FOM class structure aligns with the message type specification in ITU-R M.1371-5 annexe 8, enabling easy translation to/from NMEA 0183. The message types are modelled as interaction classes, and most parameters are optional. The root class `AIS_RadioSignal` extends the RPR-FOM interaction class `RadioSignal`, and uses the parameter `HostRadioIndex` to refer to the `RadioTransmitter` object instance of the AIS station.

The NETN-AIS FOM module content is based on ITU-R M.1371-5, Technical characteristics for an automatic identification system using time division multiple access in the VHF maritime mobile frequency band (https://www.itu.int).

## Purpose

The purpose of the NETN-AIS FOM module is to:

- represent vessel traffic in a simulation using AIS messages to for example communicate position status reports of vessels;
- enable the exchange of AIS messages between HLA federate applications in both a real-time and non-real-time platform level simulation;
- allow HLA federate applications to use regular HLA interaction classes and parameters to represent vessel information, and leave the translation to the physical message format in ITU-R M.1371-5 to, for example, dedicated gateways.

The NETN-AIS FOM module defines almost all of the AIS message types, whose content can be interpreted by applications without extensive knowledge of the ITU-R M.1371-5 standard and the message format defined by that standard.

## Scope

The NETN-AIS FOM module is simulation oriented and does not focus on the physical message format as defined by ITU-R M.1371-5. However, the FOM is aligned well with ITU-R M.1371-5, enabling relatively easy mapping to/from the message format defined in ITU-R M.1371-5.

## Overview

The NETN-AIS FOM module defines:

- The AIS message types.
- Several extensions to NETN-ETR tasks for instructing simulated entities to send specific AIS message types.
- Several extensions to NETN-ORG equipment items for creating AIS stations in a simulation.

All AIS message types are modelled as HLA Interaction Classes. The NETN-ETR extensions are modelled as HLA Interaction Classes, and the NETN-ORG extensions are modelled as HLA Object Classes.

The following figure shows the interaction class structure, where: 

- The AIS message-related classes are located under the RPR-FOM `RadioSignal` class. The `AIS_RadioSignal` class is a sub-class of the class `RadioSignal` and represents an AIS radio signal. `AIS_RadioSignal` optionally includes a reference to a Transmitter object instance. `AisMessage` is a sub-class of `AIS_RadioSignal`, which is the super class for all AIS message types. This class defines the AIS message parameters that are common across all sub-classes.
- The NETN-ETR extensions are located under the  NETN-ETR `ETR_Task` class. AIS-specific task definitions are sub-classes of `ETR_Task`. Although the extensions are located in this FOM module, the agreements as described in the NETN-ETR FOM module shall be followed in processing these extensions.

<img src="./images/NETN-AIS Interaction Class Tree.png" width="75%"/>

Figure: Interaction Class Structure

The following figure shows the object class structure, where:

- The NETN-ORG extensions are located under the NETN-ORG `EquipmentItem` class. These extensions provide additional information for creating AIS stations in a simulation. The agreements, as described in the NETN-ORG FOM module, shall be followed in processing these extensions.

<img src="./images/NETN-AIS Object Class Tree.png" width="75%"/>

### AIS message types

The modelled AIS message types are:

| Message type | Description |
| ------------ | ------------------------------------------------------------ |
| 1 | **Position Report Class A**. Message type for a scheduled position report; Class A shipborne mobile equipment. This message transmits information about the navigation of a ship: Longitude and latitude, time, heading, speed, ships navigation status, e.g. under power, at anchor. This message is transmitted every 2 to 10 seconds while underway, and every 3 minutes while at anchor. |
| 2 | Idem. |
| 3 | Idem. |
| 4 | **Base Station Report**. This message is to be used by fixed-location base stations to report a position and time reference periodically. |
| 5 | **Static and Voyage Related Data**. This message type is transmitted every 6 minutes and should only be used by Class A shipborne and SAR aircraft AIS stations when reporting static or voyage related data. |
| 6 | **Binary Addressed Message**. This message type is an addressed point-to-point message with an unspecified binary payload. |
| 7 | **Binary Acknowledge**. This message type is a receipt acknowledgement to the senders of a previous message of Message Type 6. Total length varies depending on the number of destination MMSIs included (1..4). |
| 8 | **Binary Broadcast Message**. This message type is a broadcast message with a binary payload. |
| 9 | **Standard SAR Aircraft Position Report**. Tracking information for search-and-rescue aircraft. |
| 10 | **UTC/Date Inquiry**. Request for UTC/Date information from an AIS base station. |
| 11 | **UTC/Date Response**. Identical to Message Type 4, with the semantics of a response to an inquiry. This message type is only transmitted from a mobile station as a result of a UTC request message (Message Type 10). |
| 12 | **Addressed Safety-Related Message**. This is a point-to-point text message. The payload is interpreted as a six-bit text. |
| 13 | **Safety-Related Acknowledgement**. This message type is a receipt acknowledgement to senders of previous messages of Message Type 12. |
| 14 | **Safety-Related Broadcast Message**. This is a broadcast text message. The payload is interpreted as a six-bit text. |
| 17 | **DGNSS Broadcast Binary Message**. This message type is used to broadcast differential corrections for GPS. The data in the payload is intended to be passed directly to GPS receivers capable of accepting such corrections. |
| 18 | **Standard Class B CS Position Report**. A less detailed report than message types 1-3 for vessels using Class B transmitters. The navigational status and rate of turn is not included in this message type. |
| 19 | **Extended Class B CS Position Report**. A slightly more detailed report than Message Type 18 for vessels using Class B transmitters. The navigational status and rate of turn is not included in this message type. The fields are as defined in the common navigation block and the Message Type 5 message. |
| 21 | **Aid-to-Navigation Report**. Identification and location message to be emitted by aids to navigation such as buoys and lighthouses. |
| 24 | **Static Data Report**. The equivalent of Message Type 5 for ships using Class B equipment. They are also used to associate an MMSI with a name on either class A or class B equipment. This message type may be in part A or part B format. According to the standard, parts A and B are expected to be broadcast in adjacent pairs. |
| 27 | **Long Range AIS Broadcast message**. This message type is primarily intended for long-range detection of AIS Class A equipped vessels (typically by satellite). This message has similar content to Messages 1, 2 and 3, but the total number of bits is compressed to allow for increased propagation delays associated with long-range detection. |

### NETN-ETR extensions

The NETN-ETR `ETR_Task` extensions are summarized in the following table. These extensions may be useful in SAR vignettes, for example.

| Task                                | Description                                                  |
| ----------------------------------- | ------------------------------------------------------------ |
| `SendSafetyRelatedMessage`          | Tasks entity (the source) to send a safety related message (AIS message type 12) to another entity (the destination). Both source and destination must represent an AIS station (vessel, SAR aircraft, etc). |
| `SendSafetyRelatedBroadcastMessage` | Tasks entity (the source) to send a safety related broadcast message (AIS message type 14). The source must represent an AIS station (vessel, SAR aircraft, etc). |

### NETN-ORG extensions

The NETN-ORG `EquipmentItem` extensions are summarized in the following table.

| EquipmentItem sub-classes          | Description                                                  |
| ---------------------------------- | ------------------------------------------------------------ |
| `AisEquipmentItem`                 | This class defines additional attributes for AIS equipment. Depending on the kind of AIS equipment, further attributes are added in sub-classes. |
| `AisEquipmentItem.Vessel`          | This class defines additional attributes for vessel equipment. |
| `AisEquipmentItem.SARaircraft`     | This class defines additional attributes for SAR aircraft equipment. |
| `AisEquipmentItem.Basestation`     | This class defines additional attributes for basestation equipment. |
| `AisEquipmentItem.AidToNavigation` | This class defines additional attributes for aid to navigation (ATON) equipment. |

The `AisEquipmentItem` defines, amongst others, the `RadioSystemType` of the AIS station. The `RadioSystemType` should be used by the federate to initialize the `RadioTransmitter` object instance. Entity type values that may be used for the `RadioSystemType` are:

- `7.3.0.37.0.0.0` for a default AIS transmitter that relates to the AIS station modelled
- `7.3.0.37.1.0.0` for a class A transmitter
- `7.3.0.37.2.0.0` for a class B/SO (Self-Organizing) transmitter
- `7.3.0.37.3.0.0` for a class B/CS (Carrier-Sense) transmitter

See Radio Kind in the SISO Enumerations, [UID 22] for categories. By adding a subcategory (i.e. `1` ,`2`, etc., not defined in the SISO Enumerations), it is possible to identify the specific type of transmitter, namely class A, B/SO, or B/CS in this example. These non-standard subcategory values should be defined as part of the federation agreements. The country code value `0` in this example should be replaced by the appropriate value.

## AisMessage class parameters

### Optional and required parameters

Many of the `AisMessage` class parameters are optional. For each optional parameter, a default value is defined that can be assumed by the receiving HLA federate if no parameter value is provided.

For example, to transmit a **Position Report Class A** message, only the following parameters are required:

| Parameter name | Semantics |
| -------------- | ------------------------------------------------------- |
| `MessageId` | Message type identifier. |
| `UserId` | The message is from the vessel identified by this MMSI. |

All other parameters are optional. However, to include a minimum amount of navigation data in the message and make the message useful, the following parameters should also be provided:

| Parameter name | Semantics |
| -------------- | ---------------------------------------------------------- |
| `Position` | OPTIONAL (Default: not available). AIS (Lat,Lon) position. |
| `UTCtime` | OPTIONAL (Default: not available). Time of the report. |

Other parameters include true heading, course, rate of turn, etc. These are all optional but can be provided when available.

Note that the AIS position in the NETN-AIS FOM module is defined as a `GeodeticLocation` datatype. This is different from ITU-R M.1371-5, where Longitude and Latitude are represented in 1/10 000 min and stored in a 28 and 27-bit field, respectively. The purpose of this FOM module is to not bother the user with the message format in ITU-R M.1371-5, but rather let the user focus on the information that is exchanged in the simulation. The physical message format is not a concern of this FOM module. However, the class and parameter structure is such that the mapping between the NETN-AIS FOM and ITU-R M.1371-5 is straightforward.

### Six-bit ASCII character string datatype

Several parameters are typed as six-bit character strings—for example, vessel name and callsign. The parameter data type of a six-bit character string is `HLAASCIIstring`, and the following table shows the ASCII character to be used for each six-bit character.

| six-bit | dec | char | six-bit | dec | char | six-bit | dec | char | six-bit | dec | char |
| ------ | ---- | ---- | ------ | ---- | ---- | ------ | ---- | ---- | ------ | ---- | ---- |
| 000000 | 0 | "@" | 010000 | 16 | "P" | 100000 | 32 | " " | 110000 | 48 | "0" |
| 000001 | 1 | "A" | 010001 | 17 | "Q" | 100001 | 33 | "!" | 110001 | 49 | "1" |
| 000010 | 2 | "B" | 010010 | 18 | "R" | 100010 | 34 | """ | 110010 | 50 | "2" |
| 000011 | 3 | "C" | 010011 | 19 | "S" | 100011 | 35 | "\#" | 110011 | 51 | "3" |
| 000100 | 4 | "D" | 010100 | 20 | "T" | 100100 | 36 | "$" | 110100 | 52 | "4" |
| 000101 | 5 | "E" | 010101 | 21 | "U" | 100101 | 37 | "%" | 110101 | 53 | "5" |
| 000110 | 6 | "F" | 010110 | 22 | "V" | 100110 | 38 | "&" | 110110 | 54 | "6" |
| 000111 | 7 | "G" | 010111 | 23 | "W" | 100111 | 39 | "\'" | 110111 | 55 | "7" |
| 001000 | 8 | "H" | 011000 | 24 | "X" | 101000 | 40 | "(" | 111000 | 56 | "8" |
| 001001 | 9 | "I" | 011001 | 25 | "Y" | 101001 | 41 | ")" | 111001 | 56 | "9" |
| 001010 | 10 | "J" | 011010 | 26 | "Z" | 101010 | 42 | "\*" | 111010 | 58 | ":" |
| 001011 | 11 | "K" | 011011 | 27 | "[" | 101011 | 43 | "\+" | 111011 | 59 | ";" |
| 001100 | 12 | "L" | 011100 | 28 | "\" | 101100 | 44 | "," | 111100 | 60 | "<" |
| 001101 | 13 | "M" | 011101 | 29 | "]" | 101101 | 45 | "-" | 111101 | 61 | "=" |
| 001110 | 14 | "N" | 011110 | 30 | "\^" | 101110 | 46 | "." | 111110 | 62 | ">" |
| 001111 | 15 | "O" | 011111 | 31 | "\_" | 101111 | 47 | "/" | 111111 | 63 | "?" |

### EpochTimeSecInt64 datatype

Time is represented as `EpochTimeSecInt64`. This value represents the number of simulation seconds since the Epoch, 1 Jan 1970. In most messages, time is in relation to an AIS position update in an AIS message type.

## RadioTransmitter

Optionally an AIS Radio Signal can be associated with a `RadioTransmitter` object instance. The non-optional attributes of the `RadioTransmitter` object instance may be assigned the following values:

| Attribute name | Description | Value |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `Frequency` | Center frequency of the radio transmissions, specified in Herz. | `162000000` Hz |
| `FrequencyBandwidth` | Bandpass of the radio transmissions, specified in Hertz. | `25000` Hz |
| `RadioIndex` | Specifies the identification number for each radio on a given host. This value shall not change during simulation execution. | Per agreement. If the `RadioTransmitter` is the only radio for the vessel, the index `0` should be used. |
| `RadioSystemType` | Entity type of the radio transmitter: Kind, Domain, Country, Category. This value shall not change during simulation execution. Note that no further refinement of the type (subcategory etc.) is defined. The country value `0` should be replaced by the actual value. | `7.3.0.37` |
| `TransmittedPower` | The average power being transmitted in units of decibel-milliwatts. | `12.5` watt for class A (`41` dBm), `5` watt for class B/SO (`37` dBm), and `2` watt for class B/CS (`33` dBm). |
| `TransmitterOperationalStatus` | On/Off state of the transmitter as an enumeration. | `Off`, `OnButNotTransmitting` or `OnAndTransmitting` |
| `WorldLocation` | Location of the antenna in world coordinates. | The vessel position. |

## Entity marking and callsign

As a best practice the vessel callsign (a seven (7) six-bit character string) should be based on (a) the `Callsign` attribute of the `NETN_SurfaceVessel` class or (b) the `Marking` attribute of the `SurfaceVessel` class, depending on what class is used. The size of these attributes is, however, different, as summarized in the following table:

| Attribute | Datatype | Size |
| ------------------------------------------------------------ | -------------------- | --------- |
| `BaseEntity.PhysicalEntity.Platform.SurfaceVessel.NETN_SurfaceVessel.Callsign` | ` HLAunicodeString ` | Unlimited |
| `BaseEntity.PhysicalEntity.Platform.SurfaceVessel.Marking` | `MarkingStruct` | 11 Octets |

When defining values for entity marking or callsign, the limitation for the vessel callsign should be taken into account.

## NETN-ORG MSDL extension

The MSDL schema has been extended with an AIS schema to persist the AIS-related ORBAT data in a file, together with the rest of the ORBAT data. The AIS schema is located in this repository, and the MSDL schema is located in the NETN-ORG repository. For more information about the MSDL schema and extensions, see the NETN-ORG repository.

