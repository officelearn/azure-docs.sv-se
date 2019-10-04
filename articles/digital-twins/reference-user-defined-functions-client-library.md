---
title: Azure Digitals dubbla funktioner klient biblioteks referens för användar definierade funktioner | Microsoft Docs
description: Azure Digitals dubbla användares klient biblioteks referens för användar definierade funktioner.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: 36fb8a5588321426ccae7d6c6577fb4b48f3a4db
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948911"
---
# <a name="user-defined-functions-client-library-reference"></a>Klient biblioteks referens för användardefinierade funktioner

Det här dokumentet innehåller referensinformation för klient biblioteket Azure Digitals med användar definierade funktioner.

## <a name="helper-methods"></a>Hjälp metoder

Klient biblioteket definierar hjälp metoder för ofta använda åtgärder.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (ID) ⇒ `space`

Med en utrymmes identifierare hämtar den här funktionen utrymmet från grafen.

**Typ**: global funktion

| Parameter  | type                | Description  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Utrymmes identifierare |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata (ID) ⇒ `sensor`

Med den här funktionen hämtar sensor identifieraren från grafen.

**Typ**: global funktion

| Parameter  | type                | Description  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Sensor identifierare |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata (ID) ⇒ `device`

Med en enhets identifierare hämtar den här funktionen enheten från grafen.

**Typ**: global funktion

| Parameter  | type                | Description  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Enhets identifierare |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, datatyp) ⇒ `value`

Med den här funktionen hämtar sensor-ID och dess datatyp det aktuella värdet för sensorn.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Sensor identifierare |
| *dataType*  | `string` | Sensor data typ |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, valueName) ⇒ `value`

Med en utrymmes identifierare och värde namnet hämtar den här funktionen det aktuella värdet för egenskapen Space.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Utrymmes identifierare |
| *valueName* | `string` | Utrymmes egenskaps namn |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, datatyp) ⇒ `value[]`

Med den här funktionen hämtar sensor-ID och dess datatyp de historiska värdena för sensorn.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor identifierare |
| *dataType* | `string` | Sensor data typ |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, datatyp) ⇒ `value[]`

Med en blank stegs identifierare och värde namnet hämtar den här funktionen de historiska värdena för den egenskapen på utrymmet.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Utrymmes identifierare |
| *valueName* | `string` | Utrymmes egenskaps namn |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Med en utrymmes identifierare hämtar den här funktionen de underordnade utrymmena för det överordnade utrymmet.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Utrymmes identifierare |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceId) ⇒ `sensor[]`

Med en utrymmes identifierare hämtar den här funktionen de underordnade sensorerna för det överordnade utrymmet.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Utrymmes identifierare |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices (spaceId) ⇒ `device[]`

Med en utrymmes identifierare hämtar den här funktionen de underordnade enheterna för det överordnade utrymmet.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Utrymmes identifierare |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors (deviceId) ⇒ `sensor[]`

Med en enhets identifierare hämtar den här funktionen de underordnade sensorer för den överordnade enheten.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Enhets identifierare |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Med en utrymmes identifierare hämtar den här funktionen sitt överordnade utrymme.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Utrymmes identifierare |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace (childSensorId) ⇒ `space`

Med en sensor identifierare hämtar den här funktionen sitt överordnade utrymme.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor identifierare |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (childDeviceId) ⇒ `space`

Baserat på en enhets identifierare hämtar den här funktionen sitt överordnade utrymme.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Enhets identifierare |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice (childSensorId) ⇒ `space`

Med en sensor identifierare hämtar den här funktionen sin överordnade enhet.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensor identifierare |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName) ⇒ `extendedProperty`

Med en utrymmes identifierare hämtar den här funktionen egenskapen och dess värde från utrymmet.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Utrymmes identifierare |
| *Namn* | `string` | Utrymmes egenskaps namn |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName) ⇒ `extendedProperty`

Med en sensor identifierare hämtar den här funktionen egenskapen och dess värde från sensorn.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor identifierare |
| *Namn* | `string` | Namn på sensor egenskap |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (deviceId, propertyName) ⇒ `extendedProperty`

Baserat på en enhets identifierare hämtar den här funktionen egenskapen och dess värde från enheten.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Enhets identifierare |
| *Namn* | `string` | Enhetens egenskaps namn |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, datatyp, värde)

