---
title: "Azure Functions kö lagring bindningar | Microsoft Docs"
description: "Förstå hur du använder Azure Storage-utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.openlocfilehash: b68ce106ceb25d19ee0bbde287891d553a448560
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions Queue Storage bindningar
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Den här artikeln beskriver hur du konfigurerar och koden Azure Queue storage bindningar i Azure Functions. Azure Functions stöder utlösa och utgående bindningar för Azure köer. Funktioner som är tillgängliga i alla bindningar finns [Azure Functions-utlösare och bindningar begrepp](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Queue storage utlösare
Azure Queue storage-utlösare kan du övervaka en kö lagring för nya meddelanden och ta hänsyn till dem. 

Definierar en kö utlösaren med hjälp av den **integrera** fliken i Functions-portalen. Portalen skapar följande definitionen i den **bindningar** avsnitt i *function.json*:

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* Den `connection` egenskapen måste innehålla namnet på en appinställning som innehåller en anslutningssträng för lagring. I Azure-portalen standardredigeraren i den **integrera** appinställningen du konfigurerar när du väljer ett lagringskonto.

Fler inställningar kan anges i en [host.json filen](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) att finjustera lagring-utlösare. Du kan till exempel ändra kön avsökningsintervall i host.json.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>Med hjälp av en kö-utlösare
I Node.js-funktion, få åtkomst till kön data med `context.bindings.<name>`.


I .NET-funktioner, åtkomst till kön nyttolasten med en metodparameter `CloudQueueMessage paramName`. Här, `paramName` är det värde som du angav i den [konfiguration av utlösare](#trigger). Kömeddelandet kan avserialiseras till någon av följande typer:

* POCO-objekt. Använd om kön nyttolasten är en JSON-objekt. Functions-runtime deserializes nyttolast i POCO-objektet. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>Kö utlösaren metadata
Kö utlösaren innehåller flera metadataegenskaper för. De här egenskaperna kan användas som en del av bindande uttryck i andra bindningar eller parametrar i din kod. Värden har samma semantik som [ `CloudQueueMessage` ].

* **QueueTrigger** -kön nyttolasten (om det är en giltig sträng)
* **DequeueCount** -typen `int`. Antal gånger som det här meddelandet har har tagits bort.
* **ExpirationTime** -typen `DateTimeOffset?`. Den tid då meddelandet upphör att gälla.
* **ID** -typen `string`. Kön meddelande-ID.
* **InsertionTime** -typen `DateTimeOffset?`. Den tidpunkt som meddelandet har lagts till i kön.
* **NextVisibleTime** -typen `DateTimeOffset?`. Den tidpunkt som meddelandet visas bredvid.
* **PopReceipt** -typen `string`. Meddelandets pop inleverans.

Använda metadata i kö [utlösaren exempel](#triggersample).

<a name="triggersample"></a>

## <a name="trigger-sample"></a>Utlösaren exempel
Anta att du har följande function.json som definierar en kö-utlösare:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

Se exemplet språkspecifika som hämtar och loggar kö metadata.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Utlösaren exemplet i C# #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Utlösaren exemplet i Node.js

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>Hantering av skadligt Kömeddelanden
När en kö Utlösarfunktion inte återförsök Azure Functions funktionen upp till fem gånger för en viss kö-meddelande, inklusive första försöket. Om alla fem försök misslyckas functions-runtime lägger till ett meddelande till en queue storage med namnet  *&lt;originalqueuename >-skadligt*. Du kan skriva en funktion för att bearbeta meddelanden från skadligt kön av loggning av dem eller skicka ett meddelande till den manuella åtgärder krävs. 

Om du vill hantera förgiftade meddelanden manuellt, kontrollera den `dequeueCount` i kön meddelandet (se [kö utlösaren metadata](#meta)).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Queue storage-utdatabindning
Azure queue storage utdata bindning kan du skriva meddelanden till en kö. 

Definierar en kö utdata bindning med hjälp av den **integrera** fliken i Functions-portalen. Portalen skapar följande definitionen i den **bindningar** avsnitt i *function.json*:

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* Den `connection` egenskapen måste innehålla namnet på en appinställning som innehåller en anslutningssträng för lagring. I Azure-portalen standardredigeraren i den **integrera** appinställningen du konfigurerar när du väljer ett lagringskonto.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>Med hjälp av en kö utdatabindning
I Node.js-funktion, du åtkomst till den utgående kön med hjälp av `context.bindings.<name>`.

Du kan spara till någon av följande typer i .NET-funktioner. När det är en parameter av typen `T`, `T` måste vara något av stöds utdata-typer som `string` eller en POCO.

* `out T`(serialiserad som JSON)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

Du kan också använda metodens returtyp som utdata-bindning.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>Kön utdata-exempel
Följande *function.json* definierar en HTTP-utlösare med en kö utdatabindning:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

Se exemplet språkspecifika som matar ut ett kömeddelande med den inkommande HTTP-nyttolasten.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>Kön utdata exemplet i C# #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Om du vill skicka flera meddelanden, använda en `ICollector`:

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Kön utdata exemplet i Node.js

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Eller, för att skicka flera meddelanden

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>Nästa steg

Ett exempel på en funktion som använder lagring-utlösare och bindningar finns [skapa en funktion som utlöses av Azure Queue storage](functions-create-storage-queue-triggered-function.md).

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

['CloudQueueMessage']: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
