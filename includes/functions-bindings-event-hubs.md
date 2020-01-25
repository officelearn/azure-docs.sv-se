---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: ec3a7b6420144278df66f693d9fd9933449b3d80
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748890"
---
## <a name="trigger"></a>Utlösare

Använd funktions utlösaren för att svara på en händelse som skickas till händelse strömmen i Event Hub. Du måste ha Läs behörighet till den underliggande händelsehubben för att konfigurera utlösaren. När funktionen utlöses skrivs meddelandet som skickas till funktionen som en sträng.

## <a name="trigger---scaling"></a>Utlösare-skalning

Varje instans av en händelse utlöst funktion backas upp av en enda [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -instans. Utlösaren (drivs av Event Hubs) säkerställer att endast en [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -instans kan få ett lån på en specifik partition.

Ta till exempel en Event Hub enligt följande:

* 10 partitioner
* 1 000 händelser distribueras jämnt över alla partitioner, med 100-meddelanden i varje partition

När funktionen först aktive ras finns det bara en instans av funktionen. Vi kallar den första funktions instansen `Function_0`. Funktionen `Function_0` har en enda instans av [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) som innehåller ett lån på alla tio partitionerna. Den här instansen läser händelser från partitionerna 0-9. Från den här punkten sker något av följande:

* **Nya funktions instanser behövs inte**: `Function_0` kan bearbeta alla 1 000-händelser innan logiken för functions-skalning börjar gälla. I det här fallet bearbetas alla 1 000-meddelanden av `Function_0`.

* **En ytterligare funktions instans läggs till**: om logiken Functions skalning bestämmer att `Function_0` innehåller fler meddelanden än vad som kan bearbeta, skapas en ny function-serverinstans (`Function_1`). Den här nya funktionen har också en associerad instans av [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Eftersom den underliggande Event Hubs upptäcker att en ny värd instans försöker läsa meddelanden, kan du belastningsutjämna partitionerna över värd instanserna. Partitioner 0-4 kan till exempel tilldelas `Function_0` och partitioner 5-9 för att `Function_1`.

* **N fler funktions instanser läggs till**: om logiken Functions skalning avgör att både `Function_0` och `Function_1` har fler meddelanden än de kan bearbeta, skapas nya `Functions_N` Function-programinstanser.  Appar skapas till den plats där `N` är större än antalet Event Hub-partitioner. I vårt exempel kan Event Hubs återigen belastningsutjämna partitionerna, i det här fallet över instanserna `Function_0`...`Functions_9`.

I takt med skalningen är `N` instanser ett tal som är större än antalet Event Hub-partitioner. Det här mönstret används för att se till att [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -instanser är tillgängliga för att hämta lås på partitioner när de blir tillgängliga från andra instanser. Du debiteras bara för de resurser som används när funktions instansen körs. Med andra ord debiteras du inte för den här överetableringen.

När all funktions körning slutförs (med eller utan fel) läggs kontroll punkter till i det associerade lagrings kontot. När check Pointed lyckas hämtas aldrig alla 1 000-meddelanden igen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](../articles/azure-functions/functions-dotnet-class-library.md) som loggar meddelande texten i Event Hub-utlösaren.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

För att få åtkomst till [Event metadata](#trigger---event-metadata) i funktions kod, bind till ett [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objekt (kräver en using-instruktion för `Microsoft.Azure.EventHubs`). Du kan också komma åt samma egenskaper genom att använda bindnings uttryck i Metodsignaturen.  I följande exempel visas båda sätten för att hämta samma data:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

För att ta emot händelser i en batch, ska du göra `string` eller `EventData` en matris.  

> [!NOTE]
> När du tar emot i en batch kan du inte binda till metod parametrar som i exemplet ovan med `DateTime enqueuedTimeUtc` och måste ta emot dessa från varje `EventData`-objekt  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en Utlös ande bindning för Event Hub i en *Function. JSON* -fil och en [ C# skript funktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen loggar meddelande texten i Event Hub-utlösaren.

I följande exempel visas Event Hubs data bindnings data i filen *Function. JSON* .

#### <a name="version-2x-and-higher"></a>Version 2. x och högre

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är C#-skriptkoden:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

För att få åtkomst till [Event metadata](#trigger---event-metadata) i funktions kod, bind till ett [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objekt (kräver en using-instruktion för `Microsoft.Azure.EventHubs`). Du kan också komma åt samma egenskaper genom att använda bindnings uttryck i Metodsignaturen.  I följande exempel visas båda sätten för att hämta samma data:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

För att ta emot händelser i en batch, ska du göra `string` eller `EventData` en matris:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Utlös ande bindning för Event Hub i en *Function. JSON* -fil och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen läser [händelse-metadata](#trigger---event-metadata) och loggar meddelandet.

I följande exempel visas Event Hubs data bindnings data i filen *Function. JSON* .

#### <a name="version-2x-and-higher"></a>Version 2. x och högre

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Om du vill ta emot händelser i en batch ställer du in `cardinality` att `many` i filen *Function. JSON* , som du ser i följande exempel.

#### <a name="version-2x-and-higher"></a>Version 2. x och högre

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I följande exempel visas en Utlös ande bindning för Event Hub i en *Function. JSON* -fil och en [python-funktion](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen läser [händelse-metadata](#trigger---event-metadata) och loggar meddelandet.

I följande exempel visas Event Hubs data bindnings data i filen *Function. JSON* .

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är python-koden:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="javatabjava"></a>[Java](#tab/java)

I följande exempel visas en Utlös ande bindning för Event Hub i en *Function. JSON* -fil och en [Java-funktion](../articles/azure-functions/functions-reference-java.md) som använder bindningen. Funktionen loggar meddelande texten i Event Hub-utlösaren.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `EventHubTrigger` kommentar för parametrar vars värde kommer från händelsehubben. Parametrar med dessa anteckningar gör att funktionen körs när en händelse tas emot.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>`.

 ---

## <a name="trigger---attributes-and-annotations"></a>Utlös-attribut och anteckningar

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd attributet [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) i [ C# klass bibliotek](../articles/azure-functions/functions-dotnet-class-library.md).

Attributets konstruktor tar namnet på händelsehubben, namnet på konsument gruppen och namnet på en app-inställning som innehåller anslutnings strängen. Mer information om de här inställningarna finns i [avsnittet om utlösarens konfiguration](#trigger---configuration). Här är ett exempel på en `EventHubTriggerAttribute`-attribut:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Ett komplett exempel finns i [utlösare – C#-exempel](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

I Java [Functions runtime-biblioteket](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)använder du [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) -anteckningen för parametrar vars värde kommer från händelsehubben. Parametrar med dessa anteckningar gör att funktionen körs när en händelse tas emot. Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>`.

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHubTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste anges till `eventHubTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Ej tillämpligt | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar händelse posten i funktions koden. |
|**path** |**EventHubName** | Functions 1. x. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Funktioner 2. x och högre. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. Kan refereras via appinställningar% eventHubName% |
|**consumerGroup** |**ConsumerGroup** | En valfri egenskap som anger den [konsument grupp](../articles/event-hubs/event-hubs-features.md#event-consumers) som används för att prenumerera på händelser i hubben. Om detta utelämnas används den `$Default` konsument gruppen. |
|**kardinalitet** | Ej tillämpligt | För Java Script. Ange till `many` för att aktivera batchbearbetning.  Om detta utelämnas eller anges till `one`skickas ett enskilt meddelande till funktionen. |
|**anslutning** |**Anslutning** | Namnet på en app-inställning som innehåller anslutnings strängen till Event Hub-namnområdet. Kopiera den här anslutnings strängen genom att klicka på knappen **anslutnings information** för [namn området](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), inte själva händelsehubben. Den här anslutnings strängen måste ha minst Läs behörighet för att aktivera utlösaren.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Utlös händelse metadata

Event Hubs utlösaren innehåller flera [Egenskaper för metadata](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Egenskaper för metadata kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. Egenskaperna kommer från [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) -klassen.

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext`-instansen.|
|`EnqueuedTimeUtc`|`DateTime`|Kötid i UTC.|
|`Offset`|`string`|Data förskjutningen i förhållande till partitions strömmen för Händelsehubben. Offset är en markör eller identifierare för en händelse i Event Hubs data strömmen. Identifieraren är unik i en partition av Event Hubs data strömmen.|
|`PartitionKey`|`string`|Den partition som händelse data ska skickas till.|
|`Properties`|`IDictionary<String,Object>`|Användar egenskaperna för händelse data.|
|`SequenceNumber`|`Int64`|Händelsens logiska ordnings nummer.|
|`SystemProperties`|`IDictionary<String,Object>`|System egenskaper, inklusive händelse data.|

Se [kodexempel](#trigger) som använder de här egenskaperna tidigare i den här artikeln.

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

[Host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) -filen innehåller inställningar som styr Event Hubs utlösarens beteende.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Resultat

Använd Event Hubs utgående bindning för att skriva händelser till en händelse ström. Du måste ha Send-behörighet till en Event Hub för att skriva händelser till den.

Se till att de nödvändiga paket referenserna är på plats innan du försöker implementera en utgående bindning.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

## <a name="output---attributes-and-annotations"></a>Utdata-attribut och anteckningar

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

För [ C# klass bibliotek](../articles/azure-functions/functions-dotnet-class-library.md)använder du attributet [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) .

Attributets konstruktor tar namnet på händelsehubben och namnet på en app-inställning som innehåller anslutnings strängen. Mer information om de här inställningarna finns i [output-Configuration](#output---configuration). Här är ett exempel på en `EventHub`-attribut:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

I [Java Functions runtime-biblioteket](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)använder du antecknings anteckningen [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) för parametrar vars värde skulle publiceras till Event Hub. Parametern ska vara av typen `OutputBinding<T>`, där `T` är en POJO eller en ursprunglig Java-typ.

---

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHub` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste vara inställd på eventHub. |
|**riktning** | Ej tillämpligt | Måste anges till ”ut”. Den här parametern anges automatiskt när du skapar bindningen i Azure Portal. |
|**Namn** | Ej tillämpligt | Variabel namnet som används i funktions koden som representerar händelsen. |
|**path** |**EventHubName** | Functions 1. x. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Funktioner 2. x och högre. Namnet på händelsehubben. När namnet på händelsehubben också finns i anslutnings strängen, åsidosätter det värdet den här egenskapen vid körning. |
|**anslutning** |**Anslutning** | Namnet på en app-inställning som innehåller anslutnings strängen till Event Hub-namnområdet. Kopiera den här anslutnings strängen genom att klicka på knappen **anslutnings information** för *namn området*, inte själva händelsehubben. Den här anslutnings strängen måste ha Send-behörighet för att kunna skicka meddelandet till händelse strömmen.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Skicka meddelanden med hjälp av en metod parameter som `out string paramName`. I C# skript är `paramName` det värde som anges i egenskapen `name` för *Function. JSON*. Om du vill skriva flera meddelanden kan du använda `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string`.

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Skicka meddelanden med hjälp av en metod parameter som `out string paramName`. I C# skript är `paramName` det värde som anges i egenskapen `name` för *Function. JSON*. Om du vill skriva flera meddelanden kan du använda `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till utdata-händelsen med hjälp av `context.bindings.<name>` där `<name>` är värdet som anges i egenskapen `name` för *Function. JSON*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Det finns två alternativ för att placera ett Event Hub-meddelande från en funktion:

- **RETUR värde**: Ange `name`-egenskapen i *Function. json* för att `$return`. Med den här konfigurationen sparas funktionens retur värde som ett Event Hub-meddelande.

- **Tvingande**: Skicka ett värde till [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) -metoden för den parameter som deklarerats som [Utdatatyp.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Värdet som skickas till `set` sparas som ett Event Hub-meddelande.

# <a name="javatabjava"></a>[Java](#tab/java)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion med hjälp av [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) -anteckningen:

- **RETUR värde**: genom att använda anteckningen i själva funktionen sparas returvärdet för funktionen som ett Event Hub-meddelande.

- **Tvingande**: om du uttryckligen vill ange ett meddelande värde ska du använda anteckningen på en specifik parameter av typen [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), där `T` är en POJO eller en ursprunglig Java-typ. Med den här konfigurationen behåller ett värde i `setValue`-metoden värdet som ett Event Hub-meddelande.

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Event Hub | [Drift guide](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet Host. JSON-filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i version 2. x och mer finns i [Host. JSON-referens för Azure Functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

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
|`maxBatchSize`|10|Maximalt antal händelser som tas emot per Receive-slinga.|
|`prefetchCount`|300|Standard antalet för hämtningar som används av underliggande `EventProcessorHost`.|
|`batchCheckpointFrequency`|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.|
