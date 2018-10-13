---
title: 'HTTP-API: er i varaktiga funktioner – Azure'
description: Lär dig hur du implementerar HTTP APIs i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 19351d31331431e3b5137676061aadc681c496a7
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166635"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>HTTP-API: er i varaktiga funktioner (Azure Functions)

Tillägget varaktiga uppgift visar en uppsättning HTTP-APIs som kan användas för att utföra följande uppgifter:

* Hämta status för en orchestration-instans.
* Skicka en händelse till en väntar orchestration-instans.
* Avsluta en orchestration-instans som körs.


Var och en av dessa HTTP APIs är en webhook-åtgärd som hanteras direkt av tillägget varaktiga uppgift. De är inte specifik för en funktion i funktionsappen.

> [!NOTE]
> Dessa åtgärder kan också anropas direkt med hjälp av API: er för instanshantering på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. Mer information finns i [Instanshantering](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>HTTP-URL för API-identifiering

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen visar en [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API som kan användas för att generera en HTTP-svarsnyttolasten som innehåller länkar till alla åtgärder som stöds. Här är ett exempel HTTP-utlösare-funktion som visar hur du använder den här API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Den här funktionen exempel genererar följande JSON-data som svar. Datatypen för alla fält är `string`.

| Fält             |Beskrivning                           |
|-------------------|--------------------------------------|
| id                |ID för orchestration-instans. |
| statusQueryGetUri |Status för Webbadressen till orchestration-instans. |
| sendEventPostUri  |”Rera händelse” Webbadressen till orchestration-instans. |
| terminatePostUri  |”Avsluta” Webbadressen till orchestration-instans. |
| rewindPostUri     |”Tillbakaspolning” Webbadressen till orchestration-instans. |

Här är ett exempel på ett svar:

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
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Formatet för webhook-URL: er kan variera beroende på vilken version av Azure Functions-värden som du kör. I exemplet ovan är för Azure Functions 2.0-värden.

## <a name="async-operation-tracking"></a>En asynkron åtgärd spårning

HTTP-svar som tidigare nämnts är utformad för att implementera tidskrävande HTTP async API: er med varaktiga funktioner. Detta är kallas ibland den *avsökning Konsumentmönster*. Klient/server-flödet fungerar på följande sätt:

1. Klienten skickar en HTTP-begäran för att starta en tidskrävande process, till exempel en orchestrator-funktion.
2. Target HTTP-utlösaren returnerar ett HTTP 202-svar med en `Location` huvud med den `statusQueryGetUri` värde.
3. Klienten söker URL: en i den `Location` rubrik. Det fortsätter att se HTTP 202-svar med en `Location` rubrik.
4. När instansen har slutförts (eller misslyckas), slutpunkt i den `Location` rubrik returnerar 200 HTTP.

Det här protokollet kan samordna tidskrävande processer med externa klienter eller tjänster som stöder avsöka en HTTP-slutpunkt och följa den `Location` rubrik. De grundläggande delarna är redan inbyggd i varaktiga funktioner http-API: erna.

> [!NOTE]
> Som standard alla HTTP-baserade åtgärder som tillhandahålls av [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) stöder standard asynkron åtgärd mönstret. Den här funktionen gör det möjligt att bädda in en hållbar tidskrävande-funktion som en del av ett Logic Apps-arbetsflöde. Mer information om Logic Apps stödet för asynkrona HTTP-mönster finns i den [Azure Logic Apps arbetsflöde åtgärder och utlösare dokumentation](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>HTTP API-referens

Alla HTTP APIs som implementeras av tillägget Gör följande parametrar. Datatypen för alla parametrar är `string`.

| Parameter  | Parametertyp  | Beskrivning |
|------------|-----------------|-------------|
| instanceId | URL             | ID för orchestration-instans. |
| taskHub    | Frågesträng    | Namnet på den [uppgift hub](durable-functions-task-hubs.md). Om inte anges, antas hubbnamnet för den aktuella funktionsapp uppgift. |
| anslutning | Frågesträng    | Den **namn** av anslutningssträngen för lagringskontot. Om inte anges, antas standardanslutningssträngen för funktionsappen. |
| systemKey  | Frågesträng    | Auktoriseringsnyckeln som krävs för att anropa API: et. |
| showHistory| Frågesträng    | Valfri parameter. Om inställd `true`, orchestration-körningshistorik tas med i svarets nyttolast.| 
| showHistoryOutput| Frågesträng    | Valfri parameter. Om inställd `true`, aktiviteten matar ut tas med i orchestration-körningshistorik.| 

`systemKey` är en auktoriseringsnyckeln automatiskt genererade av Azure Functions-värden. Den mer specifikt ger åtkomst till tillägget varaktiga uppgift API: er och kan hanteras på samma sätt som [andra auktorisering nycklar](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Det enklaste sättet att identifiera den `systemKey` värdet är med hjälp av den `CreateCheckStatusResponse` API tidigare nämnts.

Följande avsnitt beskriver den specifika HTTP APIs stöds av tillägget och innehåller exempel på hur de kan användas.

### <a name="get-instance-status"></a>Hämta status för instans

Hämtar status för en angiven orchestration-instans.

#### <a name="request"></a>Förfrågan

För Functions 1.0 är format för förfrågan på följande sätt:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0-formatet har samma parametrar, men har ett något annorlunda URL-prefix:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Svar

Flera möjliga status code-värden kan returneras.

* **HTTP 200 (OK)**: den angivna instansen är i slutfört tillstånd.
* **HTTP 202 (accepterad)**: den angivna instansen pågår.
* **HTTP 400 (felaktig begäran)**: den angivna instansen misslyckades eller avbröts.
* **HTTP 404 (hittades inte)**: den angivna instansen finns inte eller har inte startats.
* **HTTP 500 (Internt serverfel)**: den angivna instansen misslyckades med ett ohanterat undantag.

Svarets nyttolast för den **HTTP 200** och **HTTP 202** fall är en JSON-objekt med följande fält:

| Fält           | Datatyp | Beskrivning |
|-----------------|-----------|-------------|
| runtimeStatus   | sträng    | Körningsstatus för instansen. Värden är *kör*, *väntande*, *misslyckades*, *avbruten*, *Uppsagd*, *Slutförts*. |
| indata           | JSON      | JSON-data som används för att initiera instansen. |
| customStatus    | JSON      | JSON-data som används för anpassad orkestreringsstatus. Det här fältet är `null` om det inte angetts. |
| utdata          | JSON      | JSON-utdata för instansen. Det här fältet är `null` om instansen inte är i slutfört tillstånd. |
| createdTime     | sträng    | Den tid då instansen har skapats. Använder ISO 8601 utökade notation. |
| lastUpdatedTime | sträng    | Den tid då instansen senast sparade. Använder ISO 8601 utökade notation. |
| historyEvents   | JSON      | En JSON-matris som innehåller orchestration-körningshistorik. Det här fältet är `null` såvida inte den `showHistory` frågesträngparametern anges till `true`.  | 

Här är ett exempel svarsnyttolasten inklusive orchestration historik och aktivitetens utdata från körningen (formaterad för läsbarhet):

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

Den **HTTP 202** svaret innehåller också en **plats** svarshuvud som refererar till samma Webbadress som den `statusQueryGetUri` fältet tidigare nämnts.

### <a name="get-all-instances-status"></a>Hämta status för alla instanser

Du kan också fråga status för alla instanser. Ta bort den `instanceId` i 'Hämta status för instans-begäran. Parametrarna är samma som ”Get-instans status”. 

En sak som att komma ihåg är att `connection` och `code` är valfria. Om du har anonym autentisering om funktionen inte kod krävs.
Om du inte vill använda en annan blob storage-anslutningssträng än definieras i appinställningen AzureWebJobsStorage kan sedan du ignorera frågesträngparametern för anslutningen.

#### <a name="request"></a>Förfrågan

För Functions 1.0 är format för förfrågan på följande sätt:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0-formatet har samma parametrar men ett något annorlunda URL-prefix: 

```http
GET /runtime/webhooks/durabletask/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="response"></a>Svar

Här är ett exempel på svar-nyttolaster som inkluderar orkestreringsstatus (formaterad för läsbarhet):

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
> Den här åtgärden kan vara väldigt kostsamt när det gäller Azure Storage i/o, om det finns många rader i tabellen instanser. Mer information om instansen tabellen finns i den [prestanda och skalning i varaktiga funktioner (Azure Functions)](https://docs.microsoft.com/azure/azure-functions/durable-functions-perf-and-scale#instances-table) dokumentation.
> 

### <a name="raise-event"></a>Generera händelser

Skickar en händelse-meddelande till en orchestration-instans som körs.

#### <a name="request"></a>Förfrågan

För Functions 1.0 är format för förfrågan på följande sätt:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Functions 2.0-formatet har samma parametrar, men har ett något annorlunda URL-prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Parametrar för detta API innehåller en standarduppsättning som tidigare nämnts samt följande unika parametrar för begäran:

| Fält       | Parametertyp  | Data tType | Beskrivning |
|-------------|-----------------|-----------|-------------|
| EventName   | URL             | sträng    | Namnet på den händelse som orchestration målinstansen väntar på. |
| {innehåll}   | Begära innehåll | JSON      | JSON-formaterad händelsenyttolast. |

#### <a name="response"></a>Svar

Flera möjliga status code-värden kan returneras.

* **HTTP 202 (accepterad)**: händelsen aktiverades togs emot för bearbetning.
* **HTTP 400 (felaktig begäran)**: innehållet i begäran var inte av typen `application/json` eller är inte giltig JSON.
* **HTTP 404 (hittades inte)**: Det gick inte att hitta den angivna instansen.
* **HTTP 410 ()**: den angivna instansen har slutförts eller misslyckats och det går inte att bearbeta alla händelser som har aktiverats.

Här är en exempelbegäran som skickar JSON-sträng `"incr"` till en instans som väntar på en händelse med namnet **åtgärden**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Svar för detta API innehåller inte något innehåll.

### <a name="terminate-instance"></a>Avsluta instans

Avbryter en orchestration-instans som körs.

#### <a name="request"></a>Förfrågan

För Functions 1.0 är format för förfrågan på följande sätt:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0-formatet har samma parametrar, men har ett något annorlunda URL-prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Begära parametrar för detta API innehåller en standarduppsättning som tidigare nämnts samt följande unika parameter.

| Fält       | Parametertyp  | Datatyp | Beskrivning |
|-------------|-----------------|-----------|-------------|
| orsak      | Frågesträng    | sträng    | Valfri. Orsak för att avsluta orchestration-instans. |

#### <a name="response"></a>Svar

Flera möjliga status code-värden kan returneras.

* **HTTP 202 (accepterad)**: avslutningsbegäran togs emot för bearbetning.
* **HTTP 404 (hittades inte)**: Det gick inte att hitta den angivna instansen.
* **HTTP 410 ()**: den angivna instansen har slutförts eller misslyckats.

Här är en exempelbegäran som avslutar en instans som körs och anger en anledning för **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svar för detta API innehåller inte något innehåll.

## <a name="rewind-instance-preview"></a>Tillbakaspolning instans (förhandsversion)

Återställer en misslyckad orchestration-instans till ett körningsläge genom att spela upp de senaste misslyckade åtgärderna.

#### <a name="request"></a>Förfrågan

För Functions 1.0 är format för förfrågan på följande sätt:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Functions 2.0-formatet har samma parametrar, men har ett något annorlunda URL-prefix:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Begära parametrar för detta API innehåller en standarduppsättning som tidigare nämnts samt följande unika parameter.

| Fält       | Parametertyp  | Datatyp | Beskrivning |
|-------------|-----------------|-----------|-------------|
| orsak      | Frågesträng    | sträng    | Valfri. Orsaken till spola tillbaka orchestration-instans. |

#### <a name="response"></a>Svar

Flera möjliga status code-värden kan returneras.

* **HTTP 202 (accepterad)**: tillbakaspolning begäran togs emot för bearbetning.
* **HTTP 404 (hittades inte)**: Det gick inte att hitta den angivna instansen.
* **HTTP 410 ()**: den angivna instansen har slutförts eller har avslutats.

Här är en exempelbegäran som Spolar tillbaka en instans som misslyckats och anger en anledning för **fast**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svar för detta API innehåller inte något innehåll.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att hantera fel](durable-functions-error-handling.md)
