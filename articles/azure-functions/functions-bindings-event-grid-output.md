---
title: Utdatabindning av Azure Event Grid för Azure-funktioner
description: Lär dig att skicka en event grid-händelse i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368953"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Utdatabindning av Azure Event Grid för Azure-funktioner

Använd utdatabindningen för händelserutnät för att skriva händelser till ett anpassat ämne. Du måste ha en giltig [åtkomstnyckel för det anpassade ämnet](../event-grid/security-authentication.md#custom-topic-publishing).

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-event-grid.md).

> [!NOTE]
> Utdatabindningen för händelserutnät stöder inte SIGNATURER för delad åtkomst (SAS-token). Du måste använda avsnittets åtkomstnyckel.

> [!IMPORTANT]
> Utdatabindningen för händelserutnätet är endast tillgänglig för funktioner 2.x och högre.

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skriver ett meddelande till ett anpassat ämne för händelserutnät med hjälp av metodreturvärdet som utdata:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

I följande exempel visas `IAsyncCollector` hur du använder gränssnittet för att skicka en grupp meddelanden.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas utdata för utdata för händelserutnätet i *filen function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Här är C#-skriptkod som skapar en händelse:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Här är C#-skriptkod som skapar flera händelser:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas utdata för utdata för händelserutnätet i *filen function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Här är JavaScript-kod som skapar en enda händelse:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Här är JavaScript-kod som skapar flera händelser:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Händelserutdatabindningen är inte tillgänglig för Python.

# <a name="java"></a>[Java](#tab/java)

Händelserutdatabindningen är inte tillgänglig för Java.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

För [C#-klassbibliotek](functions-dotnet-class-library.md)använder du attributet [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

Attributets konstruktor tar namnet på en appinställning som innehåller namnet på det anpassade ämnet och namnet på en appinställning som innehåller ämnesnyckeln. Mer information om dessa inställningar finns i [Utdata - konfiguration](#configuration). Här är `EventGrid` ett attributexempel:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [exempel](#example).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Händelserutdatabindningen är inte tillgänglig för Python.

# <a name="java"></a>[Java](#tab/java)

Händelserutdatabindningen är inte tillgänglig för Java.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `EventGrid` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas in på "eventGrid". |
|**riktning** | Saknas | Måste ställas in på "ut". Den här parametern ställs in automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i funktionskoden som representerar händelsen. |
|**topicEndpointUri** |**TopicEndpointUri** | Namnet på en appinställning som innehåller URI för det `MyTopicEndpointUri`anpassade ämnet, till exempel . |
|**topicKeySetting** |**TopicKeySetting** | Namnet på en appinställning som innehåller en åtkomstnyckel för det anpassade ämnet. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Se till att du `TopicEndpointUri` anger värdet för konfigurationsegenskapen till namnet på en appinställning som innehåller URI för det anpassade avsnittet. Ange inte URI för det anpassade ämnet direkt i den här egenskapen.

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Skicka meddelanden med hjälp av `out EventGridEvent paramName`en metodparameter, till exempel . Om du vill skriva flera `ICollector<EventGridEvent>` `IAsyncCollector<EventGridEvent>` meddelanden kan `out EventGridEvent`du använda eller i stället för .

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Skicka meddelanden med hjälp av `out EventGridEvent paramName`en metodparameter, till exempel . I `paramName` C#-skriptet anges det `name` värde som anges i egenskapen *för function.json*. Om du vill skriva flera `ICollector<EventGridEvent>` `IAsyncCollector<EventGridEvent>` meddelanden kan `out EventGridEvent`du använda eller i stället för .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Öppna utdatahändelsen `context.bindings.<name>` `<name>` med hjälp av var `name` är det värde som anges i egenskapen *för function.json*.

# <a name="python"></a>[Python](#tab/python)

Händelserutdatabindningen är inte tillgänglig för Python.

# <a name="java"></a>[Java](#tab/java)

Händelserutdatabindningen är inte tillgänglig för Java.

---

## <a name="next-steps"></a>Nästa steg

* [Skicka en händelse rutnätshändelse](./functions-bindings-event-grid-trigger.md)
