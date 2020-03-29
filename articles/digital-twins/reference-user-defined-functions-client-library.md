---
title: Användardefinierad funktion klientbibliotek referens - Azure Digital Twins | Microsoft-dokument
description: Azure Digital Twins användardefinierade funktioner klientbibliotek referensdokumentation.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276818"
---
# <a name="user-defined-functions-client-library-reference"></a>Användardefinierad funktionsreferens för klientbibliotek

Det här dokumentet innehåller referensinformation för klientbiblioteket för Azure Digital Twins användardefinierade funktioner.

## <a name="helper-methods"></a>Hjälpmetoder

Klientbiblioteket definierar hjälpmetoder för vanliga åtgärder.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒`space`

Med en utrymmesidentifierare hämtar den här funktionen utrymmet från diagrammet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ---------- | ------------------- | ------------ |
| *Id*  | `guid` | Utrymmesidentifierare |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒`sensor`

Med tanke på en sensoridentifierare hämtar denna funktion sensorn från diagrammet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ---------- | ------------------- | ------------ |
| *Id*  | `guid` | Sensoridentifierare |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒`device`

Med en enhetsidentifierare hämtar den här funktionen enheten från diagrammet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *Id* | `guid` | Enhetsidentifierare |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒`value`

Med tanke på en sensoridentifierare och dess datatyp hämtar den här funktionen det aktuella värdet för den sensorn.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId (olikartade)*  | `guid` | Sensoridentifierare |
| *Datatyp*  | `string` | Sensordatatyp |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒`value`

Med en utrymmesidentifierare och värdenamnet hämtar den här funktionen det aktuella värdet för den utrymmesegenskapen.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId (avstånd)*  | `guid` | Utrymmesidentifierare |
| *valueName* | `string` | Namn på utrymmesegenskap |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒`value[]`

Med tanke på en sensoridentifierare och dess datatyp hämtar den här funktionen de historiska värdena för den sensorn.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId (olikartade)* | `guid` | Sensoridentifierare |
| *Datatyp* | `string` | Sensordatatyp |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒`value[]`

Med en utrymmeidentifierare och värdenamnet hämtar den här funktionen de historiska värdena för egenskapen i utrymmet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId (avstånd)* | `guid` | Utrymmesidentifierare |
| *valueName* | `string` | Namn på utrymmesegenskap |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒`space[]`

Med tanke på en utrymmesidentifierare hämtar den här funktionen de underordnade utrymmena för det överordnade utrymmet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId (avstånd)* | `guid` | Utrymmesidentifierare |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒`sensor[]`

Med en utrymmesidentifierare hämtar den här funktionen de underordnade sensorerna för det överordnade utrymmet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId (avstånd)* | `guid` | Utrymmesidentifierare |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒`device[]`

Med tanke på en utrymmesidentifierare hämtar den här funktionen de underordnade enheterna för det överordnade utrymmet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId (avstånd)* | `guid` | Utrymmesidentifierare |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒`sensor[]`

Med en enhetsidentifierare hämtar den här funktionen de underordnade sensorerna för den överordnade enheten.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Enhetsidentifierare |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒`space`

Med tanke på en utrymmesidentifierare hämtar den här funktionen det överordnade utrymmet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Utrymmesidentifierare |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒`space`

Med tanke på en sensoridentifierare hämtar den här funktionen dess överordnade utrymme.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensoridentifierare |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (childDeviceId) ⇒`space`

Med tanke på en enhetsidentifierare hämtar den här funktionen det överordnade utrymmet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Enhetsidentifierare |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒`space`

Med tanke på en sensoridentifierare hämtar den här funktionen den överordnade enheten.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Sensoridentifierare |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName) ⇒`extendedProperty`

Med tanke på en utrymmesidentifierare hämtar den här funktionen egenskapen och dess värde från utrymmet.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId (avstånd)* | `guid` | Utrymmesidentifierare |
| *egenskapName (propertyName)* | `string` | Namn på utrymmesegenskap |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName) ⇒`extendedProperty`

Med tanke på en sensoridentifierare hämtar den här funktionen egenskapen och dess värde från sensorn.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId (olikartade)* | `guid` | Sensoridentifierare |
| *egenskapName (propertyName)* | `string` | Namn på sensoregenskap |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒`extendedProperty`

