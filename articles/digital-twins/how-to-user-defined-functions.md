---
title: Hur du använder användardefinierade funktioner i Azure Digital Twins | Microsoft Docs
description: Riktlinjer för hur du skapar användardefinierade funktioner och matchers rolltilldelningar med Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 33190472215e7a02b94951a73054ebe3e1994e54
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623918"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Hur du använder användardefinierade funktioner i Azure Digital Twins

[Användardefinierade funktioner](./concepts-user-defined-functions.md) (UDF) kan användaren köra anpassad logik mot inkommande telemetrimeddelanden och rumsliga graph metadata. Användaren kan sedan skicka händelser till fördefinierade slutpunkter. Den här guiden går igenom ett exempel på agerar på temperatur händelser för att identifiera och Avisera om alla läsning som överskrider en viss temperatur.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>-Klientbiblioteksreferens

De funktioner som är tillgängliga som hjälpmetoder i användardefinierade funktioner runtime finns i den [referens för klienthantering](#Client-Reference) avsnittet.

## <a name="create-a-matcher"></a>Skapa en matcher

Matchers är graph-objekt som avgör vad användardefinierade funktioner som ska köras för en viss telemetri-meddelande.

- Giltigt matcher villkor jämförelser:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Giltigt matcher villkor mål:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Följande exempel matcher utvärderas till true på en sensor telemetri händelse med `"Temperature"` som sitt värde för typ av data. Du kan skapa flera matchers på en användardefinierad funktion:

```plaintext
POST yourManagementApiUrl/matchers
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
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Ditt värde | Ersätt med |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Vilken server-region som din instans är värd för |

## <a name="create-a-user-defined-function-udf"></a>Skapa en användardefinierad funktion (UDF)

När matchers har skapats laddar du upp i funktionen kodfragment med följande **POST** anropa:

> [!IMPORTANT]
> - Ange följande i rubrikerna, `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Brödtexten är flera delar:
>   - Den första delen är om metadata som behövs för en användardefinierad funktion.
>   - Den andra delen är JavaScript-beräkning logik.
> - I den **userDefinedBoundary** avsnittet, ersätter den **SpaceId** och **Machers** värden.

```plaintext
POST yourManagementApiUrl/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Parametervärde | Ersätt med |
| --- | --- |
| *userDefinedBoundary* | Ett namn i flera delar innehåll gräns |

### <a name="body"></a>Innehåll

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Ditt värde | Ersätt med |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Identifieraren utrymme  |
| YOUR_MATCHER_IDENTIFIER | ID för matcher som du vill använda |

### <a name="example-functions"></a>Exempel-funktioner

Ange sensor telemetri läser direkt för sensorn med datatypen **temperatur**, vilket är `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Den **telemetri** parametern exponerar den **SensorId** och **meddelande** egenskaper som motsvarar ett meddelande som skickas av en sensor. Den **executionContext** parametern exponerar följande attribut:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

I nästa exempel logga vi ett meddelande om sensor telemetri läsning överskrider ett fördefinierat tröskelvärde. Om diagnostikinställningarna har aktiverats på den digitala Twins för Azure-instansen, vidarebefordras loggar från användardefinierade funktioner:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Följande kod utlöser en avisering om nivån temperaturen överstiger de fördefinierade Ständiga:

```JavaScript
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

För en mer komplex UDF-kodexemplet [Kontrollera tillgängliga blankstegen med en ny air UDF](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Skapa en rolltilldelning

Vi måste du skapa en rolltilldelning för den användardefinierade funktionen ska köras. Om vi inte behöver det inte rätt behörighet för att interagera med Management-API för att utföra åtgärder på graph-objekt. De åtgärder som utförs av den användardefinierade funktionen inte är undantagna från rollbaserad åtkomstkontroll i Azure Digital Twins Management API: erna. De kan vara begränsad räckvidd genom att ange vissa roller eller vissa sökvägar för kontroll av åtkomst. Mer information finns i [rollbaserad åtkomstkontroll](./security-role-based-access-control.md) dokumentation.

1. Fråga efter roller och hämta ID för den roll som du vill tilldela till en användardefinierad funktion. Skicka det till **RoleId**:

    ```plaintext
    GET yourManagementApiUrl/system/roles
    ```

1. **ObjectId** kommer att UDF-ID som du skapade tidigare.
1. Hitta värdet för **sökväg** genom att fråga din blankstegen med `fullpath`.
1. Kopiera den returnerade `spacePaths` värde. Du ska använda som i följande kod:

    ```plaintext
    GET yourManagementApiUrl/spaces?name=yourSpaceName&includes=fullpath
    ```

    | Parametervärde | Ersätt med |
    | --- | --- |
    | *yourSpaceName* | Namnet på det utrymme som du vill använda |

1. Klistra in den returnerade `spacePaths` värde i **sökväg** att skapa en rolltilldelning för UDF:

    ```plaintext
    POST yourManagementApiUrl/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Ditt värde | Ersätt med |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identifierare för rollen |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID för en användardefinierad funktion som du vill använda |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Det ID som anger vilken UDF |
    | YOUR_ACCESS_CONTROL_PATH | Åtkomstväg för kontroll |

## <a name="send-telemetry-to-be-processed"></a>Skicka telemetri som ska bearbetas

Telemetri som genereras av sensor som beskrivs i diagrammet utlöser körning av den användardefinierade funktionen som har överförts. Registerförare hämtar telemetri. Sedan skapas en kör plan för anrop av den användardefinierade funktionen.

1. Hämta matchers för sensorn medan läsningen genererades ut från.
1. Beroende på vad matchers utvärderades korrekt, att hämta associerade användardefinierade funktioner.
1. Kör varje användardefinierad funktion.

## <a name="client-reference"></a>Referens för klienthantering

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
| *Datatyp*  | `string` | sensorn datatyp |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, värdenamn) ⇒ `value`

