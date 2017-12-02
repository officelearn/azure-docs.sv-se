---
title: "Azure Event Hubs bindningar för Azure Functions"
description: "Förstå hur du använder Azure Event Hubs bindningar i Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: wesmc
ms.openlocfilehash: 5e0ff1b98be73eb5990601ae7c5528e4a7af670b
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) bindningar för Azure Functions. Azure Functions stöder utlösa och utgående bindningar för Händelsehubbar.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Utlösare

Använd Händelsehubbar utlösaren ska svara på en händelse som skickas till en event hub händelseström. Du måste ha läsbehörighet till händelsehubben för att konfigurera utlösaren.

När en funktion för Händelsehubbar utlösare utlöses skickas meddelandet som utlöser den i funktionen som en sträng.

## <a name="trigger---scaling"></a>Utlösa - skalning

Varje instans av en Event Hub-Triggered funktion backas upp av endast 1 EventProcessorHost (EPH)-instans. Händelsehubbar garanterar att bara 1 EPH kan få ett lån på en given partition.

Anta exempelvis att vi börjar med följande inställningar och förutsättningarna för en Händelsehubb:

1. 10 partitioner.
1. 1000 händelser fördelas jämnt över alla partitioner = > 100 meddelanden i varje partition.

När din funktion aktiveras först, är det endast 1 instans av funciton. Vi ska anropa den här funktionen instansen Function_0. Function_0 har 1 EPH som hanterar för att få ett lån på alla 10 partitioner. Läsa händelser från partitioner 0-9 startas. Från och med nu händer något av följande:

* **Funktionen endast 1 instans krävs** -Function_0 har kunnat bearbeta alla 1000 innan Azure Functions skalning logik aktiveras. Därför måste bearbetas alla 1000 meddelanden av Function_0.

* **Lägga till 1 mer funktionen instans** -Azure Functions skalning logik bestämmer att Function_0 har fler meddelanden än den kan bearbeta så skapas en ny instans Function_1,. Händelsehubbar identifierar att en ny instans av EPH som försöker läsa meddelanden. Händelsehubbar startar belastningsutjämning partitionerna över EPH instanser, t.ex. partitioner 0-4 tilldelas Function_0 och partitioner 5 – 9 tilldelas Function_1. 

* **Lägg till N fungerar fler instanser** -Azure Functions skalning logik anger att både Function_0 och Function_1 har fler meddelanden än de kan bearbeta. Det kommer skala igen för Function_2... N, där N är större än Event Hub-paritions. Läser in Händelsehubbar Utjämna partitionerna över Function_0... 9 instanser.

Unikt till Azure Functions aktuella skalning logik är att N är större än antalet partitioner. Detta görs för att säkerställa att det alltid är instanser av EPH är tillgänglig för att snabbt få ett lås på den partitionen/partitionerna när de blir tillgängliga från andra instanser. Användare endast debiteras för de resurser som används när funktionen-instansen körs och debiteras inte för den här överbelasta.

