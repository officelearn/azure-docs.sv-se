---
title: Aktivitets nav i Durable Functions – Azure
description: Läs om vad en aktivitets hubb är i Durable Functions-tillägget för Azure Functions. Lär dig hur du konfigurerar konfiguration av aktivitets nav.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: b0a58251530467d788710b0584b15715a207e20f
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734330"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Aktivitets nav i Durable Functions (Azure Functions)

En *aktivitets hubb* i [Durable Functions](durable-functions-overview.md) är en logisk behållare för Azure Storage resurser som används för dirigering. Orchestrator-och aktivitets funktioner kan bara samverka med varandra när de tillhör samma aktivitets nav.

Om flera Functions-appar delar ett lagrings konto, *måste* varje funktions app konfigureras med ett separat aktivitets nav namn. Ett lagrings konto kan innehålla flera aktivitets nav. Följande diagram illustrerar en aktivitets hubb per Function-app i delade och dedikerade lagrings konton.

![Diagram som visar delade och dedikerade lagrings konton.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage resurser

En aktivitets hubb består av följande lagrings resurser:

* En eller flera kontroll köer.
* En kö för arbets objekt.
* En historik tabell.
* Tabell med en instans.
* En lagrings behållare som innehåller en eller flera blobar för lån.

Alla dessa resurser skapas automatiskt i standard Azure Storages kontot när Orchestrator-eller aktivitets funktioner körs eller är schemalagda att köras. I artikeln om [prestanda och skala](durable-functions-perf-and-scale.md) förklaras hur dessa resurser används.

## <a name="task-hub-names"></a>Namn på uppgifts hubb

Aktivitets nav identifieras med ett namn som deklareras i *Host. JSON* -filen, som visas i följande exempel:

### <a name="hostjson-functions-1x"></a>Host. JSON (funktion 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host. JSON (funktion 2. x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

Aktivitets nav kan också konfigureras med hjälp av appinställningar, som du ser i följande *Host. JSON* -exempel fil:

### <a name="hostjson-functions-1x"></a>Host. JSON (funktion 1. x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host. JSON (funktion 2. x)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

Aktivitets navets namn anges till värdet för `MyTaskHub` appens inställning. Följande `local.settings.json` visar hur du `MyTaskHub` definierar inställningen som `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Här är ett förkompilerat C# exempel på hur du skriver en funktion som använder en [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) för att arbeta med en aktivitets hubb som är konfigurerad som en app-inställning:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

### <a name="javascript"></a>JavaScript

Egenskapen aktivitets nav i `function.json` filen anges via appens inställning:
```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Namn på uppgifts hubbar måste börja med en bokstav och får bara bestå av bokstäver och siffror. Om inget värde anges är standard namnet **DurableFunctionsHub**.

> [!NOTE]
> Namnet är det som skiljer en aktivitets hubb från en annan när det finns flera aktivitets nav i ett delat lagrings konto. Om du har flera Function-appar som delar ett delat lagrings konto måste du uttryckligen konfigurera olika namn för varje aktivitets nav i *Host. JSON* -filer. Annars konkurrerar flera Function-appar med varandra för meddelanden, vilket kan resultera i odefinierat beteende.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versions hantering](durable-functions-versioning.md)
