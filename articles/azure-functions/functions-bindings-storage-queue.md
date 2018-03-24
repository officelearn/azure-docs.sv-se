---
title: Azure Queue storage bindningar för Azure Functions
description: Förstå hur du använder Azure Queue storage utlösaren och utdatabindning i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: eff36b6628c86038b3d08bf3cfd32a6322508baa
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue storage bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Queue storage bindningar i Azure Functions. Azure Functions stöder utlösa och utgående bindningar för köer.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Paket

Queue storage-bindningar finns i den [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet. Källkoden för paketet är i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Utlösare

Använd kö utlösaren för att starta en funktion när ett nytt objekt tas emot på en kö. Kömeddelandet har angetts som indata för funktionen.

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Utlösaren - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som avsöker den `myqueue-items` kö och skriver en logg varje gång ett köobjekt bearbetas.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Utlösaren - exempel på C#-skript

I följande exempel visas en kö utlösare bindning i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningen. Funktionen avsökningar i `myqueue-items` kö och skriver en logg varje gång ett köobjekt bearbetas.

Här är den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är skriptkod C#:

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

Den [användning](#trigger---usage) förklaras `myQueueItem`, som är namngiven av den `name` egenskap i function.json.  Den [meddelande avsnitt med metadata](#trigger---message-metadata) förklarar alla andra variabler som visas.

### <a name="trigger---javascript-example"></a>Utlösaren - JavaScript-exempel

I följande exempel visas en kö utlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen avsökningar i `myqueue-items` kö och skriver en logg varje gång ett köobjekt bearbetas.

Här är den *function.json* fil:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

Den [användning](#trigger---usage) förklaras `myQueueItem`, som är namngiven av den `name` egenskap i function.json.  Den [meddelande avsnitt med metadata](#trigger---message-metadata) förklarar alla andra variabler som visas.

## <a name="trigger---attributes"></a>Utlösaren - attribut
 
I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en kö-utlösare:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Attributets konstruktorn har namnet på kön att övervaka, som visas i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Du kan ange den `Connection` att ange storage-konto du använder, enligt följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  En komplett exempel finns [utlösaren - C#-exempel](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Ger ett annat sätt att ange storage-konto som ska användas. Konstruktorn får samma namn som en appinställning som innehåller en anslutningssträng för lagring. Attributet kan användas i parametern, metoden eller klassnivå. I följande exempel visas klassnivå och metoden:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Storage-konto du använder bestäms i följande ordning:

* Den `QueueTrigger` attributets `Connection` egenskapen.
* Den `StorageAccount` attribut som används i samma parameter som den `QueueTrigger` attribut.
* Den `StorageAccount` attribut som används i funktionen.
* Den `StorageAccount` attribut som används i klassen.
* Inställningen ”AzureWebJobsStorage” app.

## <a name="trigger---configuration"></a>Utlösaren - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `QueueTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas| måste anges till `queueTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**Riktning**| Saknas | I den *function.json* filen endast. måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas |Namnet på variabeln som representerar kön i funktionskoden.  | 
|**queueName** | **Könamn**| Namnet på kön avsöker. | 
|**Anslutning** | **Anslutning** |Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösaren - användning
 
Få åtkomst till meddelandedata i C# och C# skript, med hjälp av en metodparameter som `string paramName`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. Du kan binda till någon av följande typer:

* Objekt - Functions-runtime deserializes en JSON-nyttolast till en instans av en godtyckligt klass som definierats i din kod. 
* `string`
* `byte[]`
* [CloudQueueMessage]

I JavaScript, använda `context.bindings.<name>` att komma åt nyttolasten för objekt i kö. Om nyttolasten är JSON, är den avserialiseras till ett objekt.

## <a name="trigger---message-metadata"></a>Utlösaren - meddelande metadata

Kö utlösaren innehåller flera [metadataegenskaper](functions-triggers-bindings.md#binding-expressions---trigger-metadata). De här egenskaperna kan användas som en del av bindande uttryck i andra bindningar eller parametrar i din kod. Värden har samma semantik som [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage).

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`QueueTrigger`|`string`|Nyttolasten i kön (om det är en giltig sträng). Om kön meddelandet nyttolast som en sträng, `QueueTrigger` har samma värde som namnges av variabeln i `name` egenskap i *function.json*.|
|`DequeueCount`|`int`|Antal gånger som det här meddelandet har har tagits bort.|
|`ExpirationTime`|`DateTimeOffset`|Den tid då meddelandet upphör att gälla.|
|`Id`|`string`|Kön meddelande-ID.|
|`InsertionTime`|`DateTimeOffset`|Den tidpunkt som meddelandet har lagts till i kön.|
|`NextVisibleTime`|`DateTimeOffset`|Den tidpunkt som meddelandet visas bredvid.|
|`PopReceipt`|`string`|Meddelandets pop inleverans.|

## <a name="trigger---poison-messages"></a>Utlösaren - förgiftade meddelanden

När en kö Utlösarfunktion inte återförsök Azure Functions funktionen upp till fem gånger för en viss kö-meddelande, inklusive första försöket. Om alla fem försök misslyckas, functions-runtime läggs ett meddelande till en kö med namnet  *&lt;originalqueuename >-skadligt*. Du kan skriva en funktion för att bearbeta meddelanden från skadligt kön av loggning av dem eller skicka ett meddelande till den manuella åtgärder krävs.

Om du vill hantera förgiftade meddelanden manuellt, kontrollera den [dequeueCount](#trigger---message-metadata) i kön meddelandet.

## <a name="trigger---polling-algorithm"></a>Utlösaren - avsökning algoritm

Kö utlösaren implementerar en exponentiell tillbaka av algoritmen för att minska effekten av inaktiv-kön avsökning på transaktion lagringskostnader.  När ett meddelande hittas körningsmiljön väntar två sekunder och söker efter en annan message; När det finns inget meddelande, väntar på fyra sekunder innan du försöker igen. Väntetiden fortsätter att öka tills väntetiden, där standardinställningen är en minut efter efterföljande misslyckade försök att få ett meddelande i kön. Väntetiden kan konfigureras via den `maxPollingInterval` egenskap i den [host.json filen](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Utlösaren - samtidighet

När det finns flera meddelanden i kö väntar, kö-utlösaren hämtar en grupp med meddelanden och anropar funktionen instanser samtidigt för att bearbeta dem. Som standard är batchstorleken 16. När antalet bearbetas hämtar till 8, hämtar en annan batch körningsmiljön och påbörjar bearbetningen av dessa meddelanden. Därför är det maximala antalet samtidiga meddelanden som bearbetas per funktion på en virtuell dator (VM) 24. Den här gränsen gäller separat för varje funktion som utlöses av kö på varje virtuell dator. Om appen funktionen skalas ut till flera virtuella datorer, vänta utlösare varje virtuell dator och försök att köra funktioner. Om en funktionsapp skalas ut till 3 virtuella datorer, är standard maxantalet samtidiga instanser av en funktion som utlöses av kön 72.

Batchstorleken och tröskelvärdet för att hämta en ny batch kan konfigureras i den [host.json filen](functions-host-json.md#queues). Om du vill minimera parallell körning för kö-utlösta funktioner i en funktionsapp, kan du ange batchstorleken till 1. Den här inställningen eliminerar samtidighet endast så länge funktionen appen körs på en enskild virtuell dator (VM). 

Kö utlösaren förhindrar automatiskt en funktion från att bearbeta ett kömeddelande flera gånger. funktioner har inte att skrivas till vara idempotent.

## <a name="trigger---hostjson-properties"></a>Utlösaren - host.json egenskaper

Den [host.json](functions-host-json.md#queues) filen innehåller inställningar som styr beteendet för kön utlösare.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Resultat

Använda Azure Queue storage utdata bindning för att skriva meddelanden till en kö.

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som skapar ett kömeddelande för alla HTTP-begäranden tas emot.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Utdata - exempel på C#-skript

I följande exempel visas en utlösare för HTTP-bindning i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningen. Funktionen skapar ett köobjekt med en **CustomQueueMessage** objekt nyttolasten för alla HTTP-begäranden tas emot.

Här är den *function.json* fil:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är C# skriptkod som skapar ett enda kömeddelande:

```cs
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

Du kan skicka flera meddelanden samtidigt med hjälp av en `ICollector` eller `IAsyncCollector` parameter. Här följer C# skriptkod som skickar flera meddelanden, en med HTTP-begäran om data och en med hårdkodade värden:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    TraceWriter log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

I följande exempel visas en utlösare för HTTP-bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen skapar ett köobjekt för alla HTTP-begäranden tas emot.

Här är den *function.json* fil:

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Du kan skicka flera meddelanden samtidigt genom att definiera en message-matris för den `myQueueItem` utdatabindning. Följande JavaScript-kod skickar två Kömeddelanden med hårdkodade värden för alla HTTP-begäranden tas emot.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Utdata - attribut
 
I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Attributet gäller för en `out` parametern eller returvärdet för funktionen. Attributets konstruktorn har namnet på kö som det visas i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Du kan ange den `Connection` att ange storage-konto du använder, enligt följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

En komplett exempel finns [utdata - C#-exempel](#output---c-example).

Du kan använda den `StorageAccount` -attribut som anger storage-konto på klass, metoden eller parametern-nivå. Mer information finns i [utlösaren - attribut](#trigger---attribute).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Queue` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | måste anges till `queue`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**Riktning** | Saknas | måste anges till `out`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön i funktionskoden. Ange till `$return` att referera till returvärde för funktion.| 
|**queueName** |**Könamn** | Namnet på kön. | 
|**Anslutning** | **Anslutning** |Namnet på en appinställning som innehåller anslutningssträngen för lagring för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Till exempel om du ställer in `connection` för ”MyStorage” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom Functions-runtime använder standard lagringsanslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning
 
I C# och C# skript, skriver du ett enda kömeddelande med en metodparameter som `out T paramName`. I C# skript `paramName` anges värdet i den `name` -egenskapen för *function.json*. Du kan använda Returtypen för metoden i stället för en `out` parameter, och `T` kan vara något av följande typer:

* Ett objekt som kan serialiseras som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

I C# och C# skript, skriva flera meddelanden i kö med någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

I JavaScript-funktioner, använda `context.bindings.<name>` komma åt det utgående kö. Du kan använda en sträng eller ett JSON-serialiserbara objekt för nyttolasten för objekt i kö.


## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Kö | [Felkoder för kön](https://docs.microsoft.com/en-us/rest/api/storageservices/queue-service-error-codes) |
| Blobb, tabell, kö | [Felkoder för lagring](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blobb, tabell, kö |  [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå till en Snabbstart som använder en utlösare för lagring av kön](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Gå till en kurs som använder en Queue storage-utdatabindning](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
