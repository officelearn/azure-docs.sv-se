---
title: Utgående bindning för Azure Queue Storage för Azure Functions
description: Lär dig att skapa Azure Queue Storage-meddelanden i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277341"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Utgående bindningar för Azure Queue Storage för Azure Functions

Azure Functions kan skapa nya Azure Queue Storage-meddelanden genom att konfigurera en utgående bindning.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

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

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

I [konfigurations](#configuration) avsnittet förklaras dessa egenskaper.

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

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

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

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

Ett fullständigt exempel finns i [output-exemplet](#example).

Du kan använda attributet `StorageAccount` för att ange lagrings kontot på klass-, metod-eller parameter nivå. Mer information finns i avsnittet om Utlösar-attribut.

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

Med `QueueOutput` kommentar kan du skriva ett meddelande som utdata till en funktion. I följande exempel visas en HTTP-utlöst funktion som skapar ett Queue-meddelande.

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

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `Queue`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till `queue`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till `out`. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön i funktions koden. Ange till `$return` för att referera till funktionens retur värde.|
|**queueName** |**QueueName** | Köns namn. |
|**anslutningen** | **Anslutning** |Namnet på en app-inställning som innehåller den lagrings anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till exempel ställer in `connection` till "telestorage" söker Functions-körningen efter en app-inställning med namnet "Storage". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen för lagring i den app-inställning som heter `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Skriv ett enskilt Queue-meddelande med hjälp av en metod parameter som `out T paramName`. Du kan använda metoden Return-typ i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt som är serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

I C# och C# skript skriver du flera meddelanden i kön genom att använda någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Skriv ett enskilt Queue-meddelande med hjälp av en metod parameter som `out T paramName`. `paramName` är det värde som anges i egenskapen `name` för *Function. JSON*. Du kan använda metoden Return-typ i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt som är serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker binda till `CloudQueueMessage` och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

I C# och C# skript skriver du flera meddelanden i kön genom att använda någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Objektet utgående kö är tillgängligt via `context.bindings.<NAME>` där `<NAME>` matchar namnet som definierats i *Function. JSON*. Du kan använda en sträng eller ett JSON-serialiserbar objekt för nytto lasten i objektet.

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att placera ett Event Hub-meddelande från en funktion:

- **RETUR värde**: Ange `name`-egenskapen i *Function. json* för att `$return`. Med den här konfigurationen sparas funktionens retur värde som ett kö lagrings meddelande.

- **Tvingande**: Skicka ett värde till [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) -metoden för den parameter som deklarerats som [Utdatatyp.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Värdet som skickas till `set` sparas som ett kö lagrings meddelande.

# <a name="java"></a>[Java](#tab/java)

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

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Det maximala intervallet mellan Queue-avsökningar. Minimum är 00:00:00.100 (100 MS) och ökar till 00:01:00 (1 min).  I 1. x är data typen millisekunder och i 2. x och högre är det ett TimeSpan.|
|visibilityTimeout|00:00:00|Tidsintervall mellan återförsök vid bearbetning av ett meddelande Miss lyckas. |
|batchSize|16|Antalet köa meddelanden som funktions körningen hämtar samtidigt och processer parallellt. När antalet som bearbetas går ned till `newBatchThreshold`, hämtar kör tiden en annan batch och börjar bearbeta dessa meddelanden. Det maximala antalet samtidiga meddelanden som bearbetas per funktion är `batchSize` plus `newBatchThreshold`. Den här gränsen gäller separat för varje funktion som utlöses av kön. <br><br>Om du vill undvika parallell körning av meddelanden som tas emot i en kö kan du ange `batchSize` till 1. Den här inställningen eliminerar dock ingen samtidighet så länge som din funktions App körs på en enda virtuell dator (VM). Om Function-appen skalar ut till flera virtuella datorer kan varje virtuell dator köra en instans av varje funktion som utlöses av kön.<br><br>Högsta `batchSize` är 32. |
|maxDequeueCount|5|Antal försök att bearbeta ett meddelande innan det flyttas till en Poison-kö.|
|newBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt går till det här talet, hämtar körningen en annan batch.|

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion som ändringar i Queue Storage-data (utlösare)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