Den här funktionen hämtar baserat på en utrymmesidentifierare och värdenamn, det aktuella värdet för egenskapen utrymme.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | utrymmesidentifierare |
| *Värdenamn* | `string` | egenskapsnamn med blanksteg |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, dataType) ⇒ `value[]`

Den här funktionen hämtar baserat på en sensor-identifierare och dess datatyp, historiska värden för den sensorn.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | sensorn identifierare |
| *Datatyp* | `string` | sensorn datatyp |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, dataType) ⇒ `value[]`

Den här funktionen hämtar baserat på en utrymmesidentifierare och värdenamn, historiska värden för den egenskapen på området.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |
| *Värdenamn* | `string` | egenskapsnamn med blanksteg |

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

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName) ⇒ `extendedProperty`

Den här funktionen hämtar med en utrymmesidentifierare kan egenskapen och dess värde från området.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |
| *propertyName* | `string` | egenskapsnamn med blanksteg |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName) ⇒ `extendedProperty`

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
| *Datatyp*  | `string` | sensorn datatyp |
| *värde*  | `string` | Värde |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, datatyp, värde)

Den här funktionen anger ett värde på utrymme-objektet med den angivna datatypen.

**Typ**: global funktion

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | utrymmesidentifierare |
| *Datatyp* | `string` | Datatyp |
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
| *nyttolast*  | `string` | JSON-nyttolasten skickas med meddelandet. |

## <a name="return-types"></a>Returnera typer

Följande modeller beskrivs returobjekt från föregående referens för klienthantering.

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
| *Värdenamn* | `string` | namnet på värdet |

#### <a name="historyvaluename--value"></a>History(ValueName) ⇒ `value[]`

Den här funktionen returnerar historiska värdena för det aktuella utrymmet.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *Värdenamn* | `string` | namnet på värdet |

#### <a name="notifypayload"></a>Notify(Payload)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *nyttolast* | `string` | JSON-nyttolast ska inkluderas i meddelandet |

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

Den här funktionen returnerar överordnade utrymme på den aktuella enheten.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Den här funktionen returnerar underordnade sensorer för den aktuella enheten.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Den här funktionen returnerar den utökade egenskapen och dess värde för den aktuella enheten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | namnet på den utökade egenskapen |

#### <a name="notifypayload"></a>Notify(Payload)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *nyttolast* | `string` | JSON-nyttolast ska inkluderas i meddelandet |

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

#### <a name="notifypayload"></a>Notify(Payload)

Den här funktionen skickar ett meddelande med den angivna nyttolasten.

| Parameter  | Typ                | Beskrivning  |
| ------ | ------------------- | ------------ |
| *nyttolast* | `string` | JSON-nyttolast ska inkluderas i meddelandet |

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

- Lär dig hur du [skapa slutpunkter för Azure Digital Twins](how-to-egress-endpoints.md) att skicka händelser till.

- Lär dig mer om Azure Digital Twins slutpunkter, [mer om slutpunkter](concepts-events-routing.md).
