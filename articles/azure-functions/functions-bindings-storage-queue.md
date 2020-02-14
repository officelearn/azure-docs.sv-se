---
title: Azure Queue Storage-bindningar för Azure Functions
description: Lär dig hur du använder Azure Queue Storage-utlösare och utgående bindning i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 33bdf718e74011dbd7adedd766ebc90923fffb83
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189842"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Queue Storage-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med lagrings bindningar i Azure queue i Azure Functions. Azure Functions stöder utlösare och utgående bindningar för köer.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Queue Storage-bindningarna finns i [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-paketet, version 2. x. Käll koden för paketet finns i [Azure-WebJobs-SDK GitHub-](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) lagringsplatsen.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

Kö lagrings bindningar finns i [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK GitHub-](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Kodning
Functions förväntar sig en *base64* -kodad sträng. Eventuella justeringar av kodnings typen (för att förbereda data som en *base64* -kodad sträng) måste implementeras i anrops tjänsten.

## <a name="trigger"></a>Utlösare

Använd kön utlösare för att starta en funktion när ett nytt objekt tas emot i en kö. Queue-meddelandet tillhandahålls som indata till funktionen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en kö trigger-bindning i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningen. Funktionen avsöker `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

Här är *Function. JSON* -filen:

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

I [konfigurations](#trigger---configuration) avsnittet förklaras dessa egenskaper.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en kö trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen avsöker `myqueue-items` kön och skriver en logg varje gången ett köobjekt bearbetas.

Här är *Function. JSON* -filen:

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

I [konfigurations](#trigger---configuration) avsnittet förklaras dessa egenskaper.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

Koden  *_\_init_\_. py* deklarerar en parameter som `func.ServiceBusMessage`, vilket gör att du kan läsa meddelandet i kön i din funktion.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

 ---

## <a name="trigger---attributes-and-annotations"></a>Utlös-attribut och anteckningar

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

  Du kan ställa in egenskapen `Connection` för att ange den app-inställning som innehåller den anslutnings sträng för lagrings kontot som ska användas, som du ser i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Ett fullständigt exempel finns i [Utlös ande- C# exempel](#trigger).

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

* `QueueTrigger` attributets `Connection` egenskap.
* Attributet `StorageAccount` som används för samma parameter som attributet `QueueTrigger`.
* Det `StorageAccount` attribut som används för funktionen.
* Det `StorageAccount` attribut som används för klassen.
* App-inställningen "AzureWebJobsStorage".

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java-skript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

`QueueTrigger` kommentaren ger dig åtkomst till kön som utlöser funktionen. I följande exempel blir Queue-meddelandet tillgängligt för funktionen via `message`-parametern.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Egenskap    | Beskrivning |
|-------------|-----------------------------|
|`name`       | Deklarerar parameter namnet i funktions under skriften. När funktionen utlöses har den här parameterns värde innehållet i Queue-meddelandet. |
|`queueName`  | Deklarerar könamnet i lagrings kontot. |
|`connection` | Pekar på anslutnings strängen för lagrings kontot. |

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `QueueTrigger`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas| måste anges till `queueTrigger`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning**| Saknas | Endast i *Function. JSON* -filen. måste anges till `in`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas |Namnet på den variabel som innehåller köns objektets nytto Last i funktions koden.  |
|**queueName** | **QueueName**| Namnet på kön som ska avsökas. |
|**anslutningen** | **Anslutning** |Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "Storage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Få åtkomst till meddelande data med hjälp av en metod parameter som `string paramName`. Du kan binda till någon av följande typer:

* Objekt-funktions körningen deserialiserar en JSON-nyttolast till en instans av en godtycklig klass som definierats i din kod. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Få åtkomst till meddelande data med hjälp av en metod parameter som `string paramName`. `paramName` är det värde som anges i egenskapen `name` för *Function. JSON*. Du kan binda till någon av följande typer:

* Objekt-funktions körningen deserialiserar en JSON-nyttolast till en instans av en godtycklig klass som definierats i din kod. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Objektet för köobjekt är tillgängligt via `context.bindings.<NAME>` där `<NAME>` matchar namnet som definierats i *Function. JSON*. Om nytto lasten är JSON deserialiseras värdet i ett objekt.

# <a name="pythontabpython"></a>[Python](#tab/python)

Få åtkomst till Queue-meddelandet via parametern som anges som [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="javatabjava"></a>[Java](#tab/java)

[QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) -anteckningen ger dig åtkomst till det Queue meddelande som utlöste funktionen.

---

## <a name="trigger---message-metadata"></a>Utlösare - meddelande metadata

Utlösaren för kön innehåller flera [Egenskaper för metadata](./functions-bindings-expressions-patterns.md#trigger-metadata). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa är egenskaper för klassen [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`QueueTrigger`|`string`|Köns nytto Last (om en giltig sträng). Om meddelandet nytto last för kön är en sträng har `QueueTrigger` samma värde som variabeln som namnges av egenskapen `name` i *Function. JSON*.|
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

Utlösaren för kön implementerar en slumpmässig algoritm för att ta bort inaktivt kön på lagrings transaktions kostnader.

Algoritmen använder följande logik:

- När ett meddelande hittas väntar körningen i två sekunder och söker sedan efter ett annat meddelande
- När inget meddelande hittas väntar det cirka fyra sekunder innan det försöker igen.
- Efter efterföljande misslyckade försök att hämta ett Queue-meddelande fortsätter vänte tiden att öka tills den når den maximala vänte tiden, vilket är en minut som standard.
- Maximal vänte tid kan konfigureras via `maxPollingInterval`-egenskapen i [Host. JSON-filen](functions-host-json.md#queues).

För lokal utveckling är det maximala avsöknings intervallet två sekunder som standard.

I samband med faktureringen är tids åtgången för avsökning av körningen "kostnads fri" och inte räknas till ditt konto.

## <a name="trigger---concurrency"></a>Utlösare – samtidighets läge

När det finns flera köa meddelanden som väntar, hämtar kön utlösare en batch med meddelanden och anropar funktions instanserna samtidigt för att bearbeta dem. Som standard är batchstorleken 16. När antalet som bearbetas går ned till 8, får körningen en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion på en virtuell dator (VM) är 24. Den här gränsen gäller separat för varje funktion som utlöses av kön på varje virtuell dator. Om din Function-app skalar ut till flera virtuella datorer väntar varje virtuell dator på utlösare och försöker köra funktioner. Om till exempel en Function-app skalar ut till tre virtuella datorer är det maximala antalet samtidiga instanser av en funktion som utlöses av en kö 72.

Batchstorleken och tröskelvärdet för att hämta en ny batch kan konfigureras i [Host. JSON-filen](functions-host-json.md#queues). Om du vill minimera parallell körning för köade funktioner i en Function-app kan du ange batchstorleken till 1. Med den här inställningen elimineras samtidighet endast så länge din funktions program körs på en enda virtuell dator (VM). 

Queue-utlösaren förhindrar automatiskt en funktion från att bearbeta ett köobjekt flera gånger; funktioner behöver inte skrivas för att vara idempotenta.

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

[Host. JSON](functions-host-json.md#queues) -filen innehåller inställningar som styr beteendet för kön utlösare. Se avsnittet [Host. JSON-inställningar](#hostjson-settings) för information om tillgängliga inställningar.

## <a name="output"></a>Resultat

Använd bindningen för lagring av utdata i Azure Queue för att skriva meddelanden till en kö.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som skapar ett Queue-meddelande för varje http-begäran som tas emot.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
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

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en http trigger-bindning i en *Function. JSON* -fil och [ C# skript kod (. CSX)](functions-reference-csharp.md) som använder bindningen. Funktionen skapar ett köobjekt med en **CustomQueueMessage** objekt nytto last för varje http-begäran som tas emot.

Här är *Function. JSON* -filen:

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
      "name": "$return"
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

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en HTTP trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skapar ett köobjekt för varje HTTP-begäran som tas emot.

Här är *Function. JSON* -filen:

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
      "name": "$return"
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

I [konfigurations](#output---configuration) avsnittet förklaras dessa egenskaper.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

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

# <a name="javatabjava"></a>[Java](#tab/java)

 I följande exempel visas en Java-funktion som skapar ett Queue-meddelande för när det utlöses av en HTTP-begäran.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@QueueOutput` kommentar för parametrar vars värde ska skrivas till Queue Storage.  Parameter typen ska vara `OutputBinding<T>`, där `T` är valfri ursprunglig Java-typ för en POJO.

---

## <a name="output---attributes-and-annotations"></a>Utdata-attribut och anteckningar

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Använd [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

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

Ett fullständigt exempel finns i [utdata- C# exempel](#output).

Du kan använda attributet `StorageAccount` för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om Utlösar-attribut.

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java-skript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

`QueueOutput` kommentaren ger dig åtkomst till att skriva ett meddelande till en funktion. I följande exempel visas en HTTP-utlöst funktion som skapar ett Queue-meddelande.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Egenskap    | Beskrivning |
|-------------|-----------------------------|
|`name`       | Deklarerar parameter namnet i funktions under skriften. När funktionen utlöses har den här parameterns värde innehållet i Queue-meddelandet. |
|`queueName`  | Deklarerar könamnet i lagrings kontot. |
|`connection` | Pekar på anslutnings strängen för lagrings kontot. |

Den parameter som är associerad med `QueueOutput`-anteckningen skrivs som en [OutputBinding\<t\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) -instans.

---

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `Queue`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | måste anges till `queue`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | måste anges till `out`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön i funktions koden. Ange till `$return` för att referera till funktionens retur värde.|
|**queueName** |**QueueName** | Köns namn. |
|**anslutningen** | **Anslutning** |Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "Storage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Skriv ett enskilt Queue-meddelande med hjälp av en metod parameter som `out T paramName`. Du kan använda metoden Return-typ i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt som är serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

I C# och C# skript skriver du flera meddelanden i kön genom att använda någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Skriv ett enskilt Queue-meddelande med hjälp av en metod parameter som `out T paramName`. `paramName` är det värde som anges i egenskapen `name` för *Function. JSON*. Du kan använda metoden Return-typ i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt som är serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

I C# och C# skript skriver du flera meddelanden i kön genom att använda någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Objektet utgående kö är tillgängligt via `context.bindings.<NAME>` där `<NAME>` matchar namnet som definierats i *Function. JSON*. Du kan använda en sträng eller ett JSON-serialiserbar objekt för nytto lasten i objektet.

# <a name="pythontabpython"></a>[Python](#tab/python)

Det finns två alternativ för att placera ett Event Hub-meddelande från en funktion:

- **RETUR värde**: Ange `name`-egenskapen i *Function. json* för att `$return`. Med den här konfigurationen sparas funktionens retur värde som ett kö lagrings meddelande.

- **Tvingande**: Skicka ett värde till [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) -metoden för den parameter som deklarerats som [Utdatatyp.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Värdet som skickas till `set` sparas som ett kö lagrings meddelande.

# <a name="javatabjava"></a>[Java](#tab/java)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion med hjälp av [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) -anteckningen:

- **RETUR värde**: genom att använda anteckningen i själva funktionen sparas returvärdet för funktionen som ett Event Hub-meddelande.

- **Tvingande**: om du uttryckligen vill ange ett meddelande värde ska du använda anteckningen på en specifik parameter av typen [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), där `T` är en POJO eller en ursprunglig Java-typ. Med den här konfigurationen behåller ett värde i `setValue`-metoden värdet som ett Event Hub-meddelande.

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Kö | [Fel koder för kö](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tabell, kö | [Lagrings fel koder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabell, kö |  [Felsökning](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exemplet Host. JSON-filen nedan innehåller bara version 2. x +-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i version 2. x och mer finns i [Host. JSON-referens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för Host. json i functions 1. x finns i [Host. JSON-referensen för Azure Functions 1. x](functions-host-json-v1.md).

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

* [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Gå till en självstudie som använder en kö Storage utgående bindning](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
