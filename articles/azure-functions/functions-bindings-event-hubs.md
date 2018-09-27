---
title: Azure Event Hubs-bindningar för Azure Functions
description: Förstå hur du använder Azure Event Hubs-bindningar i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: def921a5378e85caadfaebdd06fc61dba2661756
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223099"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) bindningar för Azure Functions. Azure Functions stöder Utlös och utdatabindningar för Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

För Azure Functions-version 1.x, Event Hubs-bindningar finns i den [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-paketet, version 2.x.
Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub-lagringsplatsen.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

För Functions 2.x, Använd den [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) paketet, version 3.x.
Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Använda Event Hubs-utlösare för att svara på en händelse som skickas till en event hub-händelseström. Du måste ha läsbehörighet till händelsehubben för att konfigurera utlösaren.

När en funktion för Event Hubs-utlösaren utlöses skickas meddelandet som utlöser den i funktionen som en sträng.

## <a name="trigger---scaling"></a>Utlösa - skalning

Varje instans av en event hub-utlöst funktion backas upp av endast en [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instans. Event Hubs garanterar att endast en [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instans kan få ett lån på en given partition.

Anta exempelvis att en Händelsehubb på följande sätt:

* 10 partitioner.
* 1000 händelser fördelas jämnt över alla partitioner med 100 meddelanden i varje partition.

När funktionen aktiveras först, finns det endast en instans av funktionen. Vi kan kalla den här funktionen instansen `Function_0`. `Function_0` har en enda [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) -instans som har ett lån på alla tio partitioner. Den här instansen läser händelser från partitioner 0-9. Från och med nu händer något av följande:

* **Den nya funktionen instanser behövs inte**: `Function_0` kan bearbeta alla 1000 händelser innan de funktioner som skalning logic aktiveras. I det här fallet alla 1000 meddelanden bearbetas av `Function_0`.

* **En ytterligare funktion-instans läggs**: Functions skalning logic avgör att `Function_0` har fler meddelanden än den kan bearbeta. I det här fallet en ny funktion app-instansen (`Function_1`) skapas tillsammans med en ny [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instans. Händelsehubbar identifierar att en ny värdinstans som försöker läsa meddelanden. Event Hubs-belastningsutjämnar partitioner över på dess värddatorinstanser. Till exempel partitioner 0-4 kan ha tilldelats `Function_0` och partitionerar 5 – 9 till `Function_1`. 

* **N fler instanser av funktionen läggs**: Functions skalning logic avgör att båda `Function_0` och `Function_1` har fler meddelanden än vad de kan bearbeta. Ny funktion appinstanser `Function_2`... `Functions_N` skapas, där `N` är större än antalet händelsenavspartitioner. I vårt exempel Händelsehubbar igen belastningsutjämnar partitioner, i det här fallet mellan instanserna `Function_0`... `Functions_9`. 

Observera att om funktioner som kan skalas till `N` instanser, vilket är ett tal som är större än antalet händelsenavspartitioner. Detta görs för att se till att det är alltid [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instanser är tillgängliga att hämta lås på partitioner när de blir tillgängliga från andra instanser. Du debiteras bara för de resurser som används när funktionen-instans kör; du debiteras inte för den här överetablering.

När alla funktionen körningen har slutförts (med eller utan fel) har kontrollpunkter lagts till det associerade lagringskontot. När kontrollpunkter lyckas, hämtas alla 1000 meddelanden aldrig igen.

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som loggar brödtexten i event hub-utlösare.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Du får åtkomst till [händelsemetadata](#trigger---event-metadata) i Funktionskoden, binda till en [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objekt (kräver en med hjälp av instruktionen för `Microsoft.ServiceBus.Messaging`). Du kan också komma åt samma egenskaper med hjälp av bindningen uttryck i Metodsignaturen.  I följande exempel visas båda sätten att hämta samma data:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Om du vill ta emot händelser i en batch göra `string` eller `EventData` en matris:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen loggar brödtexten i event hub-utlösare.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
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
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Du får åtkomst till [händelsemetadata](#trigger---event-metadata) i Funktionskoden, binda till en [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objekt (kräver en med hjälp av instruktionen för `Microsoft.ServiceBus.Messaging`). Du kan också komma åt samma egenskaper med hjälp av bindningen uttryck i Metodsignaturen.  I följande exempel visas båda sätten att hämta samma data:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Om du vill ta emot händelser i en batch göra `string` eller `EventData` en matris:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Utlösare – F #-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [F #-funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen loggar brödtexten i event hub-utlösare.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är F #-kod:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser [händelsemetadata](#trigger---event-metadata) och loggar meddelandet.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
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
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Om du vill ta emot händelser i en batch, ange `cardinality` till `many` i den *function.json* fil, enligt följande exempel. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 

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

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

I följande exempel visas en Event Hub-utlösare bindning i en *function.json* fil och en [Java funktionen](functions-reference-java.md) som använder bindningen. Funktionen loggar brödtexten i utlösaren Event Hub.

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

 I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `EventHubTrigger` anteckning om parametrar vars värde skulle hämtas från Event Hub. Med dessa anteckningar orsaka funktionen ska köras när en händelse tas emot.  Den här anteckningen kan användas med interna Java-typer, Pojo eller kan ha värdet null-värden med hjälp av valfritt<T>. 

## <a name="trigger---attributes"></a>Utlösare - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) attribut.

Attributets konstruktorn tar namnet på händelsehubben, namnet på konsumentgruppen och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns i den [utlösa konfigurationsavsnittet](#trigger---configuration). Här är en `EventHubTriggerAttribute` attributet exempel:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHubTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `eventHubTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar objektet händelse i funktionskoden. | 
|**Sökväg** |**EventHubName** | Fungerar endast 1.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. | 
|**eventHubName** |**EventHubName** | Fungerar endast 2.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. |
|**consumerGroup** |**consumerGroup** | En valfri egenskap som anger den [konsumentgrupp](../event-hubs/event-hubs-features.md#event-consumers) används för att prenumerera på händelser i hubben. Om det utelämnas används den `$Default` konsumentgrupp används. | 
|**kardinalitet** | Saknas | För Javascript. Ange `many` för att aktivera Batchbearbetning.  Om detta utelämnas eller värdet `one`, enskilt meddelande som skickades till funktionen. | 
|**anslutning** |**anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till namnområdet för event hub. Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformation** för den [namnområde](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), inte händelsehubben själva. Den här anslutningssträngen måste ha minst läsbehörighet till aktivera utlösaren.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Utlösare - metadata för händelser

Event Hubs-utlösare innehåller flera [metadataegenskaper](functions-triggers-bindings.md#binding-expressions---trigger-metadata). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa är egenskaper för den [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) klass.

|Egenskap |Typ|Beskrivning|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Den `PartitionContext` instans.|
|`EnqueuedTimeUtc`|`DateTime`|Kötid i UTC.|
|`Offset`|`string`|Förskjutningen av data i förhållande till Event Hub partition dataströmmen. Förskjutningen är en markör eller identifieraren för en händelse i Event Hubs-dataströmmen. Identifieraren är unika inom en partition av Händelsehubbar dataströmmen.|
|`PartitionKey`|`string`|Den partition som vilken händelse som data ska skickas.|
|`Properties`|`IDictionary<String,Object>`|Användaregenskaper av händelsedata.|
|`SequenceNumber`|`Int64`|Logiska sekvensnumret för händelsen.|
|`SystemProperties`|`IDictionary<String,Object>`|Systemegenskaper inklusive händelsedata.|

Se [kodexempel](#trigger---example) som använder de här egenskaperna tidigare i den här artikeln.

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

Den [host.json](functions-host-json.md#eventhub) filen innehåller inställningar som styr beteendet för Event Hubs-utlösare.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Resultat

Använda Event Hubs-utdatabindning till skriva händelser till en händelseström. Du måste ha skicka behörighet till en event hub att skriva händelser till den.

Kontrollera att nödvändiga paketet refererar till är uppfyllda: [fungerar 1.x](#packages---functions-1.x) eller [fungerar 2.x](#packages---functions-2.x) 

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skriver ett meddelande till en händelsehubb med hjälp av metoden returvärdet som utdata:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 

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

Här är C#-skriptkoden som skapar ett meddelande:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Här är C#-skriptkoden som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Resultat – F #-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [F #-funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 

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

Här är F #-kod:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

I följande exempel visas en utlösare för händelsehubben bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

I följande exempel visas data för Event Hubs-bindning i den *function.json* fil. Det första exemplet avser fungerar 2.x och den andra är för Functions 1.x. 

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

Här är JavaScript-kod som skickar ett enskilt meddelande:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Här är JavaScript-kod som skickar flera meddelanden:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---java-example"></a>Resultat – Java-exemplet

I följande exempel visas en Java-funktion som skriver ett meddelande innehåller den aktuella tiden till en Händelsehubb.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@EventHubOutput` anteckning om parametrar vars värde är poublished till Event Hub.  Parametern måste vara av typen `OutputBinding<T>` , där T är en POJO eller några interna Java-datatypen. 

## <a name="output---attributes"></a>Utdata - attribut

För [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribut.

Attributets konstruktorn tar namnet på händelsehubben och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns i [utdata - konfigurationen](#output---configuration). Här är en `EventHub` attributet exempel:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHub` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till ”eventHub”. |
|**riktning** | Saknas | Måste anges till ”ut”. Den här parametern anges automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i Funktionskoden som representerar händelsen. | 
|**Sökväg** |**EventHubName** | Fungerar endast 1.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. | 
|**eventHubName** |**EventHubName** | Fungerar endast 2.x. Namnet på händelsehubben. När namnet på händelsehubben finns också i anslutningssträngen, åsidosätter det värdet den här egenskapen vid körning. |
|**anslutning** |**anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till namnområdet för event hub. Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformation** för den *namnområde*, inte händelsehubben själva. Den här anslutningssträngen måste ha behörighet att skicka att skicka meddelandet till händelseströmmen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I C# och C#-skript, skicka meddelanden genom att använda en metodparameter som `out string paramName`. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. Du kan använda för att skriva flera meddelanden `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string`.

Få åtkomst till händelsen utdata i JavaScript, med hjälp av `context.bindings.<name>`. `<name>` är värdet som angetts i den `name` egenskapen för *function.json*.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Händelsehubb | [Handboken](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
