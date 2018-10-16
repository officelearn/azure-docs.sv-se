---
title: Hur du använder användardefinierade funktioner i Azure Digital Twins | Microsoft Docs
description: Riktlinjer för hur du skapar användardefinierade funktioner, matchers och rolltilldelningar med Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 7fbaff5ed1b60a4434ba2eb0c78c6aa1f3fd6645
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324341"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Hur du använder användardefinierade funktioner i Azure Digital Twins

[Användardefinierade funktioner](./concepts-user-defined-functions.md) gör att användaren kan köra anpassad logik mot inkommande telemetrimeddelanden och rumsliga graph metadata, så att användaren att skicka händelser till fördefinierade slutpunkter. I den här handboken vi gå igenom ett exempel på agerar på temperatur händelser för att identifiera och Avisera om alla läsning som överskrider en viss temperatur.

I exemplen nedan `https://yourManagementApiUrl` refererar till URI: N digitala Twins API: er:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourInstanceName` | Namnet på din digitala Twins för Azure-instans |
| `yourLocation` | Vilken server-region som din instans är värd för |

## <a name="client-library-reference"></a>-Klientbiblioteksreferens

De funktioner som är tillgängliga som hjälpmetoder i användardefinierade funktioner runtime räknas upp i följande [referens för klienthantering](#Client-Reference).

## <a name="create-a-matcher"></a>Skapa en matcher

Matchers är graph-objekt, som avgör vilka användardefinierade funktioner körs för en viss telemetri-meddelande.

Giltigt matcher villkor jämförelser:

- `Equals`
- `NotEquals`
- `Contains`

Giltigt matcher villkor mål:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

Följande exempel matcher ska utvärderas till SANT på en sensor telemetri händelse med `Temperature` som sitt värde för typ av data. Du kan skapa flera matchers på en användardefinierad funktion.

```text
POST https://yourManagementApiUrl/api/v1.0/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "yourSpaceIdentifier"
}
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourManagementApiUrl` | Den fullständiga URL-sökvägen för API Management  |
| `yourSpaceIdentifier` | Vilken server-region som din instans är värd för |

## <a name="create-a-user-defined-function-udf"></a>Skapa en användardefinierad funktion (UDF)

När matchers har skapats, ladda upp i funktionen kodfragment med följande POST anrop:

> [!IMPORTANT]
> - Ange följande i rubrikerna, `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Brödtexten är flera delar:
>   - Den första delen är om metadata som behövs för UDF.
>   - Den andra delen är javascript-beräkning logik.
> - Ersätt i `userDefinedBoundary` avsnittet `SpaceId` och `Machers` GUID.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourManagementApiUrl` | Den fullständiga URL-sökvägen för API Management  |

Brödtext:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourSpaceIdentifier` | Identifieraren utrymme  |
| `yourMatcherIdentifier` | Id för matcher som du vill använda |

### <a name="example-functions"></a>Exempel-funktioner

Ange sensor telemetri läser direkt för sensorn med datatypen `Temperature`, vilket är sensorn. Datatyp:

```javascript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Logga ett meddelande om sensor telemetri läsning överskrider ett fördefinierat tröskelvärde. Om din diagnostikinställningar har aktiverats på den digitala Twins-instansen, vidarebefordras loggar från användardefinierade funktioner:

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Följande kod ska utlösa en avisering om nivån temperaturen överstiger fördefinierade konstant.

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

För en mer komplex UDF-kodexempel som avser [Kontrollera tillgängliga blankstegen med ny air UDF](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)

## <a name="create-a-role-assignment"></a>Skapa en rolltilldelning

Vi måste du skapa en rolltilldelning för den användardefinierade funktionen köras under. Om vi inte har inte behörighet att interagera med Management-API för att utföra åtgärder på graph-objekt. De åtgärder som utförs av den användardefinierade funktionen är inte undantagna från rollbaserad åtkomstkontroll inom Digital Twins Management API: erna. De kan vara begränsad räckvidd genom att ange vissa roller eller vissa sökvägar för kontroll av åtkomst. Mer information finns i [rollbaserad åtkomstkontroll](./security-role-based-access-control.md) dokumentation.

- Fråga efter roller och hämta ID för den roll som du vill tilldela till UDF; Skicka den till RoleId nedan.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourManagementApiUrl` | Den fullständiga URL-sökvägen för API Management  |

