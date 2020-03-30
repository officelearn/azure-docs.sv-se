---
title: Utdatabindning för Azure-kölagring för Azure-funktioner
description: Lär dig att skapa Azure Queue lagringsmeddelanden i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 76af5f398edd736874fa79095f2e80c02298eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277341"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Utdatabindningar för Azure-kölagring för Azure-funktioner

Azure Functions kan skapa nya Azure Queue storage-meddelanden genom att konfigurera en utdatabindning.

Information om inställnings- och konfigurationsinformation finns i [översikten](./functions-bindings-storage-queue.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skapar ett kömeddelande för varje HTTP-begäran som tas emot.

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en HTTP-utlösare bindning i en *function.json-fil* och [C#script (.csx)](functions-reference-csharp.md) kod som använder bindningen. Funktionen skapar ett köobjekt med en **AnpassadQueueMessage-objektnyttolast** för varje HTTP-begäran som tas emot.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C#-skriptkod som skapar ett enda kömeddelande:

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

Du kan skicka flera meddelanden samtidigt `ICollector` `IAsyncCollector` med hjälp av en eller en parameter. Här är C#-skriptkod som skickar flera meddelanden, ett med HTTP-begärandedata och en med hårdkodade värden:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en HTTP-utlösare som binder i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skapar ett köobjekt för varje HTTP-begäran som tas emot.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Du kan skicka flera meddelanden samtidigt genom att `myQueueItem` definiera en meddelandematris för utdatabindningen. Följande JavaScript-kod skickar två kömeddelanden med hårdkodade värden för varje HTTP-begäran som tas emot.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas hur du skriver ut enstaka och flera värden till lagringsköer. Konfigurationen som behövs för *function.json* är samma åt båda hållen.

En lagringsköbindning definieras i *function.json* där *typen* är inställd på `queue`.

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

Om du vill ange ett enskilt meddelande i `set` kön skickar du ett enda värde till metoden.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Om du vill skapa flera meddelanden i kön deklarerar du en parameter som lämplig listtyp `set` och skickar en matris med värden (som matchar listtypen) till metoden.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

 I följande exempel visas en Java-funktion som skapar ett kömeddelande för när det utlöses av en HTTP-begäran.

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

I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@QueueOutput` på parametrar vars värde skulle skrivas till Kölagring.  Parametertypen ska `OutputBinding<T>`vara `T` , var är någon inbyggd Java-typ av en POJO.

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

Attributet gäller `out` för en parameter eller funktionens returvärde. Attributets konstruktor tar namnet på kön, som visas i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Du kan `Connection` ange egenskapen för att ange vilket lagringskonto som ska användas, som visas i följande exempel:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [Exempel på utdata](#example).

Du kan `StorageAccount` använda attributet för att ange lagringskontot på klass-, metod- eller parameternivå. Mer information finns i Trigger - attribut.

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

I `QueueOutput` anteckningen kan du skriva ett meddelande som utdata för en funktion. I följande exempel visas en HTTP-utlöst funktion som skapar ett kömeddelande.

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
|`name`       | Deklarerar parameternamnet i funktionssignaturen. När funktionen utlöses har den här parameterns värde innehållet i kömeddelandet. |
|`queueName`  | Deklarerar könamnet i lagringskontot. |
|`connection` | Pekar på anslutningssträngen för lagringskonto. |

Parametern som `QueueOutput` är associerad med anteckningen skrivs som en [OutputBinding\<T-förekomst.\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java)

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `Queue` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas `queue`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste ställas `out`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön i funktionskoden. Ställ `$return` in på att referera till funktionens returvärde.|
|**queueName (queueName)** |**QueueName (QueueName)** | Namnet på kön. |
|**Anslutning** | **Anslutning** |Namnet på en appinställning som innehåller anslutningssträngen Lagring som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till `connection` exempel anger "MyStorage" söker körtiden Funktioner efter en appinställning med namnet "MyStorage". Om du `connection` lämnar tom använder körtiden Funktioner standardanslutningssträngen för `AzureWebJobsStorage`lagring i appinställningen som heter .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Skriv ett enskilt kömeddelande med hjälp `out T paramName`av en metodparameter som . Du kan använda metodens returtyp i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker `CloudQueueMessage` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Skriv flera kömeddelanden i C# och C#-skriptet med någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue (molnqueue)](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Skriv ett enskilt kömeddelande med hjälp `out T paramName`av en metodparameter som . Värdet `paramName` är det värde `name` som anges i egenskapen *function.json*. Du kan använda metodens returtyp i stället för en `out` parameter och `T` kan vara någon av följande typer:

* Ett objekt serialiserbar som JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Om du försöker `CloudQueueMessage` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

Skriv flera kömeddelanden i C# och C#-skriptet med någon av följande typer: 

* `ICollector<T>` eller `IAsyncCollector<T>`
* [CloudQueue (molnqueue)](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascript"></a>[Javascript](#tab/javascript)

Utdataköobjektet är `context.bindings.<NAME>` `<NAME>` tillgängligt via den plats där matchar namnet som definierats i *function.json*. Du kan använda en sträng eller ett JSON-serialiserbart objekt för köartikelns nyttolast.

# <a name="python"></a>[Python](#tab/python)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion:

- **Returvärde**: `name` Ange egenskapen i `$return` *function.json* till . Med den här konfigurationen sparas funktionens returvärde som ett kölagringsmeddelande.

- **Imperativ:** Skicka ett värde till den [inställda](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) metoden för parametern som deklarerats som [uttyp.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) Värdet som `set` skickas till sparas som ett kölagringsmeddelande.

# <a name="java"></a>[Java](#tab/java)

Det finns två alternativ för att mata ut ett Event Hub-meddelande från en funktion med hjälp av [KöOutput-anteckningen:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Returvärde**: Genom att använda anteckningen till själva funktionen sparas funktionens returvärde som ett Event Hub-meddelande.

- **Tvingande**: Om du uttryckligen vill ange meddelandevärdet använder du [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)anteckningen på en viss parameter av typen , där `T` är en POJO eller någon inbyggd Java-typ. Med den `setValue` här konfigurationen kvarstår värdet som ett Event Hub-meddelande om du skickar ett värde till metoden.

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning |  Referens |
|---|---|
| Kö | [Felkoder för kö](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Tabell, Kö | [Lagringsfelkoder](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tabell, Kö |  [Troubleshooting](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) (Felsökning) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>värd.json-inställningar

I det här avsnittet beskrivs de globala konfigurationsinställningar som är tillgängliga för den här bindningen i version 2.x och senare. Exempelvärden.json filen nedan innehåller endast inställningarna för version 2.x+ för den här bindningen. Mer information om globala konfigurationsinställningar i version 2.x och senare finns i [host.json-referens för Azure Functions](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x finns i [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

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
|maxPollingInterval|00:00:01|Det maximala intervallet mellan köavsökningar. Minimum är 00:00:00.100 (100 ms) och ökar upp till 00:01:00 (1 min).  I 1.x är datatypen millisekunder, och i 2.x och högre är det en TimeSpan.|
|synlighetTimeout|00:00:00|Tidsintervallet mellan återförsöken när bearbetningen av ett meddelande misslyckas. |
|batchSize|16|Antalet kömeddelanden som funktioner körs samtidigt och bearbetar parallellt. När numret som bearbetas kommer `newBatchThreshold`ner till , körtiden får en annan batch och börjar bearbeta dessa meddelanden. Så det maximala antalet samtidiga meddelanden som bearbetas `batchSize` `newBatchThreshold`per funktion är plus . Den här gränsen gäller separat för varje köutlöst funktion. <br><br>Om du vill undvika parallell körning för meddelanden som `batchSize` tas emot i en kö kan du ställa in 1. Den här inställningen eliminerar dock samtidighet endast så länge som din funktionsapp körs på en enda virtuell dator (VM). Om funktionsappen skalas ut till flera virtuella datorer kan varje virtuell dator köra en instans av varje köutlöst funktion.<br><br>Den `batchSize` maximala är 32. |
|maxDequeueCount|5|Antalet gånger för att försöka bearbeta ett meddelande innan du flyttar det till giftkön.|
|nyaBatchThreshold|batchSize/2|När antalet meddelanden som bearbetas samtidigt kommer ner till det här numret, hämtar körningen en annan batch.|

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion som ändringar av kölagringsdata (utlösare)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
