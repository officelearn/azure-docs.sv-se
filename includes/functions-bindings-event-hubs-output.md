---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589745"
---
Använd utdatabindningen för händelsehubbar för att skriva händelser till en händelseström. Du måste ha behörighet att skicka till en händelsehubb för att kunna skicka händelser till den.

Kontrollera att de nödvändiga paketreferenserna finns på plats innan du försöker implementera en utdatabindning.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](../articles/azure-functions/functions-dotnet-class-library.md) som skriver ett meddelande till en händelsehubb med hjälp av metodens returvärde som utdata:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

I följande exempel visas `IAsyncCollector` hur du använder gränssnittet för att skicka en grupp meddelanden. Det här scenariot är vanligt när du bearbetar meddelanden som kommer från en eventhub och skickar resultatet till en annan eventhubb.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en händelsehubbutlösare som binder i en *function.json-fil* och en [C#-skriptfunktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas bindningsdata för händelsehubbar i *filen function.json.* Det första exemplet är för Funktioner 2.x och högre, och det andra är för Funktioner 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är C#-skriptkod som skapar ett meddelande:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Här är C#-skriptkod som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en händelsehubbutlösare som binder i en *function.json-fil* och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas bindningsdata för händelsehubbar i *filen function.json.* Det första exemplet är för Funktioner 2.x och högre, och det andra är för Funktioner 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är JavaScript-kod som skickar ett enda meddelande:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Här är JavaScript-kod som skickar flera meddelanden:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en händelsehubbutlösare som binder i en *function.json-fil* och en [Python-funktion](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas bindningsdata för händelsehubbar i *filen function.json.*

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är Python-kod som skickar ett enda meddelande:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en Java-funktion som skriver ett meddelande som innehåller den aktuella tiden till en eventhub.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@EventHubOutput` på parametrar vars värde skulle publiceras i Event Hub.  Parametern ska vara `OutputBinding<T>` av typen , där T är en POJO eller någon inbyggd Java-typ.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

För [C#-klassbibliotek](../articles/azure-functions/functions-dotnet-class-library.md)använder du attributet [EventHubAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs)

Attributets konstruktor tar namnet på händelsehubben och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns i [Utdata - konfiguration](#configuration). Här är `EventHub` ett attributexempel:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [Exempel på Utdata - C#.](#example)

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

I [Java-funktionernas körningsbibliotek](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)använder du [EventHubOutput-anteckningen](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) på parametrar vars värde skulle publiceras i Event Hub. Parametern ska vara `OutputBinding<T>` av `T` typen , där är en POJO eller någon inbyggd Java-typ.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `EventHub` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas in på "eventHub". |
|**riktning** | Saknas | Måste ställas in på "ut". Den här parametern ställs in automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i funktionskoden som representerar händelsen. |
|**Sökvägen** |**EventHubName** | Endast funktioner 1.x. Namnet på händelsehubben. När händelsenavnamnet också finns i anslutningssträngen åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Funktioner 2.x och högre. Namnet på händelsehubben. När händelsenavnamnet också finns i anslutningssträngen åsidosätter det värdet den här egenskapen vid körning. |
|**Anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till händelsehubbens namnområde. Kopiera den här **anslutningssträngen** genom att klicka på knappen Anslutningsinformation för *namnområdet*, inte själva händelsehubben. Den här anslutningssträngen måste ha sändningsbehörighet för att skicka meddelandet till händelseströmmen.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Skicka meddelanden med hjälp av `out string paramName`en metodparameter, till exempel . I `paramName` C#-skriptet anges det `name` värde som anges i egenskapen *för function.json*. Om du vill skriva flera `ICollector<string>` `IAsyncCollector<string>` meddelanden kan `out string`du använda eller i stället för .

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Skicka meddelanden med hjälp av `out string paramName`en metodparameter, till exempel . I `paramName` C#-skriptet anges det `name` värde som anges i egenskapen *för function.json*. Om du vill skriva flera `ICollector<string>` `IAsyncCollector<string>` meddelanden kan `out string`du använda eller i stället för .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Öppna utdatahändelsen `context.bindings.<name>` `<name>` med hjälp av var `name` är det värde som anges i egenskapen *för function.json*.

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion:

- **Returvärde**: `name` Ange egenskapen i `$return` *function.json* till . Med den här konfigurationen sparas funktionens returvärde som ett Event Hub-meddelande.

- **Imperativ:** Skicka ett värde till den [inställda](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metoden för parametern som deklarerats som [uttyp.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Värdet som `set` skickas till sparas som ett Event Hub-meddelande.

# <a name="java"></a>[Java](#tab/java)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion med hjälp av [EventHubOutput-anteckningen:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Returvärde**: Genom att använda anteckningen till själva funktionen sparas funktionens returvärde som ett Event Hub-meddelande.

- **Tvingande**: Om du uttryckligen vill ange meddelandevärdet använder du [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)anteckningen på en viss parameter av typen , där `T` är en POJO eller någon inbyggd Java-typ. Med den `setValue` här konfigurationen kvarstår värdet som ett Event Hub-meddelande om du skickar ett värde till metoden.

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Händelsehubb | [Verksamhetsguide](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>värd.json-inställningar

I det här avsnittet beskrivs de globala konfigurationsinställningar som är tillgängliga för den här bindningen i version 2.x och senare. Exempelvärden.json filen nedan innehåller endast inställningarna för version 2.x+ för den här bindningen. Mer information om globala konfigurationsinställningar i version 2.x och senare finns i [host.json-referens för Azure Functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x finns i [host.json-referens för Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|`maxBatchSize`|10|Det maximala antalet händelser som tas emot per mottagningsloop.|
|`prefetchCount`|300|Standardantalet förhämtning som används `EventProcessorHost`av den underliggande .|
|`batchCheckpointFrequency`|1|Antalet händelsebatchar som ska bearbetas innan du skapar en EventHub-markörkontrollpunkt.|
