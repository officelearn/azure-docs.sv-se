---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589745"
---
Använd Event Hubs utgående bindning för att skriva händelser till en händelse ström. Du måste ha Send-behörighet till en Event Hub för att skriva händelser till den.

Se till att de nödvändiga paket referenserna är på plats innan du försöker implementera en utgående bindning.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](../articles/azure-functions/functions-dotnet-class-library.md) som skriver ett meddelande till en Event Hub med hjälp av metoden Return-värdet som utdata:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

I följande exempel visas hur du använder `IAsyncCollector`-gränssnittet för att skicka en batch med meddelanden. Det här scenariot är vanligt när du bearbetar meddelanden som kommer från en Händelsehubben och skickar resultatet till en annan Händelsehubben.

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

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en Utlös ande bindning för Event Hub i en *Function. JSON* -fil och en [ C# skript funktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen skriver ett meddelande till en Event Hub.

I följande exempel visas Event Hubs data bindnings data i filen *Function. JSON* . Det första exemplet är för functions 2. x och högre, och det andra är för funktionerna 1. x. 

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

Här är C# skript koden som skapar ett meddelande:

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

Här är C#-skriptkoden som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Utlös ande bindning för Event Hub i en *Function. JSON* -fil och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen skriver ett meddelande till en Event Hub.

I följande exempel visas Event Hubs data bindnings data i filen *Function. JSON* . Det första exemplet är för functions 2. x och högre, och det andra är för funktionerna 1. x. 

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

I följande exempel visas en Utlös ande bindning för Event Hub i en *Function. JSON* -fil och en [python-funktion](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen skriver ett meddelande till en Event Hub.

I följande exempel visas Event Hubs data bindnings data i filen *Function. JSON* .

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Här är python-koden som skickar ett enda meddelande:

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

I följande exempel visas en Java-funktion som skriver ett meddelande som innehåller den aktuella tiden till en Event Hub.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@EventHubOutput` kommentar för parametrar vars värde skulle publiceras till Event Hub.  Parametern ska vara av typen `OutputBinding<T>`, där T är en POJO eller en ursprunglig Java-typ.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

För [ C# klass bibliotek](../articles/azure-functions/functions-dotnet-class-library.md)använder du attributet [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) .

Attributets konstruktor tar namnet på händelsehubben och namnet på en app-inställning som innehåller anslutnings strängen. Mer information om de här inställningarna finns i [output-Configuration](#configuration). Här är ett exempel på en `EventHub`-attribut:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [utdata- C# exempel](#example).

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-biblioteket](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)använder du antecknings anteckningen [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) för parametrar vars värde skulle publiceras till Event Hub. Parametern ska vara av typen `OutputBinding<T>`, där `T` är en POJO eller en ursprunglig Java-typ.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `EventHub`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste vara inställd på eventHub. |
|**riktning** | Saknas | Måste anges till ”ut”. Den här parametern anges automatiskt när du skapar bindningen i Azure Portal. |
|**Namn** | Saknas | Variabel namnet som används i funktions koden som representerar händelsen. |
|**path** |**EventHubName** | Functions 1. x. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Funktioner 2. x och högre. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**anslutningen** |**Anslutning** | Namnet på en app-inställning som innehåller anslutnings strängen till Event Hub-namnområdet. Kopiera den här anslutnings strängen genom att klicka på knappen **anslutnings information** för *namn området*, inte själva händelsehubben. Den här anslutnings strängen måste ha Send-behörighet för att kunna skicka meddelandet till händelse strömmen.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Skicka meddelanden med hjälp av en metod parameter som `out string paramName`. I C# skript är `paramName` det värde som anges i egenskapen `name` för *Function. JSON*. Om du vill skriva flera meddelanden kan du använda `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string`.

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Skicka meddelanden med hjälp av en metod parameter som `out string paramName`. I C# skript är `paramName` det värde som anges i egenskapen `name` för *Function. JSON*. Om du vill skriva flera meddelanden kan du använda `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till utdata-händelsen med hjälp av `context.bindings.<name>` där `<name>` är värdet som anges i egenskapen `name` för *Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att placera ett Event Hub-meddelande från en funktion:

- **RETUR värde**: Ange `name`-egenskapen i *Function. json* för att `$return`. Med den här konfigurationen sparas funktionens retur värde som ett Event Hub-meddelande.

- **Tvingande**: Skicka ett värde till [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) -metoden för den parameter som deklarerats som [Utdatatyp.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Värdet som skickas till `set` sparas som ett Event Hub-meddelande.

# <a name="java"></a>[Java](#tab/java)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion med hjälp av [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -anteckningen:

- **RETUR värde**: genom att använda anteckningen i själva funktionen sparas returvärdet för funktionen som ett Event Hub-meddelande.

- **Tvingande**: om du uttryckligen vill ange ett meddelande värde ska du använda anteckningen på en specifik parameter av typen [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), där `T` är en POJO eller en ursprunglig Java-typ. Med den här konfigurationen behåller ett värde i `setValue`-metoden värdet som ett Event Hub-meddelande.

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Händelsehubb | [Drift guide](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet Host. JSON-filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i version 2. x och mer finns i [Host. JSON-referens för Azure Functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> En referens för Host. json i functions 1. x finns i [Host. JSON-referensen för Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

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

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|`maxBatchSize`|10|Maximalt antal händelser som tas emot per Receive-slinga.|
|`prefetchCount`|300|Standard antalet för hämtningar som används av underliggande `EventProcessorHost`.|
|`batchCheckpointFrequency`|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.|