Den här funktionen anger ett värde för objektet sensor med den aktuella data typen.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Sensor identifierare |
| *dataType*  | `string` | Sensor data typ |
| *värde*  | `string` | Value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, datatyp, värde)

Den här funktionen anger ett värde för objektet Space med den aktuella data typen.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Utrymmes identifierare |
| *dataType* | `string` | Datatyp |
| *värde* | `string` | Value |

### <a name="logmessage"></a>logg (meddelande)

Den här funktionen loggar följande meddelande i den användardefinierade funktionen.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *meddelande* | `string` | Meddelande som ska loggas |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, nytto Last)

Den här funktionen skickar ett anpassat meddelande till skickas.

**Typ**: global funktion

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Diagram objekt identifierare. Exempel är utrymme, sensor och enhets-ID.|
| *topologyObjectType*  | `string` | Exempel är sensor och enhet.|
| *innehållet*  | `string` | Den JSON-nyttolast som ska skickas med meddelandet. |

## <a name="return-types"></a>Retur typer

De svars modeller som returneras från klient referensens hjälp metoder beskrivs nedan.

### <a name="space"></a>Blanksteg

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

### <a name="space-methods"></a>Utrymmes metoder

#### <a name="parent--space"></a>Överordnad () ⇒ `space`

Den här funktionen returnerar det överordnade utrymmet för det aktuella utrymmet.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Den här funktionen returnerar de underordnade sensorer för det aktuella utrymmet.

#### <a name="childdevices--device"></a>ChildDevices () ⇒ `device[]`

Den här funktionen returnerar de underordnade enheterna för det aktuella utrymmet.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för det aktuella utrymmet.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *Namn* | `string` | Namn på utökad egenskap |

#### <a name="valuevaluename--value"></a>Värde (valueName) ⇒ `value`

Den här funktionen returnerar värdet för det aktuella utrymmet.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Namn på värdet |

#### <a name="historyvaluename--value"></a>Historik (valueName) ⇒ `value[]`

Den här funktionen returnerar de historiska värdena för det aktuella utrymmet.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Namn på värdet |

#### <a name="notifypayload"></a>Meddela (nytto Last)

Den här funktionen skickar ett meddelande till den angivna nytto lasten.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *innehållet* | `string` | JSON-nyttolast som ska inkluderas i meddelandet |

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

#### <a name="parent--space"></a>Överordnad () ⇒ `space`

Den här funktionen returnerar det överordnade utrymmet för den aktuella enheten.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Den här funktionen returnerar den aktuella enhetens underordnade sensorer.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för den aktuella enheten.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *Namn* | `string` | Namn på utökad egenskap |

#### <a name="notifypayload"></a>Meddela (nytto Last)

Den här funktionen skickar ett meddelande till den angivna nytto lasten.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *innehållet* | `string` | JSON-nyttolast som ska inkluderas i meddelandet |

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

### <a name="sensor-methods"></a>Sensor metoder

#### <a name="space--space"></a>Utrymme () ⇒ `space`

Den här funktionen returnerar den aktuella sensorns överordnade utrymme.

#### <a name="device--device"></a>Enhet () ⇒ `device`

Den här funktionen returnerar den överordnade enheten för den aktuella sensorn.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för den aktuella sensorn.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *Namn* | `string` | Namn på utökad egenskap |

#### <a name="value--value"></a>Värde () ⇒ `value`

Den här funktionen returnerar värdet för den aktuella sensorn.

#### <a name="history--value"></a>Historik () ⇒ `value[]`

Den här funktionen returnerar de historiska värdena för den aktuella sensorn.

#### <a name="notifypayload"></a>Meddela (nytto Last)

Den här funktionen skickar ett meddelande till den angivna nytto lasten.

| Parameter  | type                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *innehållet* | `string` | JSON-nyttolast som ska inkluderas i meddelandet |

### <a name="value"></a>Value

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

- Lär dig mer om [Azure Digitals dubbla användar definierade funktioner](./concepts-user-defined-functions.md).

- Lär dig [hur du skapar användardefinierade funktioner](./how-to-user-defined-functions.md).

- Lär dig [hur du felsöker användardefinierade funktioner](./how-to-diagnose-user-defined-functions.md).
