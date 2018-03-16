---
title: "HTTP-API: er i varaktiga funktioner – Azure"
description: "Lär dig hur du implementerar HTTP APIs i tillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 01e85290f00dc70323a16056ca8e73bfba72c975
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>HTTP-API: er i varaktiga funktioner (Azure-funktioner)

Beständiga Task-tillägget visar en uppsättning HTTP-APIs som kan användas för att utföra följande uppgifter:

* Hämta status för en orchestration-instans.
* Skicka en händelse till en väntande orchestration-instans.
* Avsluta en orchestration-instans som körs.


Var och en av dessa HTTP APIs är en webhook-åtgärd som hanteras direkt av beständiga Task-tillägget. De är inte specifik för en funktion i appen funktion.

> [!NOTE]
> Dessa åtgärder kan också anropas direkt med instanshantering API: er på den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass. Mer information finns i [Instanshantering](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>HTTP-URL för API-identifiering

Den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klassen visar en [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API som kan användas för att generera en HTTP-svar-nyttolast som innehåller länkar till alla åtgärder som stöds. Här är ett exempel på HTTP-utlösare funktion som visar hur du använder den här API:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Funktionen exempel genererar följande data för JSON-svar. Datatypen för alla fält är `string`.

| Fält             |Beskrivning                           |
|-------------------|--------------------------------------|
| id                |ID för orchestration-instans. |
| statusQueryGetUri |Status för Webbadressen till orchestration-instans. |
| sendEventPostUri  |”Rera händelse” URL till orchestration-instans. |
| terminatePostUri  |”Avsluta” URL till orchestration-instans. |

Här är ett exempelsvar:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Formatet på webhook-URL: er kan variera beroende på vilken version av Azure Functions-värden som du kör. I exemplet ovan är för Azure Functions 2.0-värden.

## <a name="async-operation-tracking"></a>Asynkron åtgärd spårning

HTTP-svar som tidigare nämnts är utformade för att implementera tidskrävande HTTP asynkrona API: er med beständiga funktioner. Detta ibland kallas den *avsökning Konsumentmönster*. Klient/server-flöde fungerar på följande sätt:

1. Klienten skickar en HTTP-begäran att starta en tidskrävande process, till exempel en orchestrator-funktion.
2. Mål HTTP-utlösaren returnerar ett HTTP-202 svar med en `Location` huvud med den `statusQueryGetUri` värde.
3. Klienten avsöker URL-Adressen i den `Location` rubrik. Den fortsätter att se 202 HTTP-svar med en `Location` huvud.
4. När instansen är klar (eller misslyckas), slutpunkt i den `Location` huvud returnerar HTTP 200.

Det här protokollet kan samordna tidskrävande processer med externa klienter eller tjänster som stöder avsöka en HTTP-slutpunkt och följa den `Location` rubrik. Grundläggande uppgifter är redan inbyggd i varaktiga funktioner http-API: erna.

> [!NOTE]
> Som standard är alla HTTP-baserade åtgärder som tillhandahålls av [Azure Logikappar](https://azure.microsoft.com/services/logic-apps/) standard asynkron åtgärd mönster. Den här funktionen gör det möjligt att bädda in en tidskrävande varaktiga funktion som en del av ett arbetsflöde för Logic Apps. Mer information om Logic Apps stöd för asynkron HTTP mönster kan hittas i den [Azure Logikappar åtgärder och utlösare dokumentationen](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>HTTP-API-referens

Alla HTTP APIs som implementerats av tillägget vidta följande parametrar. Datatypen för alla parametrar är `string`.

| Parameter  | Parametertypen  | Beskrivning |
|------------|-----------------|-------------|
| instanceId | URL             | ID för orchestration-instans. |
| taskHub    | Frågesträng    | Namnet på den [aktivitet hubb](durable-functions-task-hubs.md). Om inget annat anges, antas hubbnamnet för den aktuella funktionsapp aktivitet. |
| anslutning | Frågesträng    | Den **namn** av anslutningssträngen för lagringskontot. Om inget annat anges, antas standardanslutningssträngen för funktionen appen. |
| systemKey  | Frågesträng    | Auktoriseringsnyckeln som krävs för att anropa API: et. |
| showHistory| Frågesträng    | Valfri parameter. Om värdet `true`, körningstiden orchestration ska inkluderas i svaret nyttolast.| 
| showHistoryOutput| Frågesträng    | Valfri parameter. Om värdet `true`, aktiviteten matar ut tas med i historiken för orchestration-körning.| 

`systemKey` är auktoriseringsnyckel genereras automatiskt av Azure Functions-värden. Den mer specifikt ger åtkomst till varaktiga Task-tillägget API: er och kan hanteras på samma sätt som [andra auktorisering nycklar](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Det enklaste sättet att identifiera den `systemKey` värdet är med hjälp av den `CreateCheckStatusResponse` API tidigare nämnts.

De följande avsnitten upp det specifika HTTP APIs stöds av tillägget och innehåller exempel på hur de kan användas.

### <a name="get-instance-status"></a>Hämta instansens status

Hämtar status för en angiven orchestration-instans.

#### <a name="request"></a>Förfrågan

För funktioner 1.0 är det begärandeformatet:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Funktioner 2.0-format har samma parametrar, men har ett något annorlunda URL-prefix:

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Svar

Flera möjliga kod statusvärden kan returneras.

* **HTTP 200 (OK)**: den angivna instansen är i slutfört tillstånd.
* **HTTP 202 (accepterad)**: den angivna instansen pågår.
* **HTTP 400 (felaktig begäran)**: den angivna instansen misslyckades eller avbröts.
* **HTTP 404 (inget hittas)**: den angivna instansen finns inte eller har inte startats.

Nyttolasten i svar för den **HTTP 200** och **HTTP 202** fall är en JSON-objekt med följande fält:

| Fält           | Datatyp | Beskrivning |
|-----------------|-----------|-------------|
| runtimeStatus   | sträng    | Körningsstatus för instansen. Värden är *kör*, *väntande*, *misslyckades*, *avbruten*, *Uppsagd*, *Slutförts*. |
| Indata           | JSON      | JSON-data som används för att initiera instansen. |
| utdata          | JSON      | JSON-utdata för instansen. Det här fältet är `null` om instansen inte är i slutfört tillstånd. |
| createdTime     | sträng    | Tiden då instansen har skapats. Använder ISO 8601 utökad notation. |
| lastUpdatedTime | sträng    | Tiden då instansen senast sparade. Använder ISO 8601 utökad notation. |
| historyEvents   | JSON      | En JSON-matris som innehåller körningstiden orchestration. Det här fältet är `null` såvida inte den `showHistory` frågesträngparametern är inställd på `true`.  | 

Här är ett exempel svar nyttolast inklusive orchestration körning historik och aktivitet utdata (formaterad för att läsa):

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

### <a name="raise-event"></a>Generera en händelse

Skickar en händelse för meddelande till en orchestration-instans som körs.

#### <a name="request"></a>Förfrågan

För funktioner 1.0 är det begärandeformatet:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Funktioner 2.0-format har samma parametrar, men har ett något annorlunda URL-prefix:

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Parametrar för detta API innehåller en standarduppsättning som tidigare nämnts samt följande unika parametrar för begäran:

| Fält       | Parametertypen  | Data tType | Beskrivning |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | sträng    | Namnet på den händelse som orchestration målinstansen väntar på. |
| {innehåll}   | Begär innehåll | JSON      | JSON-formaterad händelsenyttolasten. |

#### <a name="response"></a>Svar

Flera möjliga kod statusvärden kan returneras.

* **HTTP 202 (accepterad)**: upphöjt händelsen togs emot för bearbetning.
* **HTTP 400 (felaktig begäran)**: innehållet i begäran var inte av typen `application/json` eller är inte giltig JSON.
* **HTTP 404 (inget hittas)**: Det gick inte att hitta den angivna instansen.
* **HTTP 410 (Gone)**: den angivna instansen har slutförts eller misslyckats och det går inte att bearbeta upphöjt händelser.

Här är en exempelbegäran som skickar JSON-strängen `"incr"` till en instans som väntar på att en händelse med namnet **åtgärden** (från den [räknaren](durable-functions-counter.md) exempel):

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

För funktioner 1.0 är det begärandeformatet:

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Funktioner 2.0-format har samma parametrar, men har ett något annorlunda URL-prefix:

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Begäran om parametrar för detta API innehåller en standarduppsättning som tidigare nämnts samt följande unika parameter.

| Fält       | Parametertypen  | Datatyp | Beskrivning |
|-------------|-----------------|-----------|-------------|
| reason      | Frågesträng    | sträng    | Valfri. Orsak till avslutar orchestration-instans. |

#### <a name="response"></a>Svar

Flera möjliga kod statusvärden kan returneras.

* **HTTP 202 (accepterad)**: avsluta begäran accepterades för bearbetning.
* **HTTP 404 (inget hittas)**: Det gick inte att hitta den angivna instansen.
* **HTTP 410 (Gone)**: den angivna instansen har slutförts eller misslyckats.

Här är en exempelbegäran som avslutar en instans som körs och anger en orsaken till **buggy**:

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Svar för detta API innehåller inte något innehåll.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att hantera fel](durable-functions-error-handling.md)
