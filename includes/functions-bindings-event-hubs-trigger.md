---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 438e3166e27511780dd871b5076a7b28ebade052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589719"
---
Använd funktionsutlösaren för att svara på en händelse som skickas till en händelsehubbhändelseström. Du måste ha läsbehörighet till den underliggande händelsehubben för att ställa in utlösaren. När funktionen utlöses skrivs meddelandet som skickas till funktionen som en sträng.

## <a name="scaling"></a>Skalning

Varje instans av en händelse som utlöses av en enda [EventProcessorHost-instans.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) Utlösaren (som drivs av Event Hubs) säkerställer att endast en [EventProcessorHost-instans](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) kan få ett lån på en viss partition.

Tänk dig till exempel en händelsehubb på följande sätt:

* 10 partitioner
* 1 000 händelser fördelas jämnt över alla partitioner, med 100 meddelanden i varje partition

När funktionen först aktiveras finns det bara en instans av funktionen. Låt oss anropa den `Function_0`första funktionsinstansen . Funktionen `Function_0` har en enda instans av [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) som har ett lån på alla tio partitioner. Den här instansen läser händelser från partitioner 0-9. Från och med nu händer något av följande:

* **Nya funktionsinstanser** `Function_0` behövs inte : kan bearbeta alla 1 000 händelser innan logiken För funktionskalning börjar gälla. I det här fallet bearbetas alla 1 000 meddelanden av `Function_0`.

* **En ytterligare funktionsinstans läggs till**: Om `Function_0` logiken Funktionerskalning bestämmer att fler`Function_1`meddelanden än den kan bearbeta, skapas en ny funktionsappinstans ( ). Den här nya funktionen har också en associerad instans av [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). När de underliggande händelsehubbar upptäcker att en ny värdinstans försöker läsa meddelanden, belastning balanserar partitionerna över värdinstanserna. Partitioner 0-4 kan till `Function_0` exempel tilldelas och partitioner 5-9 till `Function_1`.

* **N Fler funktionsinstanser läggs till**: Om `Function_0` logiken Funktionerskalning bestämmer att båda och `Function_1` har fler meddelanden än de kan bearbeta, skapas nya `Functions_N` funktionsappinstanser.  Appar skapas till `N` den punkt där är större än antalet händelsenavpartitioner. I vårt exempel, Event Hubs igen belastning balanserar partitioner, `Function_0`i detta fall över instanser ... `Functions_9`.

När skalning `N` sker är instanser ett tal som är större än antalet händelsenavpartitioner. Det här mönstret används för att säkerställa [eventprocessorhost-instanser](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) är tillgängliga för att hämta lås på partitioner när de blir tillgängliga från andra instanser. Du debiteras bara för de resurser som används när funktionsinstansen körs. Med andra ord debiteras du inte för denna överetablering.

När alla funktionskörningar är slutförd (med eller utan fel) läggs kontrollpunkter till i det associerade lagringskontot. När check-pointing lyckas hämtas aldrig alla 1 000 meddelanden igen.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](../articles/azure-functions/functions-dotnet-class-library.md) som loggar meddelandetexten för händelsehubbutlösaren.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

