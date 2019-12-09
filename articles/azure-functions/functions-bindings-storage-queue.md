---
title: Azure Queue Storage-bindningar för Azure Functions
description: Lär dig hur du använder Azure Queue Storage-utlösare och utgående bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 3e72bd366cdbba1d73bc05f98d3848e2d4f0ca6c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925340"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue Storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med lagrings bindningar i Azure queue i Azure Functions. Azure Functions stöder utlösare och utgående bindningar för köer.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Queue Storage-bindningarna finns i [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

Kö lagrings bindningar finns i [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3. x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
Functions förväntar sig en *base64* -kodad sträng. Eventuella justeringar av kodnings typen (för att förbereda data som en *base64* -kodad sträng) måste implementeras i anrops tjänsten.

## <a name="trigger"></a>Utlösare

Använd kön utlösare för att starta en funktion när ett nytt objekt tas emot i en kö. Kömeddelandet anges som indata till funktionen.

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som söker i `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

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

I följande exempel visas en kö trigger-bindning i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningen. Funktionen avsöker `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

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

I [användnings](#trigger---usage) avsnittet förklaras `myQueueItem`, som namnges av egenskapen `name` i function. JSON.  I [avsnittet meddelande metadata](#trigger---message-metadata) beskrivs alla andra variabler som visas.

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en kö trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen avsöker `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

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
> Parametern name visar `context.bindings.<name>` i JavaScript-koden som innehåller objektets nytto Last. Den här nytto lasten skickas också som den andra parametern till funktionen.

Här är JavaScript-kod:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

I [användnings](#trigger---usage) avsnittet förklaras `myQueueItem`, som namnges av egenskapen `name` i function. JSON.  I [avsnittet meddelande metadata](#trigger---message-metadata) beskrivs alla andra variabler som visas.

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

I följande Java-exempel visas en utlösnings funktion i Storage Queue som loggar det Utlös ande meddelandet som placeras i kö `myqueuename`.

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

### <a name="trigger---python-example"></a>Utlös – python-exempel

Följande exempel visar hur du läser ett Queue-meddelande som skickas till en funktion via en utlösare.

En utlösare för lagrings kön definieras i *Function. JSON* där *typ* har angetts till `queueTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Koden  *_\_init_\_. py* deklarerar en parameter som `func.ServiceBusMessage` så att du kan läsa meddelandet i kön i din funktion.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Utlösare - attribut

I [ C# klass bibliotek](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en Queue-utlösare:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Attributets konstruktor tar namnet på kön som ska övervakas, som visas i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Du kan ställa in egenskapen `Connection` för att ange det lagrings konto som ska användas, som du ser i följande exempel:

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

  Ger ett annat sätt att ange det lagrings konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en lagrings anslutnings sträng. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

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

Lagrings kontot som ska användas fastställs i följande ordning:

* Den `QueueTrigger` attributets `Connection` egenskapen.
* Den `StorageAccount` attribut som används i samma parameter som den `QueueTrigger` attribut.
* Den `StorageAccount` attribut som används i funktionen.
* Den `StorageAccount` attribut som tillämpas på klassen.
* App-inställningen "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `QueueTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt| Måste anges till `queueTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning**| Ej tillämpligt | Endast i *Function. JSON* -filen. Måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Ej tillämpligt |Namnet på den variabel som innehåller köns objektets nytto Last i funktions koden.  |
|**Könamn** | **Könamn**| Namnet på kön som ska avsökas. |
|**anslutning** | **Anslutning** |Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

I C# och C# skript kan du komma åt meddelande data med hjälp av en metod parameter som `string paramName`. I C# skript är `paramName` det värde som anges i egenskapen `name` för *Function. JSON*. Du kan binda till någon av följande typer:

* Objekt-funktions körningen deserialiserar en JSON-nyttolast till en instans av en godtycklig klass som definierats i din kod. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

Använd `context.bindings.<name>` för att komma åt nytto lasten för köobjekt i Java Script. Om nytto lasten är JSON deserialiseras den till ett objekt.

## <a name="trigger---message-metadata"></a>Utlösare - meddelande metadata

Utlösaren för kön innehåller flera [Egenskaper för metadata](./functions-bindings-expressions-patterns.md#trigger-metadata). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa är egenskaper för klassen [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`QueueTrigger`|`string`|Köns nytto Last (om en giltig sträng). Om meddelandet nytto Last i kön är en sträng, `QueueTrigger` har samma värde som variabeln som namnges av egenskapen `name` i *Function. JSON*.|
|`DequeueCount`|`int`|Antal gånger som det här meddelandet har tagits ur kö.|
|`ExpirationTime`|`DateTimeOffset`|Tiden som meddelandet upphör att gälla.|
|`Id`|`string`|Meddelande-ID för kö.|
|`InsertionTime`|`DateTimeOffset`|Den tidpunkt då meddelandet lades till i kön.|
|`NextVisibleTime`|`DateTimeOffset`|Tiden då meddelandet kommer att visas nästa.|
|`PopReceipt`|`string`|Meddelandets pop-kvitto.|

## <a name="trigger---poison-messages"></a>Utlösare – skadliga meddelanden

Om en utlösare funktion Miss lyckas försöker Azure Functions utföra funktionen upp till fem gånger för ett angivet Queue-meddelande, inklusive det första försöket. Om alla fem försöken Miss lyckas lägger Functions runtime till ett meddelande i en kö med namnet *&lt;originalqueuename >-Poison*. Du kan skriva en funktion för att bearbeta meddelanden från den Poison-kön genom att logga dem eller skicka ett meddelande om att det krävs en manuell åtgärd.

Om du vill hantera Poison-meddelanden manuellt kontrollerar du [dequeueCount](#trigger---message-metadata) i Queue-meddelandet.

## <a name="trigger---polling-algorithm"></a>Utlös ande algoritm för avsökning

Utlösaren för kön implementerar en slumpmässig algoritm för att ta bort inaktivt kön på lagrings transaktions kostnader.  När ett meddelande hittas väntar körningen i två sekunder och söker sedan efter ett annat meddelande. När inget meddelande hittas väntar det cirka fyra sekunder innan det försöker igen. Efter efterföljande misslyckade försök att hämta ett Queue-meddelande fortsätter vänte tiden att öka tills den når den maximala vänte tiden, vilket är en minut som standard. Maximal vänte tid kan konfigureras via `maxPollingInterval`-egenskapen i [Host. JSON-filen](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Utlösare – samtidighets läge

När det finns flera köa meddelanden som väntar, hämtar kön utlösare en batch med meddelanden och anropar funktions instanserna samtidigt för att bearbeta dem. Som standard är batchstorleken 16. När antalet som bearbetas går ned till 8, får körningen en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion på en virtuell dator (VM) är 24. Den här gränsen gäller separat för varje funktion som utlöses av kön på varje virtuell dator. Om din Function-app skalar ut till flera virtuella datorer väntar varje virtuell dator på utlösare och försöker köra funktioner. Om till exempel en Function-app skalar ut till tre virtuella datorer är det maximala antalet samtidiga instanser av en funktion som utlöses av en kö 72.

Batchstorleken och tröskelvärdet för att hämta en ny batch kan konfigureras i [Host. JSON-filen](functions-host-json.md#queues). Om du vill minimera parallell körning för köade funktioner i en Function-app kan du ange batchstorleken till 1. Med den här inställningen elimineras samtidighet endast så länge din funktions program körs på en enda virtuell dator (VM). 

Queue-utlösaren förhindrar automatiskt en funktion från att bearbeta ett köobjekt flera gånger; funktioner behöver inte skrivas för att vara idempotenta.

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

[Host. JSON](functions-host-json.md#queues) -filen innehåller inställningar som styr beteendet för kön utlösare. Se avsnittet [Host. JSON-inställningar](#hostjson-settings) för information om tillgängliga inställningar.

## <a name="output"></a>Resultat

Använd bindningen för lagring av utdata i Azure Queue för att skriva meddelanden till en kö.

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som skapar ett Queue-meddelande för varje http-begäran som tas emot.

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

I följande exempel visas en http trigger-bindning i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningen. Funktionen skapar ett köobjekt med en **CustomQueueMessage** objekt nytto last för varje http-begäran som tas emot.

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
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Den [configuration](#output---configuration) förklaras de här egenskaperna.

Här är C# skript koden som skapar ett enda köat meddelande:

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

Du kan skicka flera meddelanden samtidigt med hjälp av en `ICollector`-eller `IAsyncCollector`-parameter. Här är C# skript koden som skickar flera meddelanden, en med HTTP-begärans data och en med hårdkodade värden:

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

I följande exempel visas en HTTP trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skapar ett köobjekt för varje HTTP-begäran som tas emot.

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
      "connection": "MyStorageConnectionAppSetting"
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

Du kan skicka flera meddelanden samtidigt genom att definiera en meddelande mat ris för `myQueueItem` utgående bindning. Följande JavaScript-kod skickar två köa meddelanden med hårdkodade värden för varje HTTP-begäran som tas emot.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Resultat – Java-exemplet

 I följande exempel visas en Java-funktion som skapar ett Queue-meddelande för när den utlöses av en HTTP-begäran.

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

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@QueueOutput` kommentar för parametrar vars värde ska skrivas till Queue Storage.  Parametertypen ska vara `OutputBinding<T>`, där T är alla interna Java-typer av en POJO.

### <a name="output---python-example"></a>Utdata – python-exempel

I följande exempel visas hur du matar utdata av enstaka och flera värden till lagrings köer. Konfigurationen som krävs för *Function. JSON* är på samma sätt.

En bindning för lagrings kön definieras i *Function. JSON* där *typ* har angetts till `queue`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Om du vill ange ett enskilt meddelande i kön skickar du ett enda värde till metoden `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Om du vill skapa flera meddelanden i kön, deklarera en parameter som lämplig typ av lista och skicka en matris med värden (som matchar List typen) till `set` metoden.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

## <a name="output---attributes"></a>Utdata - attribut

Använd [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributet gäller för en `out` parameter eller returvärdet för funktionen. Attributets konstruktor tar köns namn, som visas i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Du kan ställa in egenskapen `Connection` för att ange det lagrings konto som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

Du kan använda attributet `StorageAccount` för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om Utlösar-attribut.

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `Queue` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste anges till `queue`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Ej tillämpligt | Måste anges till `out`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar kön i funktions koden. Ange till `$return` för att referera till funktionens retur värde.|
|**Könamn** |**Könamn** | Köns namn. |
|**anslutning** | **Anslutning** |Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyStorage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I C# och C# skript skriver du ett enskilt Queue-meddelande med hjälp av en metod parameter som `out T paramName`. I C# skript är `paramName` det värde som anges i egenskapen `name` för *Function. JSON*. Du kan använda metoden Return-typ i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt som är serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

I C# och C# skript skriver du flera meddelanden i kön genom att använda någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

Använd `context.bindings.<name>` för att få åtkomst till meddelandet utgående kö i JavaScript-funktioner. Du kan använda en sträng eller ett JSON-serialiserbar objekt för nytto lasten i objektet.


## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Kö | [Fel koder för kö](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tabell, kö | [Lagrings fel koder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö |  [Troubleshooting](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) (Felsökning) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet Host. JSON-filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i version 2. x och mer finns i [Host. JSON-referens för Azure Functions](functions-host-json.md).

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


|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Det maximala intervallet mellan Queue-avsökningar. Minimum är 00:00:00.100 (100 MS) och ökar till 00:01:00 (1 min).  I 1. x är data typen millisekunder och i 2. x och högre är det ett TimeSpan.|
|visibilityTimeout|00:00:00|Tidsintervall mellan återförsök vid bearbetning av ett meddelande Miss lyckas. |
|batchSize|16|Antalet köa meddelanden som funktions körningen hämtar samtidigt och processer parallellt. När antalet som bearbetas går ned till `newBatchThreshold`, hämtar kör tiden en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold`. Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning av meddelanden som tas emot i en kö kan du ange `batchSize` till 1. Den här inställningen eliminerar dock ingen samtidighet så länge som din funktions App körs på en enda virtuell dator (VM). Om Function-appen skalar ut till flera virtuella datorer kan varje virtuell dator köra en instans av varje funktion som utlöses av kön.<br><br>Högsta `batchSize` är 32. |
|maxDequeueCount|5|Antal försök att bearbeta ett meddelande innan det flyttas till en Poison-kö.|
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt går till det här talet, hämtar körningen en annan batch.|

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Gå till en självstudie som använder en kö Storage utgående bindning](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
