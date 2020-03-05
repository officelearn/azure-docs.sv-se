---
title: Azure Service Bus-bindningar för Azure Functions
description: Lär dig hur du kör en Azure-funktion när Azure Service Bus-meddelanden skapas.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273572"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Service Bus utlösare för Azure Functions

Använda Service Bus-utlösare ska svara på meddelanden från en Service Bus-kö eller ett ämne.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som läser [meddelandets metadata](#message-metadata) och loggar ett Service Bus Queue-meddelande:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en Service Bus trigger-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser [meddelandets metadata](#message-metadata) och loggar ett Service Bus Queue-meddelande.

Här är bindnings data i *Function. JSON* -filen:

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

Här är C#-skriptkoden:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Service Bus trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser [meddelandets metadata](#message-metadata) och loggar ett Service Bus Queue-meddelande. 

Här är bindnings data i *Function. JSON* -filen:

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

Här är skriptet JavaScript-koden:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar hur du läser ett Service Bus Queue-meddelande via en utlösare.

En Service Bus-bindning definieras i *Function. JSON* där *typ* är inställd på `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Koden i  *_\_init_\_. py* deklarerar en parameter som `func.ServiceBusMessage`, vilket gör att du kan läsa meddelandet i kön i din funktion.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

Följande Java-funktion använder `@ServiceBusQueueTrigger` kommentar från [Java Functions runtime library](/java/api/overview/azure/functions/runtime) för att beskriva konfigurationen för en Service Bus Queue-utlösare. Funktionen tar bort meddelandet som placerats i kön och lägger till det i loggarna.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Java-funktioner kan också aktive ras när ett meddelande läggs till i ett Service Bus ämne. I följande exempel används den `@ServiceBusTopicTrigger` kommentaren för att beskriva utlösarens konfiguration.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

I [ C# klass bibliotek](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en Service Bus-utlösare:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Attributets konstruktorn tar namnet på kön eller ämnet och prenumerationen. I Azure Functions-version 1.x, du kan också ange anslutningens åtkomsträttigheter. Om du inte anger åtkomst rättigheter är standardvärdet `Manage`. Mer information finns i avsnittet om [Utlösar-konfiguration](#configuration) .

  Här är ett exempel som visar det attribut som används med en strängparameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Du kan ange egenskapen `Connection` för att ange namnet på en app-inställning som innehåller den Service Bus anslutnings sträng som ska användas, som du ser i följande exempel:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Ett fullständigt exempel finns i [Utlös ande-exempel](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Innehåller ett annat sätt att ange det Service Bus-kontot som ska användas. Konstruktorn tar namnet på en appinställning som innehåller en Service Bus-anslutningssträng. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Service Bus-kontot du använder bestäms i följande ordning:

* `ServiceBusTrigger` attributets `Connection` egenskap.
* Attributet `ServiceBusAccount` som används för samma parameter som attributet `ServiceBusTrigger`.
* Det `ServiceBusAccount` attribut som används för funktionen.
* Det `ServiceBusAccount` attribut som används för klassen.
* Appinställningen ”AzureWebJobsServiceBus”.

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

Med `ServiceBusQueueTrigger` kommentaren kan du skapa en funktion som körs när ett Service Bus Queue-meddelande skapas. Tillgängliga konfigurations alternativ inkluderar könamn och namn på anslutnings sträng.

Med `ServiceBusTopicTrigger` kommentaren kan du ange ett ämne och en prenumeration som ska användas för att ange vilka data som utlöser funktionen.

Se utlösnings [exemplet](#example) för mer information.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `ServiceBusTrigger`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till ”serviceBusTrigger”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till ”in”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön eller ämnet meddelandet i funktionskoden. |
|**queueName**|**QueueName**|Namnet på kön som ska övervaka.  Ange bara om övervakning av en kö, inte för ett ämne.
|**topicName**|**TopicName**|Namnet på ämnet du övervakar. Ange bara om övervakning av ett ämne, inte för en kö.|
|**subscriptionName**|**SubscriptionName**|Namnet på prenumerationen som ska övervaka. Ange bara om övervakning av ett ämne, inte för en kö.|
|**anslutningen**|**Anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträngen för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Om du till exempel anger `connection` till "MyServiceBus" söker Functions runtime efter en app-inställning med namnet "AzureWebJobsMyServiceBus". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen Service Bus i appens inställning med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutningssträngen måste vara för en Service Bus-namnområde, inte begränsat till en viss kö eller ämne. |
|**accessRights**|**Åtkomst**|Behörigheten för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket anger att `connection` har behörigheten **Hantera** . Om du använder en anslutnings sträng som inte har behörigheten **Hantera** , anger du `accessRights` till "lyssna". Annars kan hantera Functions runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2. x och högre är den här egenskapen inte tillgänglig eftersom den senaste versionen av Service Bus SDK inte stöder hanterings åtgärder.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` om du ansluter till en kö eller prenumeration som [stöder sessioner](../service-bus-messaging/message-sessions.md) . `false` annars, vilket är standardvärdet.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Följande parameter typer är tillgängliga för kön eller ämnes meddelandet:

* `string` – om meddelandet är text.
* `byte[]` – användbart för binära data.
* En anpassad typ - om meddelandet innehåller JSON, Azure Functions försöker deserialisera JSON-data.
* `BrokeredMessage` – ger dig det deserialiserade meddelandet med metoden [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Dessa parameter typer är för Azure Functions version 1. x. för 2. x och högre använder du [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage`.

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Följande parameter typer är tillgängliga för kön eller ämnes meddelandet:

* `string` – om meddelandet är text.
* `byte[]` – användbart för binära data.
* En anpassad typ - om meddelandet innehåller JSON, Azure Functions försöker deserialisera JSON-data.
* `BrokeredMessage` – ger dig det deserialiserade meddelandet med metoden [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Dessa parametrar är för Azure Functions version 1. x. för 2. x och högre använder du [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Få åtkomst till kön eller ämnes meddelandet med hjälp av `context.bindings.<name from function.json>`. Service Bus-meddelande skickas till funktionen en sträng eller en JSON-objekt.

# <a name="python"></a>[Python](#tab/python)

Queue-meddelandet är tillgängligt för funktionen via en parameter som anges som `func.ServiceBusMessage`. Service Bus-meddelande skickas till funktionen en sträng eller en JSON-objekt.

# <a name="java"></a>[Java](#tab/java)

Det inkommande Service Buss meddelandet är tillgängligt via en `ServiceBusQueueMessage`-eller `ServiceBusTopicMessage`-parameter.

[Se exemplet för mer information](#example).

---

## <a name="poison-messages"></a>Poison-meddelanden

Hantering av skadligt meddelande kan inte styras eller konfigurerats i Azure Functions. Service Bus hanterar skadliga meddelanden själva.

## <a name="peeklock-behavior"></a>PeekLock beteende

Functions-körningen tar emot ett meddelande i [PeekLock-läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Den anropar `Complete` i meddelandet om funktionen har slutförts eller anropar `Abandon` om funktionen Miss lyckas. Om funktionen körs längre än `PeekLock` tids gränsen förnyas låset automatiskt så länge funktionen körs. 

`maxAutoRenewDuration` kan konfigureras i *Host. JSON*, som mappar till [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Högsta tillåtna för den här inställningen är 5 minuter enligt dokumentation för Service Bus, medan du kan öka tidsgränsen funktioner från standardvärdet 5 minuter till 10 minuter. För Service Bus-funktionerna skulle du vill göra det sedan eftersom du skulle överskrida gränsen för Service Bus-förnyelse.

## <a name="message-metadata"></a>Metadata för meddelande

Service Bus utlösaren innehåller flera [Egenskaper för metadata](./functions-bindings-expressions-patterns.md#trigger-metadata). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. De här egenskaperna är medlemmar i klassen [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Antal leveranser.|
|`DeadLetterSource`|`string`|Källan för obeställbara meddelanden.|
|`ExpiresAtUtc`|`DateTime`|Förfallotid i UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Kötid i UTC.|
|`MessageId`|`string`|Ett användardefinierat värde som Service Bus kan använda för att identifiera duplicerade meddelanden, om aktiverad.|
|`ContentType`|`string`|En innehålls typ identifierare som används av avsändaren och mottagaren för programspecifik logik.|
|`ReplyTo`|`string`|Svara till kö-adress.|
|`SequenceNumber`|`Int64`|Unikt nummer som tilldelats ett meddelande av Service Bus.|
|`To`|`string`|Skicka till adress.|
|`Label`|`string`|Den programspecifika etiketten.|
|`CorrelationId`|`string`|Korrelations-ID|

Se [kod exempel](#example) som använder dessa egenskaper tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

- [Skicka Azure Service Bus meddelanden från Azure Functions (utgående bindning)](./functions-bindings-service-bus-output.md)