- ObjectId kommer att UDF-ID som du skapade tidigare
- Hitta `Path` genom att fråga blankstegen med sina fullständiga sökvägen till och kopiera den `spacePaths` värde. Klistra in det i sökvägen nedan när du skapar UDF-rolltilldelning

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourManagementApiUrl` | Den fullständiga URL-sökvägen för API Management  |
| `yourSpaceName` | Namnet på det utrymme som du vill använda |

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourManagementApiUrl` | Den fullständiga URL-sökvägen för API Management  |
| `yourDesiredRoleIdentifier` | Identifierare för rollen |
| `yourUserDefinedFunctionId` | Id för en användardefinierad funktion som du vill använda |
| `yourAccessControlPath` | Åtkomstväg för kontroll |

## <a name="send-telemetry-to-be-processed"></a>Skicka telemetri som ska bearbetas

Telemetri som genereras av sensor som beskrivs i diagrammet ska utlösa körningen av en användardefinierad funktion som har överförts. När telemetri som hämtas av registerförare, skapas en Körningsplan för anrop av den användardefinierade funktionen.

1. Hämta matchers för sensorn medan läsningen genererades ut från.
1. Beroende på vad matchers utvärderades korrekt, att hämta associerade användardefinierade funktioner.
1. Kör varje användardefinierad funktion.

## <a name="client-reference"></a>Referens för klienthantering

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Beroende på ett utrymme-ID, hämtar du området från diagrammet.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| id  | `guid` | utrymmesidentifierare |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Beroende på en sensor-ID, hämtar du sensorn från diagrammet.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| id  | `guid` | sensorn identifierare |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Beroende på en enhets-ID, hämtar du enheten från diagrammet.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| id  | `guid` | Enhets-ID |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, dataType) ⇒ `value`

Baserat på en sensor-identifierare och dess datatyp, för att hämta det aktuella värdet för den sensorn.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | sensorn identifierare |
| Datatyp  | `string` | sensorn datatyp |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, värdenamn) ⇒ `value`

Baserat på en utrymmesidentifierare och värdenamn, för att hämta det aktuella värdet för egenskapen utrymme.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | utrymmesidentifierare |
| Värdenamn  | `string` | egenskapsnamn med blanksteg |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, dataType) ⇒ `value[]`

Baserat på en sensor-identifierare och dess datatyp, hämta historiska värden för den sensorn.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | sensorn identifierare |
| Datatyp  | `string` | sensorn datatyp |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, dataType) ⇒ `value[]`

Baserat på en utrymmesidentifierare och värdenamn, hämta historiska värden för den egenskapen på området.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | utrymmesidentifierare |
| Värdenamn  | `string` | egenskapsnamn med blanksteg |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Med en utrymmesidentifierare kan hämta de underordnade adressutrymmena för det överordnade området.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | utrymmesidentifierare |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Med en utrymmesidentifierare kan hämta underordnade sensorer för det överordnade området.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | utrymmesidentifierare |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Med en utrymmesidentifierare kan hämta de underordnade enheterna för det överordnade området.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | utrymmesidentifierare |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Med en enhets-ID kan hämta de underordnade sensorerna för den överordnade enheten.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | Enhets-ID |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Med en utrymmesidentifierare kan hämta dess överordnade utrymme.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| childSpaceId  | `guid` | utrymmesidentifierare |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Med en sensor identifierare kan hämta dess överordnade utrymme.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | sensorn identifierare |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Med en enhets-ID kan hämta dess överordnade utrymme.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| childDeviceId  | `guid` | Enhets-ID |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Med en sensor identifierare kan hämta den överordnade enheten.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | sensorn identifierare |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName) ⇒ `extendedProperty`

Med en utrymmesidentifierare kan hämta egenskapen och dess värde från området.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | utrymmesidentifierare |
| propertyName  | `string` | egenskapsnamn med blanksteg |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName) ⇒ `extendedProperty`

Med en sensor identifierare kan hämta egenskapen och dess värde från sensorn.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | sensorn identifierare |
| propertyName  | `string` | Egenskapen sensornamnet |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (deviceId, propertyName) ⇒ `extendedProperty`

