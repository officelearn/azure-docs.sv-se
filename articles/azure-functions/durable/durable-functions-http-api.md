---
title: 'HTTP-API: er i Durable Functions – Azure'
description: 'Lär dig hur du implementerar HTTP-API: er i Durable Functions-tillägget för Azure Functions.'
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 11ae418ddbe007c6fd5aa44ef22ed7fddec9c702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087274"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>HTTP-API: er i Durable Functions (Azure Functions)

Det varaktiga aktivitets tillägget visar en uppsättning HTTP-API: er som kan användas för att utföra följande uppgifter:

* Hämta status för en Dirigerings instans.
* Skicka en händelse till en väntande Orchestration-instans.
* Avsluta en Dirigerings instans som körs.

Vart och ett av dessa HTTP-API: er är en webhook-åtgärd som hanteras direkt av det varaktiga aktivitets tillägget. De är inte speciella för någon funktion i Function-appen.

> [!NOTE]
> Dessa åtgärder kan också anropas direkt med hjälp av API: erna för instans hantering i klassen [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) . Mer information finns i [instans hantering](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>HTTP API URL-identifiering

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) -klassen visar ett [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) -API som kan användas för att generera en http-svars nytto last som innehåller länkar till alla åtgärder som stöds. Här är ett exempel på en HTTP-utlösnings funktion som visar hur du använder det här API: et:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>Java Script (endast funktioner 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Dessa exempel funktioner producerar följande JSON-svars data. Data typen för alla fält är `string`.

| Fält                   |Beskrivning                           |
|-----------------------------|--------------------------------------|
| **`id`**                    |ID: t för Orchestration-instansen. |
| **`statusQueryGetUri`**     |Status-URL: en för Orchestration-instansen. |
| **`sendEventPostUri`**      |URL: en "öka händelse" för Orchestration-instansen. |
| **`terminatePostUri`**      |"Avsluta"-URL: en för Orchestration-instansen. |
| **`purgeHistoryDeleteUri`** |URL: en för "Rensa historik" för Orchestration-instansen. |
| **`rewindPostUri`**         |förhandsgranskningsvyn URL: en "spola tillbaka" för Orchestration-instansen. |

Här är ett exempel på svar:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "purgeHistoryDeleteUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Formatet för webhook-URL: erna kan variera beroende på vilken version av Azure Functions-värden som du kör. Exemplet ovan gäller för Azure Functions 2. x-värden.

## <a name="async-operation-tracking"></a>Asynkron åtgärds spårning

Det HTTP-svar som tidigare nämnts är utformat för att hjälpa till att implementera långvariga HTTP asynkrona API: er med Durable Functions. Detta kallas ibland för avsöknings *konsument mönster*. Klient/server-flödet fungerar på följande sätt:

1. Klienten utfärdar en HTTP-begäran om att starta en tids krävande process, till exempel en Orchestrator-funktion.
2. Målets http-utlösare returnerar ett http 202 `Location` -svar med `statusQueryGetUri` ett huvud med värdet.
3. Klienten avsöker URL: en i `Location` rubriken. Det fortsätter att se http 202-svar med `Location` ett sidhuvud.
4. När instansen har slutförts (eller Miss lyckas) returnerar slut `Location` punkten i rubriken http 200.

Det här protokollet tillåter samordning av långvariga processer med externa klienter eller tjänster som stöder avsökning av `Location` en http-slutpunkt och följande rubrik. De grundläggande delarna är redan inbyggda i Durable Functions HTTP-API: er.

> [!NOTE]
> Som standard har alla HTTP-baserade åtgärder som tillhandahålls av [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) stöd för standard mönstret för asynkrona åtgärder. Den här funktionen gör det möjligt att bädda in en långsiktig, varaktig funktion som en del av ett Logic Apps-arbetsflöde. Mer information om Logic Apps stöd för asynkrona HTTP-mönster finns i [dokumentationen för Azure Logic Apps arbets flödes åtgärder och](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)utlösare.

## <a name="http-api-reference"></a>HTTP API-referens

Alla HTTP-API: er som implementeras av tillägget tar följande parametrar. Data typen för alla parametrar är `string`.

| Parameter        | Parametertyp  | Beskrivning |
|------------------|-----------------|-------------|
| **`taskHub`**    | Frågesträng    | Namnet på [aktivitets navet](durable-functions-task-hubs.md). Om detta inte anges antas den aktuella Function-appens aktivitets namn. |
| **`connection`** | Frågesträng    | **Namnet** på anslutnings strängen för lagrings kontot. Om inget värde anges antas standard anslutnings strängen för Function-appen. |
| **`systemKey`**  | Frågesträng    | Den auktoriseringskod som krävs för att anropa API: et. |

`systemKey`är en auktoriseringspost automatiskt genererad av den Azure Functions värden. Den ger särskilt åtkomst till de ständiga API: erna för aktivitets tillägg och kan hanteras på samma sätt som [andra auktoriseringsarkiv](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Det enklaste sättet att identifiera `systemKey` värdet är genom att `CreateCheckStatusResponse` använda API: et som nämnts ovan.

I följande avsnitt beskrivs de HTTP-API: er som stöds av tillägget och innehåller exempel på hur de kan användas.

### <a name="get-instance-status"></a>Hämta instans status

Hämtar status för en angiven Orchestration-instans.

#### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält                   | Parametertyp  | Beskrivning |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID: t för Orchestration-instansen. |
| **`showInput`**         | Frågesträng    | Valfri parameter. Om detta är `false`inställt på, inkluderas inte funktions inmatning i svarets nytto Last.|
| **`showHistory`**       | Frågesträng    | Valfri parameter. Om detta är `true`inställt på, tas Dirigerings körnings historiken med i svars nytto lasten.|
| **`showHistoryOutput`** | Frågesträng    | Valfri parameter. Om detta är `true`inställt på, kommer funktionen utdata att inkluderas i körnings historiken för dirigering.|
| **`createdTimeFrom`**   | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller efter angiven ISO8601-tidsstämpel.|
| **`createdTimeTo`**     | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller före den angivna ISO8601-tidsstämpeln.|
| **`runtimeStatus`**     | Frågesträng    | Valfri parameter. Filtrerar listan över returnerade instanser baserat på deras körnings status. Om du vill se en lista över möjliga körnings status värden, se avsnittet [frågor och instanser](durable-functions-instance-management.md) . |

#### <a name="response"></a>Svar

Flera möjliga status kod värden kan returneras.

* **HTTP 200 (OK)** : Den angivna instansen är i ett slutfört tillstånd.
* **HTTP 202 (accepterad)** : Den angivna instansen pågår.
* **HTTP 400 (felaktig begäran)** : Den angivna instansen misslyckades eller avbröts.
* **HTTP 404 (hittades inte)** : Den angivna instansen finns inte eller så har den inte börjat köras.
* **HTTP 500 (internt Server fel)** : Den angivna instansen misslyckades med ett ohanterat undantag.

Svars nytto lasten för **http 200-** och **http 202** -fall är ett JSON-objekt med följande fält:

| Fält                 | Datatyp | Beskrivning |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | sträng    | Körnings status för instansen. Värdena omfattar *körning*, *väntar*,misslyckade, avbrutna, *avslutade*, *slutförda*. |
| **`input`**           | JSON      | JSON-data som används för att initiera instansen. Det här fältet `null` är `showInput` om frågesträngparametern har angetts till `false`.|
| **`customStatus`**    | JSON      | De JSON-data som används för anpassad Dirigerings status. Det här fältet `null` är om inget anges. |
| **`output`**          | JSON      | Instansens JSON-utdata. Det här fältet `null` är om instansen inte är i ett slutfört tillstånd. |
| **`createdTime`**     | sträng    | Tiden då instansen skapades. Använder utökad ISO 8601-notation. |
| **`lastUpdatedTime`** | sträng    | Tiden då instansen senast sparades. Använder utökad ISO 8601-notation. |
| **`historyEvents`**   | JSON      | En JSON-matris som innehåller Dirigerings körnings historiken. Det här fältet `null` är `showHistory` om inte frågesträngparametern har angetts till `true`. |

Här är ett exempel på en nytto last för svar på begäran, inklusive körnings historik och aktivitets utdata (formaterade för läsbarhet):

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

**Http 202-** svaret innehåller också ett rubrik för **plats** svar som refererar till samma URL som `statusQueryGetUri` det angivna fältet ovan.

### <a name="get-all-instances-status"></a>Hämta alla instans status

Du kan också fråga efter status för alla instanser genom att ta `instanceId` bort från begäran om att hämta instans status. I det här fallet är de grundläggande parametrarna samma som "Hämta instans status". Parametrar för frågesträng för filtrering stöds också.

En sak att komma ihåg är `connection` att `code` och är valfria. Om du har anonym autentisering för funktionen krävs inte kod.
Om du inte vill använda en annan lagrings anslutnings sträng än den som har definierats i AzureWebJobsStorage-appen, kan du utan risk ignorera parametern för anslutnings frågesträngen.

#### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

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

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

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

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält                   | Parametertyp  | Beskrivning |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | ID: t för Orchestration-instansen. |
| **`showInput`**         | Frågesträng    | Valfri parameter. Om detta är `false`inställt på, inkluderas inte funktions inmatning i svarets nytto Last.|
| **`showHistory`**       | Frågesträng    | Valfri parameter. Om detta är `true`inställt på, tas Dirigerings körnings historiken med i svars nytto lasten.|
| **`showHistoryOutput`** | Frågesträng    | Valfri parameter. Om detta är `true`inställt på, kommer funktionen utdata att inkluderas i körnings historiken för dirigering.|
| **`createdTimeFrom`**   | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller efter angiven ISO8601-tidsstämpel.|
| **`createdTimeTo`**     | Frågesträng    | Valfri parameter. Filtrerar listan med returnerade instanser som skapades vid eller före den angivna ISO8601-tidsstämpeln.|
| **`runtimeStatus`**     | Frågesträng    | Valfri parameter. Filtrerar listan över returnerade instanser baserat på deras körnings status. Om du vill se en lista över möjliga körnings status värden, se avsnittet [frågor och instanser](durable-functions-instance-management.md) . |
| **`top`**               | Frågesträng    | Valfri parameter. Begränsar antalet instanser som returneras av frågan. |

#### <a name="response"></a>Svar

Här är ett exempel på svars nytto laster, inklusive Orchestration-status (formaterad för läsbarhet):

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
> Den här åtgärden kan vara dyrare vad gäller Azure Storage I/O om det finns många rader i instans tabellen. Mer information om instans tabellen finns i dokumentationen om [prestanda och skala i Durable functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Om det finns fler resultat returneras en fortsättnings-token i svars huvudet.  Namnet på rubriken är `x-ms-continuation-token`.

Om du ställer in värdet för värde för fortsättnings-token i nästa begär ande huvud kan du hämta nästa sida med resultat. Namnet på begär ande rubriken är också `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Rensa enstaka instans historik

Tar bort historiken och relaterade artefakter för en angiven Dirigerings instans.

#### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |

#### <a name="response"></a>Svar

Följande HTTP status kod värden kan returneras.

* **HTTP 200 (OK)** : Instans historiken har rensats.
* **HTTP 404 (hittades inte)** : Den angivna instansen finns inte.

Svarets nytto last för **HTTP 200-** fallet är ett JSON-objekt med följande fält:

| Fält                  | Datatyp | Beskrivning |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Antal borttagna instanser. För det enskilda instans fallet bör det här värdet alltid vara `1`. |

Här är ett exempel på en nytto Last (formaterad för läsbarhet):

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Rensa flera instans historik

Du kan också ta bort historiken och relaterade artefakter för flera instanser i en aktivitets hubb genom att `{instanceId}` ta bort från begäran om att rensa en enskild instans historik. Om du vill rensa instans historik selektivt använder du samma filter som beskrivs i begäran "Hämta alla instans status".

#### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält                 | Parametertyp  | Beskrivning |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Frågesträng    | Filtrerar listan över rensade instanser som skapades vid eller efter den tilldelade ISO8601-tidsstämpeln.|
| **`createdTimeTo`**   | Frågesträng    | Valfri parameter. Filtrerar listan över rensade instanser som skapades vid eller före angiven ISO8601-tidsstämpel.|
| **`runtimeStatus`**   | Frågesträng    | Valfri parameter. När det här alternativet anges filtreras listan över rensade instanser utifrån deras körnings status. Om du vill se en lista över möjliga körnings status värden, se avsnittet [frågor och instanser](durable-functions-instance-management.md) . |

> [!NOTE]
> Den här åtgärden kan vara mycket kostsam vad gäller Azure Storage I/O om det finns många rader i instans-och historik tabellerna. Mer information om dessa tabeller finns i dokumentationen om [prestanda och skala i Durable functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

#### <a name="response"></a>Svar

Följande HTTP status kod värden kan returneras.

* **HTTP 200 (OK)** : Instans historiken har rensats.
* **HTTP 404 (hittades inte)** : Inga instanser hittades som matchar filter uttrycket.

Svarets nytto last för **HTTP 200-** fallet är ett JSON-objekt med följande fält:

| Fält                   | Datatyp | Beskrivning |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | Antal borttagna instanser. |

Här är ett exempel på en nytto Last (formaterad för läsbarhet):

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Utlös händelse

Skickar ett händelse meddelande till en pågående Orchestration-instans.

#### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |
| **`eventName`**   | URL             | Namnet på händelsen som mål Dirigerings instansen väntar på. |
| **`{content}`**   | Begär innehåll | Den JSON-formaterade händelse nytto lasten. |

#### <a name="response"></a>Svar

Flera möjliga status kod värden kan returneras.

* **HTTP 202 (accepterad)** : Den upphöjda händelsen godtogs för bearbetning.
* **HTTP 400 (felaktig begäran)** : Innehållet i begäran var inte av typen `application/json` eller var inte ett giltigt JSON-format.
* **HTTP 404 (hittades inte)** : Det gick inte att hitta den angivna instansen.
* **HTTP 410 (borta)** : Den angivna instansen har slutförts eller misslyckats och kan inte bearbeta några aktiverade händelser.

Här är en exempel förfrågan som skickar JSON-strängen `"incr"` till en instans som väntar på en händelsemed namnet:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Svaren för detta API innehåller inget innehåll.

### <a name="terminate-instance"></a>Avsluta instans

Avslutar en pågående Dirigerings instans.

#### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parameter.

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |
| **`reason`**      | Frågesträng    | Valfritt. Orsak till att avsluta Orchestration-instansen. |

#### <a name="response"></a>Svar

Flera möjliga status kod värden kan returneras.

* **HTTP 202 (accepterad)** : Begäran om att avbryta har godkänts för bearbetning.
* **HTTP 404 (hittades inte)** : Det gick inte att hitta den angivna instansen.
* **HTTP 410 (borta)** : Den angivna instansen har slutförts eller misslyckats.

Här är en exempel förfrågan som avslutar en inaktive instansen och anger en orsak till **fel sökning**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svaren för detta API innehåller inget innehåll.

### <a name="rewind-instance-preview"></a>Återspol instans (förhands granskning)

Återställer en misslyckad Orchestration-instans till ett kör tillstånd genom att spela upp de senaste misslyckade åtgärderna.

#### <a name="request"></a>Förfrågan

För version 1. x av Functions-körningen formateras begäran enligt följande (flera rader visas för tydlighetens skull):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

I version 2. x av Functions-körningen har URL-formatet samma parametrar, men med något annorlunda prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parameter.

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | ID: t för Orchestration-instansen. |
| **`reason`**      | Frågesträng    | Valfritt. Anledningen till att du spolar tillbaka Orchestration-instansen. |

#### <a name="response"></a>Svar

Flera möjliga status kod värden kan returneras.

* **HTTP 202 (accepterad)** : Begäran om att spola tillbaka togs emot för bearbetning.
* **HTTP 404 (hittades inte)** : Det gick inte att hitta den angivna instansen.
* **HTTP 410 (borta)** : Den angivna instansen har slutförts eller avbröts.

Här är en exempel förfrågan som spolar tillbaka en misslyckad instans och anger en orsak till **åtgärdat**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svaren för detta API innehåller inget innehåll.

### <a name="signal-entity-preview"></a>Signal enhet (förhands granskning)

Skickar ett envägs åtgärds meddelande till en [varaktig enhet](durable-functions-types-features-overview.md#entity-functions). Om entiteten inte finns skapas den automatiskt.

#### <a name="request"></a>Förfrågan

HTTP-begäran formateras enligt följande (flera rader visas för tydlighetens skull):

```http
POST /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametrarna för begäran för detta API inkluderar den standard uppsättning som nämnts tidigare samt följande unika parametrar:

| Fält             | Parametertyp  | Beskrivning |
|-------------------|-----------------|-------------|
| **`entityType`**  | URL             | Entitetens typ. |
| **`entityKey`**   | URL             | Entitetens unika namn. |
| **`op`**          | Frågesträng    | Valfritt. Namnet på den användardefinierade åtgärd som ska anropas. |
| **`{content}`**   | Begär innehåll | Den JSON-formaterade händelse nytto lasten. |

Här är en exempel förfrågan som skickar ett användardefinierat "Lägg till"-meddelande till en `Counter` entitet med `steps`namnet. Innehållet i meddelandet är värdet `5`. Om entiteten inte redan finns kommer den att skapas av den här begäran:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

#### <a name="response"></a>Svar

Den här åtgärden har flera möjliga svar:

* **HTTP 202 (accepterad)** : Signal åtgärden godkändes för asynkron bearbetning.
* **HTTP 400 (felaktig begäran)** : Innehållet i begäran var inte av typen `application/json`, var inte giltigt JSON eller också har det ett `entityKey` ogiltigt värde.
* **HTTP 404 (hittades inte)** : Det gick `entityType` inte att hitta den angivna.

En lyckad HTTP-begäran innehåller inget innehåll i svaret. En misslyckad HTTP-begäran kan innehålla JSON-formaterad fel information i svars innehållet.

### <a name="query-entity-preview"></a>Fråga entitet (förhands granskning)

Hämtar status för den angivna entiteten.

#### <a name="request"></a>Förfrågan

HTTP-begäran formateras enligt följande (flera rader visas för tydlighetens skull):

```http
GET /runtime/webhooks/durabletask/entities/{entityType}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

#### <a name="response"></a>Svar

Den här åtgärden har två möjliga svar:

* **HTTP 200 (OK)** : Den angivna entiteten finns.
* **HTTP 404 (hittades inte)** : Den angivna enheten hittades inte.

Ett lyckat svar innehåller den JSON-serialiserade statusen för entiteten som dess innehåll.

#### <a name="example"></a>Exempel
Följande är ett exempel på en http-begäran som hämtar statusen för en befintlig `Counter` entitet med namnet: `steps`

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Om entiteten bara innehåller ett antal steg som sparats i `currentValue` ett fält kan svars innehållet se ut så här (formaterat för läsbarhet): `Counter`

```json
{
    "currentValue": 5
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar fel](durable-functions-error-handling.md)
