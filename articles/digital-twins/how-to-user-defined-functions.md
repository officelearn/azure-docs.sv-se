---
title: Så här skapar du användardefinierade funktioner i Azure Digital Twins | Microsoft Docs
description: Så här att skapa användardefinierade funktioner, matchers och rolltilldelningar i Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 7208f96d99127247b51510e0c43c1733bb327dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921897"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Så här skapar du användardefinierade funktioner i Azure Digital Twins

[Användardefinierade funktioner](./concepts-user-defined-functions.md) användarna kan konfigurera anpassad logik som ska köras från inkommande telemetrimeddelanden och rumsliga graph metadata. Användare kan även skicka händelser till fördefinierade [slutpunkter](./how-to-egress-endpoints.md).

Den här guiden går igenom ett exempel som visar hur du identifierar och Avisera om alla läsning som överskrider en viss temperatur från mottagna temperatur händelser.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>-Klientbiblioteksreferens

Funktioner som är tillgängliga som hjälpmetoder i användardefinierade funktioner runtime finns i den [-klientbiblioteksreferens](./reference-user-defined-functions-client-library.md) dokumentet.

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

Följande exempel matcher utvärderas till true på en sensor telemetri händelse med `"Temperature"` som sitt värde för typ av data. Du kan skapa flera matchers på en användardefinierad funktion genom att göra en autentiserad HTTP POST-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

Med JSON-texten:

```JSON
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

| Värde | Ersätt med |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Den serverregion som instansen finns i |

## <a name="create-a-user-defined-function"></a>Skapa en användardefinierad funktion

Skapa en användardefinierad funktion måste du göra en multipart HTTP-begäran till Azure Digital Twins Management API: erna.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Ladda upp i funktionen kodfragment med följande autentiserade multipart HTTP POST-begäran till när matchers har skapats:

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Använd följande text:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Värde | Ersätt med |
| --- | --- |
| USER_DEFINED_BOUNDARY | Ett namn i flera delar innehåll gräns |
| YOUR_SPACE_IDENTIFIER | Identifieraren utrymme  |
| YOUR_MATCHER_IDENTIFIER | ID för matcher som du vill använda |

1. Kontrollera att rubrikerna som inkluderar: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Kontrollera att brödtexten är flera delar:

   - Den första delen innehåller metadata som krävs användardefinierad funktion.
   - Den andra delen innehåller logik för JavaScript-beräkning.

1. I den **USER_DEFINED_BOUNDARY** avsnittet, ersätter den **spaceId** (`YOUR_SPACE_IDENTIFIER`) och **matchers** (`YOUR_MATCHER_IDENTIFIER`) värden.
1. Kontrollera att användardefinierade JavaScript-funktion har angetts som `Content-Type: text/javascript`.

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

Ett mer komplext kodexempel användardefinierad funktion finns i [användandet Snabbstart](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Skapa en rolltilldelning

Skapa en rolltilldelning för den användardefinierade funktionen ska köras. Om det finns ingen rolltilldelning för användardefinierad funktion, inte den behörighet att interagera med API Management eller behörighet att utföra åtgärder på graph-objekt. Åtgärder som kan utföra en användardefinierad funktion anges och definieras via rollbaserad åtkomstkontroll i Azure Digital Twins Management API: erna. Till exempel kan användardefinierade funktioner vara begränsad räckvidd genom att ange vissa roller eller vissa sökvägar för kontroll av åtkomst. Mer information finns i den [rollbaserad åtkomstkontroll](./security-role-based-access-control.md) dokumentation.

1. [Fråga System-API: N](./security-create-manage-role-assignments.md#all) för alla roller att hämta roll-ID som du vill tilldela till din användardefinierad funktion. Gör du genom att göra en autentiserad HTTP GET-begäran till:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Behåll den önskade roll-ID. Det kommer att skickas som JSON brödtext attribut **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) nedan.

1. **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) blir den användardefinierade funktionen-ID som du skapade tidigare.
1. Hitta värdet för **sökväg** (`YOUR_ACCESS_CONTROL_PATH`) genom att fråga din blankstegen med `fullpath`.
1. Kopiera den returnerade `spacePaths` värde. Du kommer att använda som nedan. Gör en autentiserad HTTP GET-begäran till:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_SPACE_NAME | Namnet på det utrymme som du vill använda |

1. Klistra in den returnerade `spacePaths` värde i **sökväg** att skapa en användardefinierad funktion rolltilldelning genom att göra en autentiserad HTTP POST-begäran till:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Med JSON-texten:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identifierare för rollen |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID för den användardefinierade funktionen som du vill använda |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Det ID som anger vilken användardefinierad funktion |
    | YOUR_ACCESS_CONTROL_PATH | Åtkomstväg för kontroll |

>[!TIP]
> Läs artikeln [skapa och hantera rolltilldelningar](./security-create-manage-role-assignments.md) för mer information om användardefinierade funktionen Management API-åtgärder och slutpunkter.

## <a name="send-telemetry-to-be-processed"></a>Skicka telemetri som ska bearbetas

Sensorn som definierats i spatial intelligence diagrammet skickar telemetri. I sin tur utlöser telemetri som körningen av en användardefinierad funktion som har överförts. Registerförare hämtar telemetri. Sedan skapas en Körningsplan för anrop av den användardefinierade funktionen.

1. Hämta matchers för sensorn medan läsningen har genererats från.
1. Beroende på vilka matchers utvärderades korrekt, att hämta associerade användardefinierade funktioner.
1. Kör varje användardefinierad funktion.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa slutpunkter för Azure Digital Twins](./how-to-egress-endpoints.md) att skicka händelser till.

- Mer information om routning i Azure Digital Twins [routning händelser och meddelanden](./concepts-events-routing.md).

- Granska den [referensdokumentation för klienten biblioteket](./reference-user-defined-functions-client-library.md).