Med en enhets-ID kan hämta egenskapen och dess värde från enheten.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | Enhets-ID |
| propertyName  | `string` | Egenskapen enhetsnamn |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, datatyp, värde)

Anger ett värde på sensor-objektet med den angivna datatypen.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | sensorn identifierare |
| Datatyp  | `string` | sensorn datatyp |
| värde  | `string` | värde |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, datatyp, värde)

Anger ett värde på utrymme-objektet med den angivna datatypen.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | utrymmesidentifierare |
| Datatyp  | `string` | datatyp |
| värde  | `string` | värde |

### <a name="logmessage"></a>log(Message)

Loggar följande meddelande i den användardefinierade funktionen.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| meddelande  | `string` | meddelandet som ska loggas |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, nyttolast)

Skickas ett anpassat meddelande skickas.

**Typ**: global funktion

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| topologyObjectId  | `Guid` | Graph objektidentifierare (ex.) utrymme / sensor /device id)|
| topologyObjectType  | `string` | (ex.) utrymme / sensor / enhet)|
| nyttolast  | `string` | json-nyttolasten skickas med meddelandet |

## <a name="return-types"></a>Returnera typer

Följande är modeller som beskriver returobjekt från ovan referens för klienthantering.

### <a name="space"></a>Rymd

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Utrymme metoder

#### <a name="parent--space"></a>Parent() ⇒ `space`

Returnerar det överordnade utrymmet på utrymmet som.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Returnerar underordnat sensorer aktuella utrymme.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Returnerar underordnat enheter på det aktuella utrymmet.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Returnerar den utökade egenskapen och dess värde för det aktuella utrymmet.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | namnet på den utökade egenskapen |

#### <a name="valuevaluename--value"></a>Value(ValueName) ⇒ `value`

Returnerar värdet för det aktuella utrymmet.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| Värdenamn | `string` | namnet på värdet |

#### <a name="historyvaluename--value"></a>History(ValueName) ⇒ `value[]`

Returnerar de historiska värdena för det aktuella utrymmet.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| Värdenamn | `string` | namnet på värdet |

#### <a name="notifypayload"></a>Notify(Payload)

Skickar ett meddelande med den angivna nyttolasten.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| nyttolast | `string` | JSON-nyttolast ska inkluderas i meddelandet |

### <a name="device"></a>Enhet

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Enhetsmetoder

#### <a name="parent--space"></a>Parent() ⇒ `space`

Returnerar det överordnade utrymmet på den aktuella enheten.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Returnerar underordnat sensorer för den aktuella enheten.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Returnerar den utökade egenskapen och dess värde för den aktuella enheten.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | namnet på den utökade egenskapen |

#### <a name="notifypayload"></a>Notify(Payload)

Skickar ett meddelande med den angivna nyttolasten.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| nyttolast | `string` | JSON-nyttolast ska inkluderas i meddelandet |

### <a name="sensor"></a>Sensor

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Sensorn metoder

#### <a name="space--space"></a>Space() ⇒ `space`

Returnerar det överordnade utrymmet på den aktuella sensorn.

#### <a name="device--device"></a>Device() ⇒ `device`

Returnerar den överordnade enheten för den aktuella sensorn.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Returnerar den utökade egenskapen och dess värde för den aktuella sensorn.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | namnet på den utökade egenskapen |

#### <a name="value--value"></a>Value() ⇒ `value`

Returnerar värdet för den aktuella sensorn.

#### <a name="history--value"></a>History() ⇒ `value[]`

Returnerar de historiska värdena för den aktuella sensorn.

#### <a name="notifypayload"></a>Notify(Payload)

Skickar ett meddelande med den angivna nyttolasten.

| Param  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| nyttolast | `string` | JSON-nyttolast ska inkluderas i meddelandet |

### <a name="value"></a>Värde

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Utökad egenskap

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Nästa steg

Läs hur du skapar digitala Twins slutpunkter för att skicka händelser till [skapa digitala Twins slutpunkter](how-to-egress-endpoints.md).

Mer information på digitala Twins slutpunkter [Läs mer om slutpunkter](concepts-events-routing.md).