Med en enhetsidentifierare hämtar den här funktionen egenskapen och dess värde från enheten.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Enhetsidentifierare |
| *egenskapName (propertyName)* | `string` | Namn på enhetsegenskap |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, värde)

Den här funktionen anger ett värde på sensorobjektet med den angivna datatypen.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId (olikartade)* | `guid` | Sensoridentifierare |
| *Datatyp*  | `string` | Sensordatatyp |
| *värde*  | `string` | Värde |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, värde)

Den här funktionen anger ett värde för utrymmesobjektet med den angivna datatypen.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId (avstånd)* | `guid` | Utrymmesidentifierare |
| *Datatyp* | `string` | Datatyp |
| *värde* | `string` | Värde |

### <a name="logmessage"></a>log(meddelande)

Den här funktionen loggar följande meddelande i den användardefinierade funktionen.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *Meddelande* | `string` | Meddelande som ska loggas |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologiObjectId, topologiObjectType, nyttolast)

Den här funktionen skickar ett anpassat meddelande ut för att skickas.

**Sort**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *topologiObjectId*  | `guid` | Grafobjektidentifierare. Exempel är utrymme, sensor och enhets-ID.|
| *topologiObjectType*  | `string` | Exempel är sensor och enhet.|
| *payload*  | `string` | Den JSON nyttolast som ska skickas med anmälan. |

## <a name="return-types"></a>Returtyper

Svarsmodellerna som returneras från klientreferenshjälpmetoder beskrivs nedan.

### <a name="space"></a>Space

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

### <a name="space-methods"></a>Utrymmesmetoder

#### <a name="parent--space"></a>Förälder() ⇒`space`

Den här funktionen returnerar det överordnade utrymmet för det aktuella blanksteget.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒`sensor[]`

Den här funktionen returnerar de underordnade sensorerna i det aktuella utrymmet.

#### <a name="childdevices--device"></a>ChildDevices() ⇒`device[]`

Den här funktionen returnerar de underordnade enheterna i det aktuella utrymmet.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för det aktuella utrymmet.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *egenskapName (propertyName)* | `string` | Namnet på den utökade egenskapen |

#### <a name="valuevaluename--value"></a>Värde(valueName) ⇒`value`

Den här funktionen returnerar värdet för det aktuella blanksteget.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Värdets namn |

#### <a name="historyvaluename--value"></a>Historia(valueName) ⇒`value[]`

Den här funktionen returnerar de historiska värdena för det aktuella blanksteget.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Värdets namn |

#### <a name="notifypayload"></a>Meddela(nyttolast)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON nyttolast som ska ingå i anmälan |

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

#### <a name="parent--space"></a>Förälder() ⇒`space`

Den här funktionen returnerar det överordnade utrymmet för den aktuella enheten.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒`sensor[]`

Den här funktionen returnerar den aktuella enhetens underordnade sensorer.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för den aktuella enheten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *egenskapName (propertyName)* | `string` | Namnet på den utökade egenskapen |

#### <a name="notifypayload"></a>Meddela(nyttolast)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON nyttolast som ska ingå i anmälan |

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

### <a name="sensor-methods"></a>Sensormetoder

#### <a name="space--space"></a>Space() ⇒`space`

Den här funktionen returnerar det överordnade utrymmet för den aktuella sensorn.

#### <a name="device--device"></a>Enhet() ⇒`device`

Den här funktionen returnerar den aktuella sensorns överordnade enhet.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för den aktuella sensorn.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *egenskapName (propertyName)* | `string` | Namnet på den utökade egenskapen |

#### <a name="value--value"></a>Värde() ⇒`value`

Den här funktionen returnerar värdet för den aktuella sensorn.

#### <a name="history--value"></a>Historia() ⇒`value[]`

Den här funktionen returnerar de aktuella sensorns historiska värden.

#### <a name="notifypayload"></a>Meddela(nyttolast)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON nyttolast som ska ingå i anmälan |

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

- Lär dig mer om [användardefinierade Azure Digital Twins-funktioner](./concepts-user-defined-functions.md).

- Lär dig hur du [skapar användardefinierade funktioner](./how-to-user-defined-functions.md).

- Lär dig hur du [felsöker användardefinierade funktioner](./how-to-diagnose-user-defined-functions.md).
