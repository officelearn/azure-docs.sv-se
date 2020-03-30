---
title: HTTP-API:er i varaktiga funktioner - Azure-funktioner
description: Lär dig hur du implementerar HTTP-API:er i tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278173"
---
# <a name="http-api-reference"></a>HTTP API-referens

Tillägget Varaktiga funktioner visar en uppsättning inbyggda HTTP-API:er som kan användas för att utföra hanteringsuppgifter på [orkestreringar,](durable-functions-types-features-overview.md#orchestrator-functions) [entiteter](durable-functions-types-features-overview.md#entity-functions)och [aktivitetshubbar](durable-functions-task-hubs.md). Dessa HTTP-API:er är utökningsbarhetswehooks som är auktoriserade av Azure Functions-värden men hanteras direkt av tillägget Varaktiga funktioner.

Alla HTTP-API:er som implementeras av tillägget kräver följande parametrar. Datatypen för alla `string`parametrar är .

| Parameter        | Parametertyp  | Beskrivning |
|------------------|-----------------|-------------|
| **`taskHub`**    | Frågesträng    | Namnet på [aktivitetshubben](durable-functions-task-hubs.md). Om inget anges antas den aktuella funktionsappens aktivitetsnavnamn. |
| **`connection`** | Frågesträng    | **Namnet** på anslutningssträngen för lagringskontot. Om inget anges antas standardanslutningssträngen för funktionsappen. |
| **`systemKey`**  | Frågesträng    | Auktoriseringsnyckeln som krävs för att anropa API:et. |

`systemKey`är en auktoriseringsnyckel som skapas automatiskt av Azure Functions-värden. Den ger specifikt åtkomst till API:er för varaktigt aktivitetstillägg och kan hanteras på samma sätt som [andra auktoriseringsnycklar](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Du `taskHub`kan generera URL:er `connection`som `systemKey` innehåller rätt , och frågesträngvärden med `CreateCheckStatusResponse` `CreateHttpManagementPayload` hjälp av [orchestration-klientbindnings-API:er,](durable-functions-bindings.md#orchestration-client) till exempel API:erna och API:erna i .NET eller `createCheckStatusResponse` api:erna och `createHttpManagementPayload` API:erna i JavaScript.

De följande avsnitten täcker de specifika HTTP-API:er som stöds av tillägget och ger exempel på hur de kan användas.

## <a name="start-orchestration"></a>Starta orkestrering

Startar körning av en ny instans av den angivna orchestrator-funktionen.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field              | Parametertyp  | Beskrivning |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | Namnet på orchestrator-funktionen som ska startas. |
| **`instanceId`**   | URL             | Valfri parameter. ID för orchestration-instansen. Om inget anges börjar orchestrator-funktionen med ett slumpmässigt instans-ID. |
| **`{content}`**    | Begär innehåll | Valfri. JSON-formaterad orchestrator-funktionsinmatning. |

### <a name="response"></a>Svar

Flera möjliga statuskodvärden kan returneras.

* **HTTP 202 (Accepterad)**: Den angivna orchestrator-funktionen var schemalagd att börja köras. Svarshuvudet `Location` innehåller en URL för avsökning av orkestreringsstatus.
* **HTTP 400 (Felaktig begäran)**: Den angivna orchestrator-funktionen finns inte, det angivna instans-ID:t var ogiltigt eller begär att innehåll inte var giltigt JSON.

Följande är en exempelbegäran `RestartVMs` som startar en orchestrator-funktion och innehåller JSON-objektnyttolaster:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

Svarsnyttolasten för **HTTP 202-ärendena** är ett JSON-objekt med följande fält:

| Field                       | Beskrivning                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID för orchestration-instansen. |
| **`statusQueryGetUri`**     |Status-URL:en för orchestration-instansen. |
| **`sendEventPostUri`**      |Url:en "höja händelse" för orchestration-instansen. |
| **`terminatePostUri`**      |Url:en "avsluta" för orchestration-instansen. |
| **`purgeHistoryDeleteUri`** |URL:en för "rensa historik" för orchestration-instansen. |
| **`rewindPostUri`**         |(förhandsgranskning) Url:en "spola tillbaka" för orchestration-instansen. |

Datatypen för alla `string`fält är .

Här är ett exempel svar nyttolast `abc123` för en orkestrering instans med som dess ID (formaterad för läsbarhet):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

HTTP-svaret är avsett att vara kompatibelt med *avsökningskonsumentmönstret*. Den innehåller också följande anmärkningsvärda svarsrubriker:

* **Plats**: URL:en för statusslutpunkten. Den här URL:en innehåller `statusQueryGetUri` samma värde som fältet.
* **Försök igen efter**: Antalet sekunder att vänta mellan avsökningsåtgärder. Standardvärdet är `10`.

Mer information om det asynkrona HTTP-avsökningsmönstret finns i dokumentationen för [http-async-åtgärdsspårning.](durable-functions-http-features.md#async-operation-tracking)

## <a name="get-instance-status"></a>Hämta förekomststatus

Hämtar status för en angiven orchestration-instans.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field                   | Parametertyp  | Beskrivning |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID för orchestration-instansen. |
| **`showInput`**         | Frågesträng    | Valfri parameter. Om den `false`är inställd på inkluderas inte funktionsingången i svarsnyttolasten.|
| **`showHistory`**       | Frågesträng    | Valfri parameter. Om den `true`är inställd på inkluderas historiken för orkestreringskörning i svarsnyttolasten.|
| **`showHistoryOutput`** | Frågesträng    | Valfri parameter. Om den `true`är inställd på inkluderas funktionsutgångarna i historiken för orkestreringskörning.|
| **`createdTimeFrom`**   | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade instanser som skapades vid eller efter den angivna ISO8601-tidsstämpeln.|
| **`createdTimeTo`**     | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade instanser som skapades vid eller före den angivna ISO8601-tidsstämpeln.|
| **`runtimeStatus`**     | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade instanser baserat på deras körningsstatus. Mer om du vill visa listan över möjliga körningsstatusvärden finns i artikeln [Frågeinstans.](durable-functions-instance-management.md) |

### <a name="response"></a>Svar

Flera möjliga statuskodvärden kan returneras.

* **HTTP 200 (OK)**: Den angivna instansen är i ett slutfört tillstånd.
* **HTTP 202 (Accepterad)**: Den angivna instansen pågår.
* **HTTP 400 (Felaktig begäran)**: Den angivna instansen misslyckades eller avslutades.
* **HTTP 404 (Hittades inte)**: Den angivna instansen finns inte eller har inte börjat köras.
* **HTTP 500 (Internt serverfel)**: Den angivna instansen misslyckades med ett ohanterat undantag.

Svarsnyttolasten för **HTTP 200-** och **HTTP 202-ärendena** är ett JSON-objekt med följande fält:

| Field                 | Datatyp | Beskrivning |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | sträng    | Körningsstatus för instansen. Värden inkluderar *Körning*, *Väntande*, *Misslyckades*, *Avbröt , Avslutad*, *Terminated* *Slutförd*. |
| **`input`**           | JSON      | JSON-data som används för att initiera instansen. Det här `null` fältet `showInput` är om frågesträngparametern är inställd på `false`.|
| **`customStatus`**    | JSON      | JSON-data som används för anpassad orkestreringsstatus. Det här `null` fältet är om det inte är inställt. |
| **`output`**          | JSON      | JSON-utdata för instansen. Det här `null` fältet är om instansen inte är i ett slutfört tillstånd. |
| **`createdTime`**     | sträng    | Den tidpunkt då instansen skapades. Använder den utökade noteringen i ISO 8601. |
| **`lastUpdatedTime`** | sträng    | Den tidpunkt då instansen senast kvarstod. Använder den utökade noteringen i ISO 8601. |
| **`historyEvents`**   | JSON      | En JSON-matris som innehåller historiken för orkestreringskörning. Det här `null` fältet `showHistory` är om inte `true`frågesträngparametern är inställd på . |

Här är ett exempel på svarsnyttolast inklusive orchestration execution-historik och aktivitetsutdata (formaterade för läsbarhet):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202-svaret** innehåller också ett platssvarshuvud som `statusQueryGetUri` refererar till samma URL som det fält som nämndes tidigare. **Location**

## <a name="get-all-instances-status"></a>Hämta status för alla instanser

Du kan också fråga status för alla `instanceId` instanser genom att ta bort begäran från "Hämta instansstatus". I det här fallet är de grundläggande parametrarna desamma som "Hämta instansstatus". Frågesträngparametrar för filtrering stöds också.

En sak att `connection` komma `code` ihåg är det och är valfria. Om du har anonym auth `code` på funktionen, då inte krävs.
Om du inte vill använda en annan lagringsanslutningssträng än vad som definieras i appinställningen AzureWebJobsStorage kan du ignorera parametern anslutningsfrågesträng.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field                   | Parametertyp  | Beskrivning |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID för orchestration-instansen. |
| **`showInput`**         | Frågesträng    | Valfri parameter. Om den `false`är inställd på inkluderas inte funktionsingången i svarsnyttolasten.|
| **`showHistory`**       | Frågesträng    | Valfri parameter. Om den `true`är inställd på inkluderas historiken för orkestreringskörning i svarsnyttolasten.|
| **`showHistoryOutput`** | Frågesträng    | Valfri parameter. Om den `true`är inställd på inkluderas funktionsutgångarna i historiken för orkestreringskörning.|
| **`createdTimeFrom`**   | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade instanser som skapades vid eller efter den angivna ISO8601-tidsstämpeln.|
| **`createdTimeTo`**     | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade instanser som skapades vid eller före den angivna ISO8601-tidsstämpeln.|
| **`runtimeStatus`**     | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade instanser baserat på deras körningsstatus. Mer om du vill visa listan över möjliga körningsstatusvärden finns i artikeln [Frågeinstans.](durable-functions-instance-management.md) |
| **`top`**               | Frågesträng    | Valfri parameter. När det anges begränsar antalet instanser som returneras av frågan. |

### <a name="response"></a>Svar

Här är ett exempel på svarsnyttolaster inklusive orkestreringsstatus (formaterad för läsbarhet):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Den här åtgärden kan vara mycket dyr när det gäller Azure Storage I/O om det finns många rader i tabellen Instanser. Mer information om instanstabellen finns i dokumentationen [prestanda och skala i varaktiga funktioner (Azure Functions).](durable-functions-perf-and-scale.md#instances-table)
>

Om det finns fler resultat returneras en fortsättningstoken i svarshuvudet.  Namnet på rubriken `x-ms-continuation-token`är .

Om du anger standardvärdet i nästa begärandehuvud kan du få nästa resultatsida. Det här namnet på `x-ms-continuation-token`begäran huvudet är också .

## <a name="purge-single-instance-history"></a>Rensa historik för en instans

Tar bort historiken och relaterade artefakter för en angiven orchestration-instans.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID för orchestration-instansen. |

### <a name="response"></a>Svar

Följande HTTP-statuskodvärden kan returneras.

* **HTTP 200 (OK)**: Instanshistoriken har rensats.
* **HTTP 404 (Hittades inte)**: Den angivna instansen finns inte.

Svarsnyttolasten för **HTTP 200-ärendet** är ett JSON-objekt med följande fält:

| Field                  | Datatyp | Beskrivning |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | heltal   | Antalet borttagna instanser. För det enskilda instansfallet ska `1`det här värdet alltid vara . |

Här är ett exempel svar nyttolast (formaterad för läsbarhet):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Rensa flera instanshistoriker

Du kan också ta bort historiken och relaterade artefakter för `{instanceId}` flera instanser i en aktivitetsnav genom att ta bort begäran om rensning av en instans. Om du vill rensa instanshistoriken selektivt använder du samma filter som beskrivs i begäran om hämta alla instanser.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field                 | Parametertyp  | Beskrivning |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Frågesträng    | Filtrerar listan över rensade instanser som skapades vid eller efter den angivna ISO8601-tidsstämpeln.|
| **`createdTimeTo`**   | Frågesträng    | Valfri parameter. När det anges filtrerar listan över rensade instanser som skapades vid eller före den angivna ISO8601-tidsstämpeln.|
| **`runtimeStatus`**   | Frågesträng    | Valfri parameter. När det anges filtrerar listan över rensade instanser baserat på deras körningsstatus. Mer om du vill visa listan över möjliga körningsstatusvärden finns i artikeln [Frågeinstans.](durable-functions-instance-management.md) |

> [!NOTE]
> Den här åtgärden kan vara mycket dyr när det gäller Azure Storage I/O om det finns många rader i tabellerna Instanser och/eller Historik. Mer information om dessa tabeller finns i dokumentationen [prestanda och skala i varaktiga funktioner (Azure Functions).](durable-functions-perf-and-scale.md#instances-table)

### <a name="response"></a>Svar

Följande HTTP-statuskodvärden kan returneras.

* **HTTP 200 (OK)**: Instanshistoriken har rensats.
* **HTTP 404 (Hittades inte)**: Inga instanser hittades som matchar filteruttrycket.

Svarsnyttolasten för **HTTP 200-ärendet** är ett JSON-objekt med följande fält:

| Field                   | Datatyp | Beskrivning |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | heltal   | Antalet borttagna instanser. |

Här är ett exempel svar nyttolast (formaterad för läsbarhet):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Höj händelse

Skickar ett händelsemeddelande till en förekomst med orkestrering som körs.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID för orchestration-instansen. |
| **`eventName`**   | URL             | Namnet på händelsen som målorkestreringsinstansen väntar på. |
| **`{content}`**   | Begär innehåll | Den JSON-formaterade händelsenyttolasten. |

### <a name="response"></a>Svar

Flera möjliga statuskodvärden kan returneras.

* **HTTP 202 (Accepterad)**: Den höjda händelsen accepterades för bearbetning.
* **HTTP 400 (Felaktig begäran)**: Begärandeinnehållet var inte av typen `application/json` eller var inte giltigt JSON.
* **HTTP 404 (Hittades inte)**: Den angivna instansen hittades inte.
* **HTTP 410 (Borta)**: Den angivna instansen har slutförts eller misslyckats och kan inte bearbeta några upphöjda händelser.

Här är en exempelbegäran som `"incr"` skickar JSON-strängen till en instans som väntar på en händelse med namnet **åtgärd:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Svaren för det här API:et innehåller inget innehåll.

## <a name="terminate-instance"></a>Avsluta instans

Avslutar en löporkestreringsinstans.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som nämndes tidigare samt följande unika parameter.

| Field             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID för orchestration-instansen. |
| **`reason`**      | Frågesträng    | Valfri. Anledningen till att orchestration-instansen avslutas. |

### <a name="response"></a>Svar

Flera möjliga statuskodvärden kan returneras.

* **HTTP 202 (Godkänd)**: Begäran om avslutad upphör accepterades för bearbetning.
* **HTTP 404 (Hittades inte)**: Den angivna instansen hittades inte.
* **HTTP 410 (Borta)**: Den angivna instansen har slutförts eller misslyckats.

Här är ett exempel begäran som avslutar en löpande instans och anger en orsak till **buggy:**

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svaren för det här API:et innehåller inget innehåll.

## <a name="rewind-instance-preview"></a>Spola tillbaka instans (förhandsgranskning)

Återställer en misslyckad orkestreringsinstans till ett körläge genom att spela upp de senaste misslyckade åtgärderna.

### <a name="request"></a>Förfrågan

För version 1.x av funktionskörningen formateras begäran på följande sätt (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I version 2.x av funktionskörningen har URL-formatet samma parametrar men med ett något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som nämndes tidigare samt följande unika parameter.

| Field             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID för orchestration-instansen. |
| **`reason`**      | Frågesträng    | Valfri. Anledningen till att orchestration-instansen spolas tillbaka. |

### <a name="response"></a>Svar

Flera möjliga statuskodvärden kan returneras.

* **HTTP 202 (Godkänd):** Begäran om tillbakaspolning accepterades för bearbetning.
* **HTTP 404 (Hittades inte)**: Den angivna instansen hittades inte.
* **HTTP 410 (Borta)**: Den angivna instansen har slutförts eller avslutats.

Här är en exempelbegäran som spolar tillbaka en misslyckad instans och anger en orsak till **fast:**

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svaren för det här API:et innehåller inget innehåll.

## <a name="signal-entity"></a>Signalenhet

Skickar ett enkelriktad åtgärdsmeddelande till en [varaktig entitet](durable-functions-types-features-overview.md#entity-functions). Om entiteten inte finns skapas den automatiskt.

> [!NOTE]
> Varaktiga enheter är tillgängliga från varaktiga funktioner 2.0.

### <a name="request"></a>Förfrågan

HTTP-begäran är formaterad på följande sätt (flera rader visas för tydlighetens skull):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | Namnet (typen) på entiteten. |
| **`entityKey`**   | URL             | Nyckeln (unikt ID) för entiteten. |
| **`op`**          | Frågesträng    | Valfri. Namnet på den användardefinierade åtgärden som ska anropas. |
| **`{content}`**   | Begär innehåll | Den JSON-formaterade händelsenyttolasten. |

Här är en exempelbegäran som skickar ett användardefinierat "Lägg till"-meddelande till en `Counter` entitet med namnet `steps`. Innehållet i meddelandet är `5`värdet . Om entiteten inte redan finns skapas den av den här begäran:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Som standard med [klassbaserade entiteter i .NET](durable-functions-dotnet-entities.md#defining-entity-classes)ska värdet `op` `delete` för ska ta bort tillståndet för en entitet. Om entiteten definierar `delete`en åtgärd med namnet anropas dock den användardefinierade åtgärden i stället.

### <a name="response"></a>Svar

Den här åtgärden har flera möjliga svar:

* **HTTP 202 (Accepterad)**: Signalåtgärden accepterades för asynkron bearbetning.
* **HTTP 400 (Felaktig begäran)**: Begärandeinnehållet var inte av typen `application/json` `entityKey` , var ogiltigt JSON eller hade ett ogiltigt värde.
* **HTTP 404 (Hittades inte)** `entityName` : Det gick inte att hitta det angivna.

En lyckad HTTP-begäran innehåller inget innehåll i svaret. En misslyckad HTTP-begäran kan innehålla JSON-formaterad felinformation i svarsinnehållet.

## <a name="get-entity"></a>Hämta entitet

Hämtar tillståndet för den angivna entiteten.

### <a name="request"></a>Förfrågan

HTTP-begäran är formaterad på följande sätt (flera rader visas för tydlighetens skull):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Svar

Den här åtgärden har två möjliga svar:

* **HTTP 200 (OK)**: Den angivna entiteten finns.
* **HTTP 404 (Hittades inte)**: Den angivna entiteten hittades inte.

Ett lyckat svar innehåller JSON-serialiserat tillstånd för entiteten som dess innehåll.

### <a name="example"></a>Exempel
I följande exempel får HTTP-begäran `Counter` tillståndet `steps`för en befintlig entitet med namnet:

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Om `Counter` entiteten bara innehöll ett antal `currentValue` steg som sparats i ett fält kan svarsinnehållet se ut så här (formaterat för läsbarhet):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Listentiteter

Du kan fråga efter flera entiteter efter entitetsnamnet eller det senaste operationsdatumet.

### <a name="request"></a>Förfrågan

HTTP-begäran är formaterad på följande sätt (flera rader visas för tydlighetens skull):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Begärandeparametrar för det här API:et inkluderar standarduppsättningen som tidigare nämnts samt följande unika parametrar:

| Field                       | Parametertyp  | Beskrivning |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | Valfri. När det anges filtrerar listan över returnerade entiteter efter deras entitetsnamn (skiftlägesokänslig). |
| **`fetchState`**            | Frågesträng    | Valfri parameter. Om den `true`är inställd på inkluderas entitetstillståndet i svarsnyttolasten. |
| **`lastOperationTimeFrom`** | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade entiteter som bearbetade åtgärder efter den angivna ISO8601-tidsstämpeln. |
| **`lastOperationTimeTo`**   | Frågesträng    | Valfri parameter. När det anges filtrerar listan över returnerade entiteter som bearbetade åtgärder före den angivna ISO8601-tidsstämpeln. |
| **`top`**                   | Frågesträng    | Valfri parameter. När det anges begränsar antalet entiteter som returneras av frågan. |


### <a name="response"></a>Svar

Ett framgångsrikt HTTP 200-svar innehåller en JSON-serialiserad matris med entiteter och eventuellt tillståndet för varje entitet.

Som standard returnerar åtgärden de första 100 entiteterna som matchar frågevillkoren. Anroparen kan ange ett frågesträngparametervärde för `top` att returnera ett annat maximalt antal resultat. Om det finns fler resultat utöver vad som returneras returneras även en fortsättningstoken i svarshuvudet. Namnet på rubriken `x-ms-continuation-token`är .

Om du anger standardvärdet i nästa begärandehuvud kan du få nästa resultatsida. Det här namnet på `x-ms-continuation-token`begäran huvudet är också .

### <a name="example---list-all-entities"></a>Exempel - lista alla entiteter

I följande exempel http-begäran visas alla entiteter i aktivitetshubben:

```http
GET /runtime/webhooks/durabletask/entities
```

Svaret JSON kan se ut så här (formaterad för läsbarhet):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Exempel - filtrering av entiteter

I följande exempel http-begäran visas bara `counter` de två första entiteterna av typen och hämtar även deras tillstånd:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

Svaret JSON kan se ut så här (formaterad för läsbarhet):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder Application Insights för att övervaka dina varaktiga funktioner](durable-functions-diagnostics.md)