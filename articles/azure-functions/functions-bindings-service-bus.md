---
title: "Azure Functions Service Bus-utlösare och bindningar | Microsoft Docs"
description: "Förstå hur du använder Azure Service Bus-utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 71149aaacc940a62e085cf1ce103a0214d05bd1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-service-bus-bindings"></a>Azure Functions Service Bus-bindningar
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln beskriver hur du konfigurerar och arbetar med Azure Service Bus-bindningar i Azure Functions. 

Azure Functions stöder utlösa och utgående bindningar för Service Bus-köer och ämnen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Service Bus-utlösare
Använda Service Bus-utlösaren ska svara på meddelanden från en Service Bus-kö eller ett ämne. 

Service Bus-kö och avsnittet utlösare definieras av följande JSON-objekt i den `bindings` matris med function.json:

* *kön* utlösare:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* *avsnittet* utlösare:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

Observera följande:

* För `connection`, [skapa en appinställningen i appen funktionen](functions-how-to-use-azure-function-app-settings.md) som innehåller anslutningssträngen till Service Bus-namnrymd, ange namnet på appinställningen i den `connection` egenskap i utlösaren. Du hämta anslutningssträngen genom att följa stegen som visas vid [hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  Anslutningssträngen måste vara för Service Bus-namnrymd inte begränsat till en särskild kö eller ett ämne.
  Om du lämnar `connection` tom utlösaren förutsätter att en standard Service Bus-anslutningssträng har angetts i en app inställning med namnet `AzureWebJobsServiceBus`.
* För `accessRights`, tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** , behörighetsgrupp `accessRights` till `listen`. Annars kan hantera funktionerna runtime misslyckas försöker att utföra åtgärder som kräver rättigheter.

## <a name="trigger-behavior"></a>Utlösaren beteende
* **Single-threading** – som standard funktioner runtime processer flera meddelanden samtidigt. För att dirigera runtime att bearbeta en enskild kö eller ett ämne meddelande i taget ange `serviceBus.maxConcurrentCalls` 1 i *host.json*. 
  Information om *host.json*, se [mappstrukturen](functions-reference.md#folder-structure) och [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json).
* **Hantering av förgiftade meddelanden** -Service Bus har sin egen hantering av skadligt meddelande som inte styrs eller konfigurerats i Azure Functions konfiguration eller kod. 
* **PeekLock beteende** -Functions-runtime tar emot ett meddelande i [ `PeekLock` läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) och anropar `Complete` på meddelandet om funktionen slutförs eller samtal `Abandon` om misslyckas åtgärden. 
  Om funktionen körs längre än den `PeekLock` timeout låset förnyas automatiskt.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Utlösaren användning
Det här avsnittet visar hur du använder Service Bus-utlösare i funktionskoden. 

I C# och F #, kan Service Bus utlösaren meddelandet avserialiseras till någon av följande typer av inkommande:

* `string`-användbart för sträng meddelanden
* `byte[]`-användbart för binära data
* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) – det är användbart för JSON-serialiserade data.
  Om du exempelvis deklarera en anpassad Indatatyp `CustomType`, Azure Functions görs ett försök att deserialisera JSON-data till den angivna typen.
* `BrokeredMessage`– ger dig avserialiserat meddelandet med den [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) metod.

I Node.js skickas Service Bus utlösaren meddelandet till funktionen som en sträng eller en JSON-objekt.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Utlösaren exempel
Anta att du har följande function.json:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Se exemplet språkspecifika som bearbetar ett meddelande för Service Bus-kö.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Utlösaren exemplet i C# #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Utlösaren exemplet i F # #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Utlösaren exemplet i Node.js

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Service Bus-utdatabindning
Service Bus-kö och avsnittet utdata för en funktion använder följande JSON-objekt i den `bindings` matris med function.json:

* *kön* utdata:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* *avsnittet* utdata:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

Observera följande:

* För `connection`, [skapa en appinställningen i appen funktionen](functions-how-to-use-azure-function-app-settings.md) som innehåller anslutningssträngen till Service Bus-namnrymd, ange namnet på appinställningen i den `connection` egenskapen i utdata-bindning. Du hämta anslutningssträngen genom att följa stegen som visas vid [hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials).
  Anslutningssträngen måste vara för Service Bus-namnrymd inte begränsat till en särskild kö eller ett ämne.
  Om du lämnar `connection` tom utdata bindningen förutsätter att en standard Service Bus-anslutningssträng har angetts i en app inställning med namnet `AzureWebJobsServiceBus`.
* För `accessRights`, tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** , behörighetsgrupp `accessRights` till `listen`. Annars kan hantera funktionerna runtime misslyckas försöker att utföra åtgärder som kräver rättigheter.

<a name="outputusage"></a>

## <a name="output-usage"></a>Användning av utdata
I C# och F #, kan Azure Functions skapa ett Service Bus-kö-meddelande från någon av följande typer:

* Alla [objekt](https://msdn.microsoft.com/library/system.object.aspx) -parameterdefinitionen ser ut som `out T paramName` (C#).
  Funktioner deserializes objektet till en JSON-meddelande. Om värdet är null när funktionen avslutas skapar funktioner meddelandet med ett null-objekt.
* `string`-Parameterdefinitionen ser ut som `out string paraName` (C#). Om parametervärdet är icke-null när funktionen avslutas, skapar ett meddelande i funktioner.
* `byte[]`-Parameterdefinitionen ser ut som `out byte[] paraName` (C#). Om parametervärdet är icke-null när funktionen avslutas, skapar ett meddelande i funktioner.
* `BrokeredMessage`Parameterdefinitionen ser ut som `out BrokeredMessage paraName` (C#). Om parametervärdet är icke-null när funktionen avslutas, skapar ett meddelande i funktioner.

Du kan använda för att skapa flera meddelanden i en C#-funktion, `ICollector<T>` eller `IAsyncCollector<T>`. Skapa ett meddelande när du anropar den `Add` metoden.

I Node.js, du kan tilldela en sträng, en bytematris eller ett Javascript-objekt (deserialisera till JSON) `context.binding.<paramName>`.

<a name="outputsample"></a>

## <a name="output-sample"></a>Exempel på utdata
Anta att du har följande function.json, som definierar en Service Bus-kö utdata:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Finns det språkspecifika exempel som skickar ett meddelande till service bus-kö.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Utdata exemplet i C# #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Eller skapa flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Utdata exemplet i F # #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Utdata exemplet i Node.js

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Eller skapa flera meddelanden:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

