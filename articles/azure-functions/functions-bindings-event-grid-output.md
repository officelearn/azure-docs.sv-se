---
title: Azure Event Grid utgående bindning för Azure Functions
description: Lär dig att skicka en Event Grid-händelse i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 6bd4d5d82af213063b2000693e46d22744604480
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214113"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Event Grid utgående bindning för Azure Functions

Använd Event Grid utgående bindning för att skriva händelser till ett anpassat ämne. Du måste ha en giltig [åtkomst nyckel för det anpassade ämnet](../event-grid/security-authenticate-publishing-clients.md).

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-event-grid.md).

> [!NOTE]
> Event Grid utgående bindning har inte stöd för signaturer för delad åtkomst (SAS-token). Du måste använda ämnets åtkomst nyckel.

> [!IMPORTANT]
> Den Event Grid utgående bindningen är endast tillgänglig för funktionerna 2. x och högre.

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skriver ett meddelande till ett event Grid anpassat ämne med hjälp av metodens retur värde som utdata:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

I följande exempel visas hur du använder `IAsyncCollector` gränssnittet för att skicka en batch med meddelanden.

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

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas Event Grid utgående bindnings data i *function.jspå* filen.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Här är C#-skript kod som skapar en händelse:

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

Här är C#-skript kod som skapar flera händelser:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas Event Grid utgående bindnings data i *function.jspå* filen.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Här är JavaScript-kod som skapar en enskild händelse:

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

I följande exempel visas en trigger-bindning i en *function.jsi* filen och en [python-funktion](functions-reference-python.md) som använder bindningen. Den skickar sedan en händelse till det anpassade ämnet, enligt vad som anges i `topicEndpointUri` .

Här är bindnings data i *function.jspå* filen:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Här är python-exemplet för att skicka en händelse till ett anpassat ämne genom att ange `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime


def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

# <a name="java"></a>[Java](#tab/java)

Den Event Grid utgående bindningen är inte tillgänglig för Java.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

För [klass bibliotek i C#](functions-dotnet-class-library.md)använder du attributet [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

Attributets konstruktor tar namnet på en app-inställning som innehåller namnet på det anpassade ämnet och namnet på en app-inställning som innehåller ämnes nyckeln. Mer information om de här inställningarna finns i [output-Configuration](#configuration). Här är ett `EventGrid` exempel på attribut:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [exemplet](#example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Den Event Grid utgående bindningen är inte tillgänglig för python.

# <a name="java"></a>[Java](#tab/java)

Den Event Grid utgående bindningen är inte tillgänglig för Java.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `EventGrid` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste vara inställd på "eventGrid". |
|**position** | saknas | Måste anges till "out". Den här parametern anges automatiskt när du skapar bindningen i Azure Portal. |
|**Namn** | saknas | Variabel namnet som används i funktions koden som representerar händelsen. |
|**topicEndpointUri** |**TopicEndpointUri** | Namnet på en app-inställning som innehåller URI: n för det anpassade ämnet, till exempel `MyTopicEndpointUri` . |
|**topicKeySetting** |**TopicKeySetting** | Namnet på en app-inställning som innehåller en åtkomst nyckel för det anpassade ämnet. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Se till att du anger värdet för `TopicEndpointUri` konfigurations egenskapen till namnet på en app-inställning som innehåller URI: n för det anpassade ämnet. Ange inte URI för det anpassade ämnet direkt i den här egenskapen.

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Skicka meddelanden med hjälp av en metod parameter, till exempel `out EventGridEvent paramName` . Om du vill skriva flera meddelanden kan du använda `ICollector<EventGridEvent>` eller `IAsyncCollector<EventGridEvent>` i stället för `out EventGridEvent` .

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Skicka meddelanden med hjälp av en metod parameter, till exempel `out EventGridEvent paramName` . I C#-skript `paramName` är det värde som anges i `name` egenskapen för *function.jspå*. Om du vill skriva flera meddelanden kan du använda `ICollector<EventGridEvent>` eller `IAsyncCollector<EventGridEvent>` i stället för `out EventGridEvent` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till utdata-händelsen `context.bindings.<name>` genom `<name>` att använda WHERE är värdet som anges i `name` egenskapen för *function.jspå*.

# <a name="python"></a>[Python](#tab/python)

Den Event Grid utgående bindningen är inte tillgänglig för python.

# <a name="java"></a>[Java](#tab/java)

Den Event Grid utgående bindningen är inte tillgänglig för Java.

---

## <a name="next-steps"></a>Nästa steg

* [Skicka en Event Grid-händelse](./functions-bindings-event-grid-trigger.md)
