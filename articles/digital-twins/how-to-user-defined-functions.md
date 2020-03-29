---
title: Så här skapar du användardefinierade funktioner – i Azure Digital Twins | Microsoft-dokument
description: Så här skapar du användardefinierade funktioner, matchningar och rolltilldelningar i Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276942"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Så här skapar du användardefinierade funktioner i Azure Digital Twins

[Användardefinierade funktioner](./concepts-user-defined-functions.md) gör det möjligt för användare att konfigurera anpassad logik som ska köras från inkommande telemetrimeddelanden och rumsliga grafmetadata. Användare kan också skicka händelser till fördefinierade [slutpunkter](./how-to-egress-endpoints.md).

Den här guiden går igenom ett exempel som visar hur du upptäcker och varnar vid en avläsning som överskrider en viss temperatur från mottagna temperaturhändelser.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Referens för klientbibliotek

Funktioner som är tillgängliga som hjälpmetoder i den användardefinierade funktionskörningen visas i [klientbibliotekets referensdokument.](./reference-user-defined-functions-client-library.md)

## <a name="create-a-matcher"></a>Skapa en matchning

Matchningar är diagramobjekt som avgör vilka användardefinierade funktioner som körs för ett visst telemetrimeddelande.

- Giltiga jämförelser av matchningsvillkor:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Giltiga matchningsvillkorsmål:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Följande exempel matchningar utvärderas till true på `"Temperature"` alla sensortelemetrihändelse med som datatypsvärde. Du kan skapa flera matchningar på en användardefinierad funktion genom att göra en autentiserat HTTP POST-begäran till:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Med JSON kropp:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Värde | Ersätt med |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Den serverregion som instansen finns i |

## <a name="create-a-user-defined-function"></a>Skapa en användardefinierad funktion

Att skapa en användardefinierad funktion innebär att du gör en HTTP-begäran för flera delar till Azure Digital Twins Management API:er.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

När matchningsmatcherna har skapats laddar du upp funktionskodavsnittet med följande autentiserade HTTP POST-begäran i flera delar till:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Använd följande kropp:

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
| USER_DEFINED_BOUNDARY | Ett gränsnamn för flera delar |
| YOUR_SPACE_IDENTIFIER | Utrymmesidentifieraren  |
| YOUR_MATCHER_IDENTIFIER | ID:t för de matcher som du vill använda |

1. Kontrollera att rubrikerna `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`innehåller: .
1. Kontrollera att kroppen är flera delar:

   - Den första delen innehåller de användardefinierade funktionsmetadata som krävs.
   - Den andra delen innehåller JavaScript-beräkningslogiken.

1. I avsnittet **USER_DEFINED_BOUNDARY** ersätter du **värdena spaceId** (`YOUR_SPACE_IDENTIFIER`) och **matchers** (`YOUR_MATCHER_IDENTIFIER`).
1. Kontrollera att javascript-användardefinierade funktionen `Content-Type: text/javascript`anges som .

### <a name="example-functions"></a>Exempel på funktioner

Ställ in sensortelemetriavläsningen direkt för sensorn `sensor.DataType`med datatypEn **Temperatur**, vilket är:

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

Telemetriparametern visar **attributen SensorId** och **Meddelande,** vilket motsvarar ett meddelande som skickas av en sensor. **telemetry** Parametern **executionContext** visar följande attribut:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

I nästa exempel loggar vi ett meddelande om sensorelemetriavläsningen överskrider en fördefinierad tröskel. Om dina diagnostikinställningar är aktiverade i Azure Digital Twins-instansen vidarebefordras även loggar från användardefinierade funktioner:

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

Följande kod utlöser ett meddelande om temperaturnivån stiger över den fördefinierade konstanten:

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

Ett mer komplext användardefinierat funktionskodexempel läser du [snabbstarten Beläggning](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Skapa en rolltilldelning

Skapa en rolltilldelning för den användardefinierade funktionen som ska köras under. Om det inte finns någon rolltilldelning för den användardefinierade funktionen har den inte rätt behörighet att interagera med hanterings-API:et eller ha åtkomst till att utföra åtgärder på diagramobjekt. Åtgärder som en användardefinierad funktion kan utföra anges och definieras via rollbaserad åtkomstkontroll i Azure Digital Twins Management API:er. Användardefinierade funktioner kan till exempel begränsas i omfånget genom att ange vissa roller eller vissa sökvägar för åtkomstkontroll. Mer information finns i dokumentationen [för rollbaserad åtkomstkontroll.](./security-role-based-access-control.md)

1. [Fråga system-API:et](./security-create-manage-role-assignments.md#retrieve-all-roles) för alla roller för att få det roll-ID som du vill tilldela din användardefinierade funktion. Gör det genom att göra en autentiserat HTTP GET-begäran till:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Behåll önskat roll-ID. Det kommer att skickas som JSON-brödtextatrollId ( **roleId** `YOUR_DESIRED_ROLE_IDENTIFIER`) nedan.

1. **objectId** `YOUR_USER_DEFINED_FUNCTION_ID`( ) blir det användardefinierade funktions-ID som skapades tidigare.
1. Hitta värdet för`YOUR_ACCESS_CONTROL_PATH` **sökvägen** ( ) `fullpath`genom att fråga dina blanksteg med .
1. Kopiera det `spacePaths` returnerade värdet. Du kommer att använda det nedan. Gör en autentrad HTTP GET-begäran till:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_SPACE_NAME | Namnet på det utrymme du vill använda |

1. Klistra in `spacePaths` det returnerade värdet i **sökvägen** för att skapa en användardefinierad funktionsrolltilldelning genom att göra en autentrad HTTP POST-begäran till:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Med JSON kropp:

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
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identifieraren för önskad roll |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID:t för den användardefinierade funktion som du vill använda |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID:t som anger den användardefinierade funktionstypen (`UserDefinedFunctionId`) |
    | YOUR_ACCESS_CONTROL_PATH | Sökvägen till åtkomstkontroll |

>[!TIP]
> Läs artikeln [Hur du skapar och hanterar rolltilldelningar](./security-create-manage-role-assignments.md) för mer information om användardefinierade API-åtgärder och slutpunkter för funktionshantering.

## <a name="send-telemetry-to-be-processed"></a>Skicka telemetri som ska bearbetas

Sensorn som definieras i den rumsliga intelligens grafen skickar telemetri. Telemetrin utlöser i sin tur körningen av den användardefinierade funktion som laddades upp. Dataprocessorn hämtar telemetrin. Sedan skapas en körningsplan för anrop av den användardefinierade funktionen.

1. Hämta matchningar för sensorn som avläsningen genererades från.
1. Beroende på vilka matchningar som utvärderades kan du hämta de associerade användardefinierade funktionerna.
1. Kör varje användardefinierad funktion.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar Azure Digital Twins slutpunkter](./how-to-egress-endpoints.md) för att skicka händelser till.

- Mer information om routning i Azure Digital Twins finns i [Routningshändelser och meddelanden](./concepts-events-routing.md).

- Granska [referensdokumentationen för klientbiblioteket](./reference-user-defined-functions-client-library.md).