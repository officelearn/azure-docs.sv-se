---
title: Azure Digital Twins användardefinierade funktioner-klientbiblioteksreferens | Microsoft Docs
description: Azure Digital Twins användardefinierade funktioner-klientbiblioteksreferens.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 12/27/2018
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: d309765e6c4b0b0cc8a7e92977134d4135a69a13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535597"
---
# <a name="user-defined-functions-client-library-reference"></a>Användardefinierade funktioner-klientbiblioteksreferens

Det här dokumentet innehåller information om klientbiblioteket för Azure Digital Twins användardefinierade funktioner.

## <a name="helper-methods"></a>Hjälpmetoder

Klientbiblioteket definierar hjälpmetoder för vanliga åtgärder.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Med en utrymmesidentifierare kan returnerar den här funktionen utrymmet från diagrammet.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | utrymmesidentifierare |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Med en sensor identifierare kan returnerar den här funktionen sensorn från diagrammet.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | sensorn identifierare |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Med en enhets-ID kan returnerar den här funktionen enheten från diagrammet.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *ID* | `guid` | Enhets-ID |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, dataType) ⇒ `value`

Den här funktionen hämtar baserat på en sensor-identifierare och dess datatyp, det aktuella värdet för den sensorn.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | sensorn identifierare |
| *dataType*  | `string` | sensorn datatyp |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Den här funktionen hämtar baserat på en utrymmesidentifierare och värdenamn, det aktuella värdet för egenskapen utrymme.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | utrymmesidentifierare |
| *valueName* | `string` | egenskapsnamn med blanksteg |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, dataType) ⇒ `value[]`

Den här funktionen hämtar baserat på en sensor-identifierare och dess datatyp, historiska värden för den sensorn.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | sensorn identifierare |
| *dataType* | `string` | sensorn datatyp |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, dataType) ⇒ `value[]`

Den här funktionen hämtar baserat på en utrymmesidentifierare och värdenamn, historiska värden för den egenskapen på området.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |
| *valueName* | `string` | egenskapsnamn med blanksteg |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Den här funktionen hämtar med en utrymmesidentifierare kan underordnade adressutrymmen för det överordnade området.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Den här funktionen hämtar med en utrymmesidentifierare kan underordnade sensorer för det överordnade området.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Den här funktionen hämtar med en utrymmesidentifierare kan underordnade enheter för det överordnade området.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Med en enhets-ID kan returnerar den här funktionen underordnade sensorer för den överordnade enheten.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Enhets-ID |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Den här funktionen hämtar med en utrymmesidentifierare kan dess överordnade utrymme.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | utrymmesidentifierare |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Den här funktionen hämtar med en sensor identifierare kan dess överordnade utrymme.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | sensorn identifierare |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Den här funktionen hämtar med en enhets-ID kan dess överordnade utrymme.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Enhets-ID |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Med en sensor identifierare kan returnerar den här funktionen den överordnade enheten.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | sensorn identifierare |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Den här funktionen hämtar med en utrymmesidentifierare kan egenskapen och dess värde från området.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |
| *propertyName* | `string` | egenskapsnamn med blanksteg |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Den här funktionen hämtar med en sensor identifierare kan egenskapen och dess värde från sensorn.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | sensorn identifierare |
| *propertyName* | `string` | Egenskapen sensornamnet |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (deviceId, propertyName) ⇒ `extendedProperty`

Den här funktionen hämtar med en enhets-ID kan egenskapen och dess värde från enheten.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Enhets-ID |
| *propertyName* | `string` | Egenskapen enhetsnamn |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, datatyp, värde)

Den här funktionen anger ett värde på sensor-objektet med den angivna datatypen.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | sensorn identifierare |
| *dataType*  | `string` | sensorn datatyp |
| *värde*  | `string` | Värde |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, datatyp, värde)

Den här funktionen anger ett värde på utrymme-objektet med den angivna datatypen.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |
| *dataType* | `string` | Datatyp |
| *värde* | `string` | Värde |

### <a name="logmessage"></a>log(Message)

Den här funktionen loggar följande meddelande i den användardefinierade funktionen.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *meddelande* | `string` | meddelandet som ska loggas |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, nyttolast)

Den här funktionen skickas ett anpassat meddelande skickas.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Diagram över objektidentifierare. Exempel är utrymme, sensor och enhets-ID.|
| *topologyObjectType*  | `string` | Exempel är sensor- och enhetsdata.|
| *payload*  | `string` | JSON-nyttolasten skickas med meddelandet. |

## <a name="return-types"></a>Returnera typer

Svar-modeller som returneras från klienten referens hjälpmetoder beskrivs nedan.

### <a name="space"></a>Rymd

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>Utrymme metoder

#### <a name="parent--space"></a>Parent() ⇒ `space`

Den här funktionen returnerar överordnade utrymme på utrymmet.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Den här funktionen returnerar underordnade sensorer aktuella utrymme.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Den här funktionen returnerar underordnade enheter på det aktuella utrymmet.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för det aktuella utrymmet.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | namnet på den utökade egenskapen |

#### <a name="valuevaluename--value"></a>Value(ValueName) ⇒ `value`

Den här funktionen returnerar värdet för det aktuella utrymmet.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | namnet på värdet |

#### <a name="historyvaluename--value"></a>History(ValueName) ⇒ `value[]`

Den här funktionen returnerar historiska värdena för det aktuella utrymmet.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | namnet på värdet |

#### <a name="notifypayload"></a>Notify(payload)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON-nyttolast ska inkluderas i meddelandet |

### <a name="device"></a>Enhet

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Enhetsmetoder

#### <a name="parent--space"></a>Parent() ⇒ `space`

Den här funktionen returnerar överordnade utrymme på den aktuella enheten.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Den här funktionen returnerar underordnade sensorer för den aktuella enheten.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för den aktuella enheten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | namnet på den utökade egenskapen |

#### <a name="notifypayload"></a>Notify(payload)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON-nyttolast ska inkluderas i meddelandet |

### <a name="sensor"></a>Sensor

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Sensorn metoder

#### <a name="space--space"></a>Space() ⇒ `space`

Den här funktionen returnerar överordnade utrymme på den aktuella sensorn.

#### <a name="device--device"></a>Device() ⇒ `device`

Den här funktionen returnerar den överordnade enheten för den aktuella sensorn.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för den aktuella sensorn.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | namnet på den utökade egenskapen |

#### <a name="value--value"></a>Value() ⇒ `value`

Den här funktionen returnerar värdet för den aktuella sensorn.

#### <a name="history--value"></a>History() ⇒ `value[]`

Den här funktionen returnerar historiska värdena för den aktuella sensorn.

#### <a name="notifypayload"></a>Notify(payload)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON-nyttolast ska inkluderas i meddelandet |

### <a name="value"></a>Värde

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Utökad egenskap

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Digital Twins användardefinierade funktioner](./concepts-user-defined-functions.md).

- Lär dig [så här skapar du användardefinierade funktioner](./how-to-user-defined-functions.md).

- Lär dig [Felsök användardefinierade funktioner](./how-to-diagnose-user-defined-functions.md).
