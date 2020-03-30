---
title: Azure-kölagringsutlösare för Azure-funktioner
description: Lär dig att köra en Azure-funktion när Azure Queue storage-data ändras.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277380"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Azure-kölagringsutlösare för Azure-funktioner

Kölagringsutlösaren kör en funktion när meddelanden läggs till i Azure Queue storage.

## <a name="encoding"></a>Kodning

Funktionerna förväntar sig en *base64-kodad* sträng. Alla justeringar av kodningstypen (för att förbereda data som en *base64-kodad* sträng) måste implementeras i anropartjänsten.

## <a name="example"></a>Exempel

Använd köutlösaren för att starta en funktion när ett nytt objekt tas emot i en kö. Kömeddelandet anges som indata till funktionen.

# <a name="c"></a>[C#](#tab/csharp)

Följande exempel visar en [C#-funktion](functions-dotnet-class-library.md) som avföljer `myqueue-items` kön och skriver en logg varje gång ett köobjekt bearbetas.

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en köutlösare som binder i en *function.json-fil* och [C#script (.csx)](functions-reference-csharp.md) som använder bindningen. Funktionen avserar `myqueue-items` kön och skriver en logg varje gång ett köobjekt bearbetas.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

Här är C# skriptkoden:

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

I [avsnittet](#usage) `myQueueItem`användning förklaras , `name` som namnges av egenskapen i function.json.  [Meddelandemetadataavsnittet](#message-metadata) förklarar alla andra variabler som visas.

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en köutlösare som binder i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen avserar `myqueue-items` kön och skriver en logg varje gång ett köobjekt bearbetas.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#configuration) förklarar dessa egenskaper.

> [!NOTE]
> Namnparametern `context.bindings.<name>` återspeglar som i JavaScript-koden som innehåller köobjektet nyttolast. Den här nyttolasten skickas också som den andra parametern till funktionen.

Här är JavaScript-koden:

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

I [avsnittet](#usage) `myQueueItem`användning förklaras , `name` som namnges av egenskapen i function.json.  [Meddelandemetadataavsnittet](#message-metadata) förklarar alla andra variabler som visas.

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar hur du läser ett kömeddelande som skickas till en funktion via en utlösare.

En lagringsköutlösare definieras i *function.json* där *typen* är inställd på `queueTrigger`.

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

Koden `func.ServiceBusMessage`init *\_.py deklarerar en parameter som , vilket gör att du kan läsa kömeddelandet i din funktion. _ \__*

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

# <a name="java"></a>[Java](#tab/java)

Följande Java-exempel visar en lagringsköutlösarefunktion som loggar `myqueuename`det utlösta meddelandet som placeras i kön .

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

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [klassbibliotek C#](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en köutlösare:

* [QueueTriggerAttribut](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Attributets konstruktor tar namnet på kön som ska övervakas, vilket visas i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Du kan `Connection` ange egenskapen för att ange den appinställning som innehåller anslutningssträngen för lagringskonto som ska användas, vilket visas i följande exempel:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Ett fullständigt exempel finns i [exempel](#example).

* [LagringKontoAttribut](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Här kan du ange vilket lagringskonto som ska användas. Konstruktorn tar namnet på en appinställning som innehåller en lagringsanslutningssträng. Attributet kan användas på parameter-, metod- eller klassnivå. I följande exempel visas klassnivå och metodnivå:

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

Det lagringskonto som ska användas bestäms i följande ordning:

* Egenskapen `QueueTrigger` för `Connection` attributet.
* Attributet `StorageAccount` som tillämpas på `QueueTrigger` samma parameter som attributet.
* Attributet `StorageAccount` som används för funktionen.
* Attributet `StorageAccount` som används för klassen.
* Appinställningen "AzureWebJobsStorage".

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Anteckningen `QueueTrigger` ger dig åtkomst till kön som utlöser funktionen. I följande exempel görs kömeddelandet tillgängligt `message` för funktionen via parametern.

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
|`name`       | Deklarerar parameternamnet i funktionssignaturen. När funktionen utlöses har den här parameterns värde innehållet i kömeddelandet. |
|`queueName`  | Deklarerar könamnet i lagringskontot. |
|`connection` | Pekar på anslutningssträngen för lagringskonto. |

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `QueueTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas| Måste ställas `queueTrigger`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning**| Saknas | Endast i *filen function.json.* Måste ställas `in`in på . Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas |Namnet på variabeln som innehåller köartikelns nyttolast i funktionskoden.  |
|**queueName (queueName)** | **QueueName (QueueName)**| Namnet på kön till omröstning. |
|**Anslutning** | **Anslutning** |Namnet på en appinställning som innehåller anslutningssträngen Lagring som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet här. Om du till `connection` exempel anger "MyStorage" söker körtiden Funktioner efter en appinställning med namnet "MyStorage". Om du `connection` lämnar tom använder körtiden Funktioner standardanslutningssträngen för `AzureWebJobsStorage`lagring i appinställningen som heter .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Få tillgång till meddelandedata med `string paramName`hjälp av en metodparameter som . Du kan binda till någon av följande typer:

* Objekt - Funktionerna deserialiserar en JSON-nyttolast till en instans av en godtycklig klass som definieras i koden. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker `CloudQueueMessage` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Få tillgång till meddelandedata med `string paramName`hjälp av en metodparameter som . Värdet `paramName` är det värde `name` som anges i egenskapen *function.json*. Du kan binda till någon av följande typer:

* Objekt - Funktionerna deserialiserar en JSON-nyttolast till en instans av en godtycklig klass som definieras i koden. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Om du försöker `CloudQueueMessage` binda till och få ett felmeddelande kontrollerar du att du har en referens till [rätt LagringSDK-version](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Köartikelnyttolasten är `context.bindings.<NAME>` tillgänglig `<NAME>` via den plats där matchar namnet som definierats i *function.json*. Om nyttolasten är JSON avaktiveras värdet till ett objekt.

# <a name="python"></a>[Python](#tab/python)

Öppna kömeddelandet via parametern som skrivs som [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

[KöTrigger-anteckningen](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) ger dig åtkomst till kömeddelandet som utlöste funktionen.

---

## <a name="message-metadata"></a>Metadata för meddelanden

Köutlösaren innehåller flera [metadataegenskaper](./functions-bindings-expressions-patterns.md#trigger-metadata). Dessa egenskaper kan användas som en del av bindningsuttryck i andra bindningar eller som parametrar i koden. Egenskaperna är medlemmar i klassen [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`QueueTrigger`|`string`|Könyttolast (om en giltig sträng). Om kömeddelandenyttolasten är `QueueTrigger` en sträng har du samma `name` värde som variabeln som anges av egenskapen i *function.json*.|
|`DequeueCount`|`int`|Antalet gånger det här meddelandet har avskällits.|
|`ExpirationTime`|`DateTimeOffset`|Den tid då meddelandet upphör att gälla.|
|`Id`|`string`|Kömeddelande-ID.|
|`InsertionTime`|`DateTimeOffset`|Den tid då meddelandet lades till i kön.|
|`NextVisibleTime`|`DateTimeOffset`|Den tid då meddelandet visas nästa gång.|
|`PopReceipt`|`string`|Meddelandets popkvitto.|

## <a name="poison-messages"></a>Giftmeddelanden

När en köutlösarefunktion misslyckas försöker Azure Functions funktionen upp till fem gånger för ett visst kömeddelande, inklusive det första försöket. Om alla fem försöken misslyckas lägger funktionerna till ett meddelande i en kö med namnet * &lt;originalqueuename>-poison*. Du kan skriva en funktion för att bearbeta meddelanden från giftkön genom att logga dem eller skicka ett meddelande om att manuell uppmärksamhet behövs.

Om du vill hantera giftmeddelanden manuellt kontrollerar du [dequeueCount](#message-metadata) för kömeddelandet.

## <a name="polling-algorithm"></a>Avsökningsalgoritm

Köutlösaren implementerar en slumpmässig exponentiell back-off-algoritm för att minska effekten av inaktiv köavsökning på lagringstransaktionskostnader.

Algoritmen använder följande logik:

- När ett meddelande hittas väntar körningen två sekunder och söker sedan efter ett annat meddelande
- När inget meddelande hittas väntar det cirka fyra sekunder innan du försöker igen.
- Efter efterföljande misslyckade försök att få ett kömeddelande fortsätter väntetiden att öka tills den når den maximala väntetiden, vilket är standard till en minut.
- Den maximala väntetiden kan konfigureras via egenskapen `maxPollingInterval` i filen [host.json](functions-host-json.md#queues).

För lokal utveckling är det maximala avsökningsintervallet som standard två sekunder.

När det gäller fakturering är tiden för avsökning av körningen "gratis" och räknas inte mot ditt konto.

## <a name="concurrency"></a>Samtidighet

När det väntar flera kömeddelanden hämtar köutlösaren en bunt meddelanden och anropar funktionsinstanser samtidigt för att bearbeta dem. Som standard är batchstorleken 16. När numret som bearbetas börjar bli åtta får körningen en ny batch och börjar bearbeta dessa meddelanden. Så det maximala antalet samtidiga meddelanden som bearbetas per funktion på en virtuell dator (VM) är 24. Den här gränsen gäller separat för varje köutlöst funktion på varje virtuell dator. Om funktionsappen skalas ut till flera virtuella datorer väntar varje virtuell dator på utlösare och försöker köra funktioner. Om till exempel en funktionsapp skalas ut till tre virtuella datorer är standardtvårt antal samtidiga instanser av en köutlöst funktion 72.

Batchstorleken och tröskelvärdet för att hämta en ny batch kan konfigureras i [filen host.json](functions-host-json.md#queues). Om du vill minimera parallell körning för köutlösta funktioner i en funktionsapp kan du ange batchstorleken till 1. Den här inställningen eliminerar samtidighet endast så länge som din funktionsapp körs på en enda virtuell dator (VM). 

Köutlösaren förhindrar automatiskt att en funktion bearbetar ett kömeddelande flera gånger. funktioner behöver inte skrivas för att vara idempotenta.

## <a name="hostjson-properties"></a>egenskaper för host.json

[Filen host.json](functions-host-json.md#queues) innehåller inställningar som styr köutlösarens beteende. Mer information om tillgängliga inställningar finns i avsnittet [host.json-inställningar.](functions-bindings-storage-queue-output.md#hostjson-settings)

## <a name="next-steps"></a>Nästa steg

- [Skriva kölagringsmeddelanden (utdatabindning)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
