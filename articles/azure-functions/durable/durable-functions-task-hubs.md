---
title: Uppgiftshubbar i varaktiga funktioner – Azure
description: Lär dig vilka en uppgift hub finns i tillägget varaktiga funktioner för Azure Functions. Lär dig hur du konfigurerar konfigurera uppgiftshubbar.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: 596eedab39ff926fcdc880c82c49ac464b7ff23b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753478"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Uppgiftshubbar i varaktiga funktioner (Azure Functions)

En *uppgift hub* i [varaktiga funktioner](durable-functions-overview.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringar. Orchestrator och aktivitet kan bara interagera med varandra när de tillhör samma uppgift hubb.

Om flera funktionsappar delar ett storage-konto varje funktionsapp *måste* konfigureras med en separat åtgärd-hubbnamn. Ett lagringskonto kan innehålla flera uppgiftshubbar. Följande diagram illustrerar en uppgift hubb per funktionsappen i delade och dedikerade storage-konton.

![Diagram som visar delade och dedikerade storage-konton.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-resurser

En uppgift hubb består av följande lagringsresurser:

* En eller fler köer för kontrollen.
* En arbetsobjektet kön.
* En historiktabellen.
* En tabell med instanser.
* En storage-behållare som innehåller en eller flera blobbar för lånet.

Alla dessa resurser skapas automatiskt i Azure Storage-kontot när orchestrator eller Aktivitetsfunktioner kör eller är schemalagda att köras. Den [prestanda och skalning](durable-functions-perf-and-scale.md) artikeln förklarar hur dessa resurser används.

## <a name="task-hub-names"></a>Namn på uppgift

Uppgiftshubbar identifieras med ett namn som är deklarerad i den *host.json* filen enligt i följande exempel:

### <a name="hostjson-functions-1x"></a>Host.JSON (fungerar 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host.JSON (fungerar 2.x)

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

Uppgiftshubbar kan också konfigureras med hjälp av appinställningarna, enligt följande *host.json* exempelfil:

### <a name="hostjson-functions-1x"></a>Host.JSON (fungerar 1.x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>Host.JSON (fungerar 2.x)

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

Uppgiften hubbnamnet anges till värdet för den `MyTaskHub` appinställningen. Följande `local.settings.json` visar hur du definierar den `MyTaskHub` som `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Här är en förkompilerade C# exempel på hur du skriver en funktion som använder en [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) att arbeta med en uppgift-hubb som är konfigurerad som en App-inställning:

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

Nedan är konfigurationen som krävs för JavaScript. Egenskapen uppgift hub i den `function.json` filen har angetts via Appinställningen:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Namn på aktiviteten måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inte anges standardnamnet är **DurableFunctionsHub**.

> [!NOTE]
> Namnet är vad skiljer hub för en aktivitet från en annan när det finns flera uppgiftshubbar i en delad lagring-konto. Om du har flera funktionsappar som delar en delad lagring-konto, måste du uttryckligen konfigurera olika namn för varje uppgift hubben i den *host.json* filer. Annars konkurrerar flera funktionsappar med varandra om meddelanden, vilket kan resultera i odefinierat beteende.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versionshantering](durable-functions-versioning.md)
