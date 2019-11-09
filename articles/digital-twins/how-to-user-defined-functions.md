---
title: Så här skapar du användardefinierade funktioner i Azure Digitals flätar | Microsoft Docs
description: Så här skapar du användardefinierade funktioner,-matchningar och roll tilldelningar i digitala Azure-dubbla.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/07/2019
ms.custom: seodec18
ms.openlocfilehash: 5271b14ec008579d18a152a229b9768339927bb7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888850"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Så här skapar du användardefinierade funktioner i Azure Digitals dubbla

[Användardefinierade funktioner](./concepts-user-defined-functions.md) gör det möjligt för användare att konfigurera anpassad logik att köras från inkommande telemetridata och metadata för spatialdata. Användare kan även skicka händelser till fördefinierade [slut punkter](./how-to-egress-endpoints.md).

Den här guiden går igenom ett exempel som demonstrerar hur man identifierar och varnar för all läsning som överskrider en viss temperatur från mottagna temperatur händelser.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Klient biblioteks referens

Funktioner som är tillgängliga som hjälp metoder i körningen av användardefinierade funktioner visas i referens dokumentet för [klient bibliotek](./reference-user-defined-functions-client-library.md) .

## <a name="create-a-matcher"></a>Skapa en matchning

Motsvarigheter är graf-objekt som avgör vilka användardefinierade funktioner som körs för ett specifikt telemetri-meddelande.

- Giltiga villkors jämförelser för matchningar:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Giltiga villkors mål för matchning:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Följande exempel matchning utvärderar till sant för valfri sensor för telemetri-telemetri med `"Temperature"` som datatyp värde. Du kan skapa flera motsvarigheter i en användardefinierad funktion genom att göra en autentiserad HTTP POST-begäran till:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

Med JSON-brödtext:

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

Att skapa en användardefinierad funktion innebär att göra en multipart HTTP-begäran till Azure Digitals hanterings-API: er.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

När matcharna har skapats överför du funktions kodfragmentet med följande autentiserade multipart HTTP POST-begäran till:

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
| USER_DEFINED_BOUNDARY | Namn på en flerdelade innehålls gränser |
| YOUR_SPACE_IDENTIFIER | Utrymmes identifieraren  |
| YOUR_MATCHER_IDENTIFIER | ID: t för den matchning som du vill använda |

1. Kontrol lera att rubrikerna är: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Kontrol lera att texten är multipart:

   - Den första delen innehåller de användardefinierade metadata som krävs av användaren.
   - Den andra delen innehåller beräknings logiken för Java Script.

1. I avsnittet **USER_DEFINED_BOUNDARY** ersätter du värdena **spaceId** (`YOUR_SPACE_IDENTIFIER`) och **motsvarigheter** (`YOUR_MATCHER_IDENTIFIER`).
1. Kontrol lera att den användardefinierade JavaScript-funktionen anges som `Content-Type: text/javascript`.

### <a name="example-functions"></a>Exempel funktioner

Ange att sensor telemetri ska läsas direkt för sensorn med data typen **temperatur**, som är `sensor.DataType`:

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

Parametern **telemetri** visar **SensorId** och attribut för **meddelanden** som motsvarar ett meddelande som skickas av en sensor. **ExecutionContext** -parametern visar följande attribut:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

I nästa exempel loggar vi ett meddelande om att läsningen av sensor telemetri överskrider ett fördefinierat tröskelvärde. Om dina diagnostikinställningar är aktiverade på Azure Digitals-instansen vidarebefordras även loggar från användardefinierade funktioner:

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

Följande kod utlöser ett meddelande om temperatur nivån stiger över den fördefinierade konstanten:

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

Ett mer komplext användardefinierat funktions kod exempel finns i [snabb start](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)för användning.

## <a name="create-a-role-assignment"></a>Skapa en roll tilldelning

Skapa en roll tilldelning för den användardefinierade funktionen som ska köras under. Om det inte finns någon roll tilldelning för den användardefinierade funktionen har den inte tillräckliga behörigheter för att samverka med hanterings-API: et eller ha åtkomst för att utföra åtgärder på diagram objekt. Åtgärder som en användardefinierad funktion kan utföra anges och definieras via rollbaserad åtkomst kontroll i Azure Digitals hanterings-API: er. Användardefinierade funktioner kan till exempel begränsas i omfånget genom att ange vissa roller eller vissa Sök vägar för åtkomst kontroll. Mer information finns i dokumentationen för [rollbaserad åtkomst kontroll](./security-role-based-access-control.md) .

1. [Fråga system-API: et](./security-create-manage-role-assignments.md#retrieve-all-roles) för alla roller för att hämta det roll-ID som du vill tilldela till din användardefinierade funktion. Gör detta genom att göra en autentiserad HTTP GET-begäran till:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Behåll det önskade roll-ID: t. Den kommer att skickas som JSON Body-attributet **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) nedan.

1. **ObjectID** (`YOUR_USER_DEFINED_FUNCTION_ID`) är det användardefinierade funktions-ID som skapades tidigare.
1. Hitta värdet för **Path** (`YOUR_ACCESS_CONTROL_PATH`) genom att fråga dina Spaces med `fullpath`.
1. Kopiera det returnerade `spacePaths`-värdet. Du kommer att använda det nedan. Gör en autentiserad HTTP GET-begäran till:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Värde | Ersätt med |
    | --- | --- |
    | YOUR_SPACE_NAME | Namnet på det utrymme som du vill använda |

1. Klistra in det returnerade `spacePaths` svärdet i **sökvägen** för att skapa en användardefinierad funktions roll tilldelning genom att göra en autentiserad HTTP POST-begäran till:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Med JSON-brödtext:

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
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identifieraren för den önskade rollen |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID för den användardefinierade funktion som du vill använda |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Det ID som anger den användardefinierade funktions typen |
    | YOUR_ACCESS_CONTROL_PATH | Sökväg till åtkomst kontroll |

>[!TIP]
> Läs artikeln [skapa och hantera roll tilldelningar](./security-create-manage-role-assignments.md) för mer information om användardefinierade funktions hanterings-API-åtgärder och slut punkter.

## <a name="send-telemetry-to-be-processed"></a>Skicka telemetri som ska bearbetas

Sensorn som definieras i spatial Intelligence-diagrammet skickar telemetri. I sin tur utlöser telemetri körningen av den användardefinierade funktion som överfördes. Data processorn hämtar Telemetrin. Sedan skapas en körnings plan för den användardefinierade funktionens anrop.

1. Hämta matchningarna för sensorn som läsningen genererades från.
1. Hämta de associerade användardefinierade funktionerna beroende på vilka matchningar som har utvärderats.
1. Kör varje användardefinierad funktion.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar Azure Digitals-slutpunkter](./how-to-egress-endpoints.md) för att skicka händelser till.

- Om du vill ha mer information om routning i digitala Azure- [meddelanden läser du dirigera händelser och meddelanden](./concepts-events-routing.md).

- Läs [dokumentationen om klient biblioteks referens](./reference-user-defined-functions-client-library.md).