För att få åtkomst till [händelsemetadata](#event-metadata) i funktionskod binder `Microsoft.Azure.EventHubs`du till ett [EventData-objekt](/dotnet/api/microsoft.servicebus.messaging.eventdata) (kräver en medsats för ). Du kan också komma åt samma egenskaper med hjälp av bindningsuttryck i metodsignaturen.  I följande exempel visas båda sätten att hämta samma data:

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

Om du vill ta `string` emot `EventData` händelser i en batch gör du eller en matris.  

> [!NOTE]
> När du tar emot i en batch kan du `DateTime enqueuedTimeUtc` inte binda till `EventData` metodparametrar som i exemplet ovan med och måste ta emot dessa från varje objekt  

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en händelsehubbutlösare som binder i en *function.json-fil* och en [C#-skriptfunktion](../articles/azure-functions/functions-reference-csharp.md) som använder bindningen. Funktionen loggar meddelandetexten för händelsehubbutlösaren.

I följande exempel visas bindningsdata för händelsehubbar i *filen function.json.*

### <a name="version-2x-and-higher"></a>Version 2.x och högre

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

Här är C# skriptkoden:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

För att få åtkomst till [händelsemetadata](#event-metadata) i funktionskod binder `Microsoft.Azure.EventHubs`du till ett [EventData-objekt](/dotnet/api/microsoft.servicebus.messaging.eventdata) (kräver en medsats för ). Du kan också komma åt samma egenskaper med hjälp av bindningsuttryck i metodsignaturen.  I följande exempel visas båda sätten att hämta samma data:

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

Om du vill ta `string` emot `EventData` händelser i en batch gör du eller en matris:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en händelsehubbutlösare som binder i en *function.json-fil* och en [JavaScript-funktion](../articles/azure-functions/functions-reference-node.md) som använder bindningen. Funktionen läser [händelsemetadata](#event-metadata) och loggar meddelandet.

I följande exempel visas bindningsdata för händelsehubbar i *filen function.json.*

### <a name="version-2x-and-higher"></a>Version 2.x och högre

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

Här är JavaScript-koden:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Om du vill ta `cardinality` emot `many` händelser i en batch anger du till i *filen function.json,* vilket visas i följande exempel.

### <a name="version-2x-and-higher"></a>Version 2.x och högre

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

Här är JavaScript-koden:

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

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en händelsehubbutlösare som binder i en *function.json-fil* och en [Python-funktion](../articles/azure-functions/functions-reference-python.md) som använder bindningen. Funktionen läser [händelsemetadata](#event-metadata) och loggar meddelandet.

I följande exempel visas bindningsdata för händelsehubbar i *filen function.json.*

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Här är Python-koden:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en händelsehubbutlösare som loggar meddelandetexten för händelsehubbutlösaren.

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

 I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `EventHubTrigger` på parametrar vars värde skulle komma från Event Hub. Parametrar med dessa anteckningar gör att funktionen körs när en händelse anländer.  Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

 ---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](../articles/azure-functions/functions-dotnet-class-library.md)använder du attributet [EventHubTriggerAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs)

Attributets konstruktor tar namnet på händelsehubben, namnet på konsumentgruppen och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns i [avsnittet utlösarkonfiguration](#configuration). Här är `EventHubTriggerAttribute` ett attributexempel:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [Utlösare - C#exempel](#example).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Använd [EventHubTrigger-anteckningen](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) på parametrar vars värde skulle komma från Event Hub från [Java-funktionernas körningsbibliotek.](https://docs.microsoft.com/java/api/overview/azure/functions/runtime) Parametrar med dessa anteckningar gör att funktionen körs när en händelse anländer. Den här anteckningen kan användas med inbyggda Java-typer, `Optional<T>`POJOs eller nullable värden med .

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `EventHubTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `eventHubTrigger`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste ställas `in`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar händelseartikeln i funktionskoden. |
|**Sökvägen** |**EventHubName** | Endast funktioner 1.x. Namnet på händelsehubben. När händelsenavnamnet också finns i anslutningssträngen åsidosätter det värdet den här egenskapen vid körning. |
|**eventHubName** |**EventHubName** | Funktioner 2.x och högre. Namnet på händelsehubben. När händelsenavnamnet också finns i anslutningssträngen åsidosätter det värdet den här egenskapen vid körning. Kan refereras via appinställningarna %eventHubName% |
|**consumerGroup (konsumentGrupp)** |**Konsumentgrupp** | En valfri egenskap som anger den [konsumentgrupp](../articles/event-hubs/event-hubs-features.md#event-consumers) som används för att prenumerera på händelser i navet. Om det utelämnas `$Default` används konsumentgruppen. |
|**Kardinalitet** | Saknas | För Javascript. Ställ `many` in på för att aktivera batchbearbetning.  Om det utelämnas `one`eller ställs in på skickas ett enskilt meddelande till funktionen. |
|**Anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till händelsehubbens namnområde. Kopiera den här **anslutningssträngen** genom att klicka på knappen Anslutningsinformation för [namnområdet](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), inte själva händelsehubben. Den här anslutningssträngen måste ha minst läsbehörighet för att aktivera utlösaren.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Metadata för händelser

Utlösaren Event Hubs innehåller flera [metadataegenskaper](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Metadataegenskaper kan användas som en del av bindningsuttryck i andra bindningar eller som parametrar i koden. Egenskaperna kommer från klassen [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata)

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Instansen. `PartitionContext`|
|`EnqueuedTimeUtc`|`DateTime`|Den enqueued tiden i UTC.|
|`Offset`|`string`|Förskjutning av data i förhållande till partitionsströmmen för händelsehubben. Förskjutningen är en markör eller identifierare för en händelse i händelsehubbens strömmar. Identifieraren är unik i en partition i händelsehubbens strömmar.|
|`PartitionKey`|`string`|Den partition som händelsedata ska skickas till.|
|`Properties`|`IDictionary<String,Object>`|Händelsedatas användaregenskaper.|
|`SequenceNumber`|`Int64`|Händelsens logiska sekvensnummer.|
|`SystemProperties`|`IDictionary<String,Object>`|Systemegenskaperna, inklusive händelsedata.|

Se [kodexempel](#example) som använder dessa egenskaper tidigare i den här artikeln.

## <a name="hostjson-properties"></a>egenskaper för host.json

[Filen host.json](../articles/azure-functions/functions-host-json.md#eventhub) innehåller inställningar som styr händelsehubbars utlösarbeteende.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]