Om alla körningar av funktionen fungerar utan fel har kontrollpunkter lagts till det associerade lagringskontot. När du pekar lyckas, ska alla 1000 meddelanden aldrig hämtas igen.

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [Förkompilerade C#](#trigger---c-example)
* [C#-skript](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Utlösaren - C#-exempel

Följande exempel visar [förkompilerat C#](functions-dotnet-class-library.md) kod som loggar av meddelandetexten i hubben händelseutlösare.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

För att få åtkomst till händelsen metadata kan bindas till ett [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objekt (kräver en `using` -uttrycket för `Microsoft.ServiceBus.Messaging`).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```
Om du vill ta emot händelser i en batch Se `string` eller `EventData` en matris:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Utlösaren - exempel på C#-skript

I följande exempel visas en hub händelseutlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen loggar av meddelandetexten i hubben händelseutlösare.

Här är de bindande data den *function.json* fil:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```
Här är skriptkod C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

För att få åtkomst till händelsen metadata kan bindas till ett [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objekt (kräver en med hjälp av instruktionen för `Microsoft.ServiceBus.Messaging`).

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Om du vill ta emot händelser i en batch Se `string` eller `EventData` en matris:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Utlösaren - F #-exempel

I följande exempel visas en hub händelseutlösare bindning i en *function.json* fil och en [F # funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen loggar av meddelandetexten i hubben händelseutlösare.

Här är de bindande data den *function.json* fil:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Här är F #-kod:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Utlösaren - JavaScript-exempel

I följande exempel visas en hub händelseutlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen loggar av meddelandetexten i hubben händelseutlösare.

Här är de bindande data den *function.json* fil:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Här är JavaScript-kod:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

## <a name="trigger---attributes"></a>Utlösaren - attribut

För [förkompilerat C#](functions-dotnet-class-library.md) funktion, Använd den [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) attribut som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

Attributets konstruktorn har namnet på händelsehubben, namnet på konsumentgruppen och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns i [utlösa konfigurationsavsnittet](#trigger---configuration). Här är en `EventHubTriggerAttribute` attributet exempel:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

En komplett exempel finns [utlösaren - förkompilerade C#-exempel](#trigger---c-example).

## <a name="trigger---configuration"></a>Utlösaren - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHubTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `eventHubTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar händelsen i funktionskoden. | 
|**sökväg** |**EventHubName** | Namnet på händelsehubben. | 
|**consumerGroup** |**ConsumerGroup** | En valfri egenskap som anger den [konsumentgrupp](../event-hubs/event-hubs-features.md#event-consumers) används för att prenumerera på händelser i hubben. Om det utelämnas används den `$Default` konsumentgrupp används. | 
|**anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till den event hub-namnområdet. Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformationen** knappen för den *namnområde*, inte händelsehubben sig själv. Den här anslutningssträngen måste ha minst läsbehörighet utlösaren ska aktiveras.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---hostjson-properties"></a>Utlösaren - host.json egenskaper

Den [host.json](functions-host-json.md#eventhub) filen innehåller inställningar som styr beteendet för Händelsehubbar utlösare.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Resultat

Använda Händelsehubbar utdata bindning skriva händelser till en händelseström. Du måste ha skicka behörighet till en händelsehubb skriva händelser till den.

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [Förkompilerade C#](#output---c-example)
* [C#-skript](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

Följande exempel visar en [förkompilerat C#-funktionen](functions-dotnet-class-library.md) som skriver ett meddelande till en händelsehubb med hjälp av metoden returvärdet som utdata:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Utdata - exempel på C#-skript

I följande exempel visas en hub händelseutlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

Här är de bindande data den *function.json* fil:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Här är C# skriptkod som skapar ett meddelande:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Här följer C# skriptkod som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Utdata - F #-exempel

I följande exempel visas en hub händelseutlösare bindning i en *function.json* fil och en [F # funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

Här är de bindande data den *function.json* fil:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
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

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

I följande exempel visas en hub händelseutlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen skriver ett meddelande till en händelsehubb.

Här är de bindande data den *function.json* fil:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Här är JavaScript-kod som skickar ett meddelande:

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

## <a name="output---attributes"></a>Utdata - attribut

För [förkompilerat C#](functions-dotnet-class-library.md) funktion, Använd den [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) attribut som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

Attributets konstruktorn har namnet på händelsehubben och namnet på en appinställning som innehåller anslutningssträngen. Mer information om dessa inställningar finns [utdata - konfiguration](#output---configuration). Här är en `EventHub` attributet exempel:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

En komplett exempel finns [utdata - förkompilerade C#-exempel](#output---c-example).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `EventHub` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till ”eventHub”. |
|**riktning** | Saknas | Måste anges till ”out”. Den här parametern anges automatiskt när du skapar bindningen i Azure-portalen. |
|**Namn** | Saknas | Variabelnamnet som används i Funktionskoden som representerar händelsen. | 
|**sökväg** |**EventHubName** | Namnet på händelsehubben. | 
|**anslutning** |**Anslutning** | Namnet på en appinställning som innehåller anslutningssträngen till den event hub-namnområdet. Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformationen** knappen för den *namnområde*, inte händelsehubben sig själv. Den här anslutningssträngen måste ha skicka behörighet att skicka meddelandet till händelseströmmen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I C# och C#-skript kan skicka meddelanden med hjälp av en metodparameter som `out string paramName`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. Om du vill skriva flera meddelanden, kan du använda `ICollector<string>` eller `IAsyncCollector<string>` i stället för `out string`.

Få åtkomst till händelsen utdata i JavaScript, med hjälp av `context.bindings.<name>`. `<name>`är värdet som angetts i den `name` -egenskapen för *function.json*.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
