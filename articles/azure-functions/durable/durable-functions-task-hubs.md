---
title: Aktivitetshubbar i varaktiga funktioner - Azure
description: Lär dig vad en aktivitetsnav är i tillägget Varaktiga funktioner för Azure Functions. Läs om hur du konfigurerar aktivitetshubbar.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604616"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Aktivitetshubbar i varaktiga funktioner (Azure-funktioner)

En *aktivitetsnav* i [Varaktiga funktioner](durable-functions-overview.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringar. Orchestrator- och aktivitetsfunktioner kan bara interagera med varandra när de tillhör samma aktivitetsnav.

Om flera funktionsappar delar ett lagringskonto *måste* varje funktionsapp konfigureras med ett separat aktivitetsnavnamn. Ett lagringskonto kan innehålla flera aktivitetshubbar. Följande diagram illustrerar en aktivitetsnav per funktionsapp i delade och dedikerade lagringskonton.

![Diagram som visar delade och dedikerade lagringskonton.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-resurser

En aktivitetsnav består av följande lagringsresurser:

* En eller flera kontrollköer.
* En arbetsartikelkö.
* Ett historikbord.
* En instanstabell.
* En lagringsbehållare som innehåller en eller flera låneblobar.
* En förvaringsbehållare som innehåller stora meddelandenyttolaster, om tillämpligt.

Alla dessa resurser skapas automatiskt i standardkontot för Azure Storage när orchestrator-, entitets- eller aktivitetsfunktioner körs eller är schemalagda att köras. I artikeln [Prestanda och skala](durable-functions-perf-and-scale.md) beskrivs hur dessa resurser används.

## <a name="task-hub-names"></a>Namn på aktivitetsnav

Aktivitetshubbar identifieras med ett namn som följer dessa regler:

* Innehåller endast alfanumeriska tecken
* Börjar med en bokstav
* Har en minsta längd på 3 tecken, maximal längd på 45 tecken

Aktivitetsnavnamnet deklareras i *filen host.json,* vilket visas i följande exempel:

### <a name="hostjson-functions-20"></a>host.json (Funktioner 2.0)

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

### <a name="hostjson-functions-1x"></a>host.json (Funktioner 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Aktivitetshubbar kan också konfigureras med hjälp av `host.json` appinställningar, vilket visas i följande exempelfil:

### <a name="hostjson-functions-10"></a>host.json (Funktioner 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Funktioner 2.0)

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

Aktivitetsnavnamnet ställs in på `MyTaskHub` värdet för appinställningen. Följande `local.settings.json` visar hur du `MyTaskHub` definierar `samplehubname`inställningen som:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Följande kod visar hur du skriver en funktion som använder [orchestration-klientbindningen](durable-functions-bindings.md#orchestration-client) för att arbeta med en aktivitetsnav som är konfigurerad som en appinställning:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
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

> [!NOTE]
> Det föregående C#-exemplet är för varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Egenskapen task hub `function.json` i filen anges via Appinställning:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Aktivitetsnavnamn måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inget anges används ett standardnamn för aktivitetsnav som visas i följande tabell:

| Hållbar tilläggsversion | Namn på standardaktivitetsnav |
| - | - |
| 2.x | När aktivitetsnavnamnet distribueras i Azure härleds det från namnet på _funktionsappen_. När du kör utanför Azure är `TestHubName`standardaktivitetsnavets namn . |
| 1.x | Standardaktivitetsnavets namn för `DurableFunctionsHub`alla miljöer är . |

Mer information om skillnaderna mellan tilläggsversioner finns i artikeln [Över huvudversioner av varaktiga funktioner.](durable-functions-versions.md)

> [!NOTE]
> Namnet är det som skiljer en aktivitetsnav från en annan när det finns flera aktivitetshubbar i ett delat lagringskonto. Om du har flera funktionsappar som delar ett delat lagringskonto måste du uttryckligen konfigurera olika namn för varje aktivitetsnav i *host.json-filerna.* Annars kommer flera funktionsappar att konkurrera med varandra om meddelanden, vilket kan resultera i odefinierat `Pending` beteende, inklusive orkestreringar som oväntat "fastnar" i tillståndet eller. `Running`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar orkestreringsversionering](durable-functions-versioning.md)
