---
title: "Azure Functions Händelsehubbar bindningar | Microsoft Docs"
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
ms.date: 06/20/2017
ms.author: wesmc
ms.openlocfilehash: 85eb6985ef3579b1b2313db3ce5f91c3471da72f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Azure Functions Händelsehubbar bindningar
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln beskriver hur du konfigurerar och använder [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) bindningar för Azure Functions.
Azure Functions stöder utlösa och utgående bindningar för Händelsehubbar.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Om du har använt Azure Event Hubs, se den [översikt av Händelsehubbar](../event-hubs/event-hubs-what-is-event-hubs.md).

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Händelseutlösare hub
Använd Händelsehubbar utlösaren ska svara på en händelse som skickas till en event hub händelseström. Du måste ha läsbehörighet till händelsehubben för att konfigurera utlösaren.

Händelsehubbar funktionen utlösaren använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup`är en valfri egenskap som används för att ange den [konsumentgrupp](../event-hubs/event-hubs-features.md#event-consumers) används för att prenumerera på händelser i hubben. Om det utelämnas används den `$Default` konsumentgrupp används.  
`connection`måste vara namnet på en appinställning som innehåller anslutningssträngen till den event hub-namnområdet.
Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformationen** knappen för den *namnområde*, inte händelsehubben sig själv. Den här anslutningssträngen måste ha minst läsbehörighet utlösaren ska aktiveras.

[Ytterligare inställningar](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) kan anges i en host.json fil att ytterligare finjustera Händelsehubbar utlösare.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utlösaren användning
När en funktion för Händelsehubbar utlösare utlöses skickas meddelandet som utlöser den i funktionen som en sträng.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Utlösaren exempel
Anta att du har följande Händelsehubbar utlösare i den `bindings` matris med function.json:

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

Se exemplet språkspecifika loggar av meddelandetexten i hubben händelseutlösare.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Utlösaren exemplet i C# #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Du kan också få händelsen som en [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) -objektet, vilket ger dig tillgång till metadata för händelser.

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

Om du vill ta emot händelser i en batch ändra Metodsignaturen till `string[]` eller `EventData[]`.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Utlösaren exemplet i F # #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Utlösaren exemplet i Node.js

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>Händelsehubbar utdatabindning
Använda Händelsehubbar utdata bindning skriva händelser till en event hub händelseström. Du måste ha skicka behörighet till en händelsehubb skriva händelser till den.

Utdata bindningen använder följande JSON-objekt i den `bindings` matris med function.json:

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection`måste vara namnet på en appinställning som innehåller anslutningssträngen till den event hub-namnområdet.
Kopiera denna anslutningssträng genom att klicka på den **anslutningsinformationen** knappen för den *namnområde*, inte händelsehubben sig själv. Den här anslutningssträngen måste ha skicka behörighet att skicka meddelandet till händelseströmmen.

## <a name="output-usage"></a>Användning av utdata
Det här avsnittet visar hur du använder din Händelsehubbar utdata bindningen i din funktionskoden.

Du kan skicka meddelanden till den konfigurerade event hub med följande parameter:

* `out string`
* `ICollector<string>`(för att skicka flera meddelanden)
* `IAsyncCollector<string>`(asynkrona versionen av `ICollector<T>`)

<a name="outputsample"></a>

## <a name="output-sample"></a>Exempel på utdata
Anta att du har följande Händelsehubbar utdatabindning i den `bindings` matris med function.json:

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

Se exemplet språkspecifika som skriver en händelse till även dataströmmen.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Utdata exemplet i C# #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Eller skapa flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Utdata exemplet i F # #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Exempel utdata för Node.js

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Eller, för att skicka flera meddelanden

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

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
