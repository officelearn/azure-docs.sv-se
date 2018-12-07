---
title: Azure Queue storage-bindningar för Azure Functions
description: Förstå hur du använder Azure Queue storage-utlösare och -utdatabindning i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 565b4c66c98d62fdcbd23f6446f522b810db1430
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999461"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Queue storage-bindningar i Azure Functions. Azure Functions stöder Utlös och utdatabindningar för köer.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Queue storage-bindningar finns i den [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Queue storage-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Använd kö-utlösare för att starta en funktion när ett nytt objekt tas emot i en kö. Kömeddelandet anges som indata till funktionen.

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---Java-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som avsöker den `myqueue-items` kön och skriver en logg varje gång som ett jobbkö-objekt har bearbetats.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en köutlösare bindning i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningen. Den funktion avsöker den `myqueue-items` kön och skriver en logg varje gång som ett jobbkö-objekt har bearbetats.

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

Här är C#-skriptkoden:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

Den [användning](#trigger---usage) förklaras `myQueueItem`, som är namngiven av den `name` -egenskapen i function.json.  Den [meddelande metadataavsnittet](#trigger---message-metadata) förklarar alla andra variabler visas.

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en köutlösare bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Den funktion avsöker den `myqueue-items` kön och skriver en logg varje gång som ett jobbkö-objekt har bearbetats.

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

> [!NOTE]
> Parametern name återspeglar som `context.bindings.<name>` i JavaScript-kod som innehåller nyttolasten för objekt i kö. Den här nyttolasten skickas också som den andra parametern för funktionen.

Här är JavaScript-kod:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
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

Den [användning](#trigger---usage) förklaras `myQueueItem`, som är namngiven av den `name` -egenskapen i function.json.  Den [meddelande metadataavsnittet](#trigger---message-metadata) förklarar alla andra variabler visas.

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

Java-exemplet nedan visar en queue storage-utlösare-funktioner som loggar utlösta meddelande placeras i kö `myqueuename`.
 
 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

## <a name="trigger---attributes"></a>Utlösare - attribut
 
I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en kö-utlösare:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Attributets konstruktorn tar namnet på kön som ska övervakas, som visas i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Du kan ange den `Connection` egenskapen att ange storage-konto du använder, enligt följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```
 
  Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Innehåller ett annat sätt att ange konto för att använda. Konstruktorn tar namnet på en appinställning som innehåller en anslutningssträng för lagring. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

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
* Den `StorageAccount` attribut som tillämpas på klassen.
* Appinställningen ”AzureWebJobsStorage”.

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `QueueTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas| Måste anges till `queueTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning**| Saknas | I den *function.json* bara fil. Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas |Namnet på variabeln som innehåller nyttolasten för objekt i kö i funktionskoden.  | 
|**Könamn** | **Könamn**| Namnet på kön att söka. | 
|**anslutning** | **anslutning** |Namnet på en appinställning som innehåller lagringsanslutningssträngen ska användas för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Exempel: Om du ställer in `connection` till ”MyStorage” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom funktionskörningen använder standard Storage anslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning
 
Få åtkomst till meddelandedata i C# och C#-skript, genom att använda en metodparameter som `string paramName`. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. Du kan binda till någon av följande typer:

* Objekt - Functions-körning deserializes en JSON-nyttolast i en instans av en godtyckligt klass som definierats i din kod. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker binda till `CloudQueueMessage` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

I JavaScript, använda `context.bindings.<name>` till nyttolasten för objekt i kö. Om nyttolasten är JSON, är det deserialisera placeras i ett objekt.

## <a name="trigger---message-metadata"></a>Utlösare - meddelande metadata

Kö-utlösare innehåller flera [metadataegenskaper](functions-triggers-bindings.md#binding-expressions---trigger-metadata). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa är egenskaper för den [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) klass.

|Egenskap |Typ|Beskrivning|
|--------|----|-----------|
|`QueueTrigger`|`string`|Kön nyttolast (om det är en giltig sträng). Om kön nyttolast som en sträng `QueueTrigger` har samma värde som variabeln med namnet genom att den `name` -egenskapen i *function.json*.|
|`DequeueCount`|`int`|Antal gånger som har tagits bort från kön det här meddelandet.|
|`ExpirationTime`|`DateTimeOffset`|Den tid som meddelandet upphör att gälla.|
|`Id`|`string`|Kön meddelande-ID.|
|`InsertionTime`|`DateTimeOffset`|Den tid som meddelandet har lagts till i kön.|
|`NextVisibleTime`|`DateTimeOffset`|Den tid som meddelandet ska sedan vara synliga.|
|`PopReceipt`|`string`|Meddelandets pop-kvitto.|

## <a name="trigger---poison-messages"></a>Utlösare – skadliga meddelanden

När en kö utlösningsfunktion misslyckas, försöker Azure Functions funktionen upp till fem gånger för en given kö-meddelande, inklusive första försöket. Om alla fem försök misslyckas, funktionskörningen lägger till ett meddelande till en kö med namnet  *&lt;originalqueuename >-skadliga*. Du kan skriva en funktion du bearbetar meddelanden från skadliga kön av loggning av dem eller skicka ett meddelande till den manuella åtgärder krävs.

För att hantera skadliga meddelanden manuellt, kontrollera den [dequeueCount](#trigger---message-metadata) av kömeddelandet.

## <a name="trigger---polling-algorithm"></a>Utlösare - avsökning algoritmen

Kö-utlösare implementerar en exponentiell backoff-algoritmen för att minska effekten av idle-kön avsökning lagringskostnader för transaktionen.  När ett meddelande hittas av runtime väntar två sekunder och söker efter en annan message; När inget meddelande hittas väntar ungefär fyra sekunder innan du försöker igen. Väntetiden fortsätter att öka tills den når maximal väntetid, där standardinställningen är en minut efter flera misslyckade försök att hämta ett kömeddelande. Maximal väntetid kan konfigureras via den `maxPollingInterval` -egenskapen i den [host.json filen](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Utlösare - samtidighet

När det finns flera Kömeddelanden som väntar på, kö-utlösaren hämtar en grupp med meddelanden och anropar funktionen instanser samtidigt för att bearbeta dem på. Som standard är batchstorleken 16. När antalet bearbetas hämtar till 8, hämtar en annan batch körningen och börjar behandla meddelandena. Så det maximala antalet samtidiga meddelanden som bearbetas per funktion på en virtuell dator (VM) är 24. Den här gränsen gäller separat för varje kö-utlöst funktion på varje virtuell dator. Om din funktionsapp skalas ut till flera virtuella datorer, kommer varje virtuell dator vänta utlösare och försök att köra functions. Om en funktionsapp skalas upp till 3 virtuella datorer, till exempel är 72 standard maxantalet samtidiga instanser av en funktion som utlöses av kön.

Batchstorlek och tröskelvärdet för att få en ny batch kan konfigureras i den [host.json filen](functions-host-json.md#queues). Om du vill minimera parallell körning för kö-utlösta funktioner i en funktionsapp kan du ange batchstorleken till 1. Den här inställningen eliminerar samtidighet bara så länge som din funktionsapp körs på en enskild virtuell dator (VM). 

Kö-utlösare förhindrar automatiskt en funktion från att bearbeta ett kömeddelande flera gånger. Functions behöver inte skrivas till vara idempotenta.

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

Den [host.json](functions-host-json.md#queues) filen innehåller inställningar som styr beteendet för kö-utlösare.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Resultat

Använd den Azure Queue storage-utdatabindning för att skriva meddelanden till en kö.

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skapar ett kömeddelande för varje HTTP-begäran tas emot.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

I följande exempel visas en HTTP-utlösare bindning i en *function.json* fil och [C#-skript (.csx)](functions-reference-csharp.md) kod som använder bindningen. Funktionen skapar ett köobjekt med en **CustomQueueMessage** objekt nyttolast för varje HTTP-begäran tas emot.

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

Här är C#-skriptkoden som skapar ett enda kömeddelande:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Du kan skicka flera meddelanden samtidigt med hjälp av en `ICollector` eller `IAsyncCollector` parametern. Här är C#-skriptkoden som skickar flera meddelanden, en med HTTP-begärandedata och en med hårdkodade värden:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

I följande exempel visas en HTTP-utlösare bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skapar en jobbkö-objekt för varje HTTP-begäran tas emot.

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

Du kan skicka flera meddelanden samtidigt genom att definiera en meddelande-matris för den `myQueueItem` utdatabindning. Följande JavaScript-koden skickar två Kömeddelanden med hårdkodade värden för varje HTTP-begäran tas emot.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Resultat – Java-exemplet

 I följande exempel visas en Java-funktion som skapar ett kömeddelande för när det utlöses av en HTTP-begäran.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
 ```

I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@QueueOutput` anteckning om parametrar vars värde skulle skrivas till Queue storage.  Parametertypen ska vara `OutputBinding<T>`, där T är alla interna Java-typer av en POJO.


## <a name="output---attributes"></a>Utdata - attribut
 
I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Attributet som gäller för en `out` parametern eller returvärdet för funktionen. Attributets konstruktorn tar namnet på kön som du ser i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Du kan ange den `Connection` egenskapen att ange storage-konto du använder, enligt följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

Du kan använda den `StorageAccount` attribut för att ange storage-konto på klass, metod eller parametern-nivå. Mer information finns i [utlösare - attribut](#trigger---attribute).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Queue` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `queue`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `out`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön i funktionskoden. Ange `$return` att referera till returvärde för funktion.| 
|**Könamn** |**Könamn** | Köns namn. | 
|**anslutning** | **anslutning** |Namnet på en appinställning som innehåller lagringsanslutningssträngen ska användas för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av det här namnet. Exempel: Om du ställer in `connection` till ”MyStorage” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyStorage”. Om du lämnar `connection` tom funktionskörningen använder standard Storage anslutningssträngen i appinställningen som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning
 
I C# och C#-skript, skriver du ett enda kömeddelande genom att använda en metodparameter som `out T paramName`. I C#-skript, `paramName` har värdet som angetts i den `name` egenskapen för *function.json*. Du kan använda metoden returtypen i stället för en `out` parametern och `T` kan vara något av följande typer:

* Ett objekt som serializable som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och får ett felmeddelande, se till att du har en referens till [rätt Storage SDK version](#azure-storage-sdk-version-in-functions-1x).

I C# och C#-skript, skriva flera Kömeddelanden med någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

I JavaScript-funktioner använder `context.bindings.<name>` att komma åt kömeddelandet utdata. Du kan använda en sträng eller ett JSON-serialiserbara objekt för nyttolasten för objekt i kö.


## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Kö | [Felkoder för kö](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tabell, kö | [Felkoder för lagring](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö |  [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

Det här avsnittet beskrivs de globala konfigurationsinställningarna som är tillgängliga för den här bindningen i version 2.x. Host.json-exempelfilen nedan innehåller bara till version 2.x inställningarna för den här bindningen. Mer information om konfigurationsinställningar i version 2.x kan se [host.json-referens för Azure Functions version 2.x](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```  


|Egenskap   |Standard | Beskrivning |
|---------|---------|---------| 
|maxPollingInterval|00:00:02|Den längsta tiden mellan kön avsöker. Minimum är 00:00:00.100 (100 ms). | 
|visibilityTimeout|00:00:00|Det går inte att tidsintervall mellan försök vid bearbetning av ett meddelande. | 
|batchSize|16|Antal Kömeddelanden som Functions-körning hämtar samtidigt och bearbetar parallellt. När antalet bearbetas kommer ned till den `newBatchThreshold`, körningen får en annan batch och påbörjar bearbetningen av dessa meddelanden. Så det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold`. Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning för meddelanden som tas emot i en kö kan du ange `batchSize` till 1. Den här inställningen eliminerar dock samtidighet bara så länge som din funktionsapp körs på en enskild virtuell dator (VM). Om funktionsappen skalas ut till flera virtuella datorer, kan varje virtuell dator kör en instans av varje funktion som utlöses av kön.<br><br>Maximalt `batchSize` är 32. | 
|maxDequeueCount|5|Antal gånger att försöka bearbetar ett meddelande innan du flyttar den till skadliga kön.| 
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetades samtidigt hamnar till det här talet, hämtar körningen en annan batch.| 

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Gå till en självstudiekurs som använder en Queue storage-utdatabindning](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
