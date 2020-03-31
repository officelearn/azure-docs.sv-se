---
title: Azure Service Bus-bindningar för Azure-funktioner
description: Lär dig att köra en Azure-funktion när azure servicebussmeddelanden skapas.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273572"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Service Bus-utlösare för Azure-funktioner

Använd servicebussutlösaren för att svara på meddelanden från en servicebusskö eller ett servicebussavsnitt.

Information om inställnings- och konfigurationsinformation finns i [översikten](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser [meddelandemetadata](#message-metadata) och loggar ett kömeddelande för Service Bus:

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

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en Service Bus-utlösarebindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser [meddelandemetadata](#message-metadata) och loggar ett servicebusskömeddelande.

Här är bindningsdata i *filen function.json:*

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

Här är C# skriptkoden:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en Service Bus-utlösarebindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser [meddelandemetadata](#message-metadata) och loggar ett servicebusskömeddelande. 

Här är bindningsdata i *filen function.json:*

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

Här är JavaScript-skriptkoden:

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

I följande exempel visas hur du läser ett kömeddelande för Service Bus via en utlösare.

En Service Bus-bindning definieras i *function.json* där *typen* är inställd på `serviceBusTrigger`.

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

Koden i * _ \_init_\_.py* deklarerar `func.ServiceBusMessage`en parameter som , vilket gör att du kan läsa kömeddelandet i din funktion.

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

Följande Java-funktion `@ServiceBusQueueTrigger` använder anteckningen från [Java-funktioners körningsbibliotek](/java/api/overview/azure/functions/runtime) för att beskriva konfigurationen för en servicebussköutlösare. Funktionen tar tag i meddelandet som placeras i kön och lägger till det i loggarna.

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

Java-funktioner kan också utlösas när ett meddelande läggs till i ett Service Bus-ämne. I följande exempel `@ServiceBusTopicTrigger` används anteckningen för att beskriva utlösarkonfigurationen.

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

I [klassbibliotek C#](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en Service Bus-utlösare:

* [ServiceBusTriggerAttribut](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Attributets konstruktor tar namnet på kön eller ämnet och prenumerationen. I Azure Functions version 1.x kan du också ange anslutningens åtkomsträttigheter. Om du inte anger åtkomsträttigheter är `Manage`standardinställningen . Mer information finns i [avsnittet Utlösare - konfiguration.](#configuration)

  Här är ett exempel som visar attributet som används med en strängparameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Du kan `Connection` ange egenskapen för att ange namnet på en appinställning som innehåller den servicebussanslutningssträng som ska användas, vilket visas i följande exempel:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Ett fullständigt exempel finns i [Utlösare - exempel](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Här kan du ange vilket Service Bus-konto som ska användas. Konstruktorn tar namnet på en appinställning som innehåller en Service Bus-anslutningssträng. Attributet kan användas på parameter-, metod- eller klassnivå. I följande exempel visas klassnivå och metodnivå:

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

Service Bus-kontot som ska användas bestäms i följande ordning:

* Egenskapen `ServiceBusTrigger` för `Connection` attributet.
* Attributet `ServiceBusAccount` som tillämpas på `ServiceBusTrigger` samma parameter som attributet.
* Attributet `ServiceBusAccount` som används för funktionen.
* Attributet `ServiceBusAccount` som används för klassen.
* Appinställningen "AzureWebJobsServiceBus".

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

I `ServiceBusQueueTrigger` anteckningen kan du skapa en funktion som körs när ett servicebusskömeddelande skapas. Konfigurationsalternativ som är tillgängliga inkluderar könamn och anslutningssträngnamn.

I `ServiceBusTopicTrigger` anteckningen kan du ange ett ämne och en prenumeration för att rikta in sig på vilka data som utlöser funktionen.

Se [utlösarexemplet](#example) för mer information.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `ServiceBusTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas in på "serviceBusTrigger". Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste ställas in på "in". Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kö- eller ämnesmeddelandet i funktionskoden. |
|**queueName (queueName)**|**QueueName (QueueName)**|Namnet på kön som ska övervakas.  Ange endast om du övervakar en kö, inte för ett ämne.
|**topicName (ämnesnamn)**|**TopicName (Ämnesnamn)**|Namnet på det ämne som ska övervakas. Ange endast om du övervakar ett ämne, inte för en kö.|
|**subscriptionName (prenumerationNamn)**|**PrenumerationNamn**|Namn på prenumerationen som ska övervakas. Ange endast om du övervakar ett ämne, inte för en kö.|
|**Anslutning**|**Anslutning**|Namnet på en appinställning som innehåller anslutningssträngen servicebuss som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet. Om du till `connection` exempel anger "MyServiceBus" söker körtiden Funktioner efter en appinställning med namnet "AzureWebJobsMyServiceBus". Om du `connection` lämnar tom använder funktionskörningen standardkopplingssträngen för Service Bus i appinställningen med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutningssträng följer du stegen som visas på [Hämta hanteringsautentiseringsuppgifter .](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string) Anslutningssträngen måste vara för ett servicebussnamnområde, inte begränsat till en viss kö eller ett visst ämne. |
|**accessRights**|**Åtkomst**|Åtkomsträttigheter för anslutningssträngen. Tillgängliga värden `manage` `listen`är och . Standard är `manage`, vilket anger `connection` att behörigheten Hantera har behörigheten **Hantera.** Om du använder en anslutningssträng **Manage** som inte `accessRights` har behörigheten Hantera anger du att "lyssna". Annars kan körningen Funktioner misslyckas med att försöka utföra åtgärder som kräver hanteringsrättigheter. I Azure Functions version 2.x och senare är den här egenskapen inte tillgänglig eftersom den senaste versionen av Service Bus SDK inte stöder hantera åtgärder.|
|**ärSessionsEnabled**|**IsSessionsEnabled**|`true`om du ansluter till en [sessionsmedveten](../service-bus-messaging/message-sessions.md) kö eller prenumeration. `false`annars, vilket är standardvärdet.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

# <a name="c"></a>[C#](#tab/csharp)

Följande parametertyper är tillgängliga för kö- eller ämnesmeddelandet:

* `string`- Om meddelandet är text.
* `byte[]`- Användbart för binära data.
* En anpassad typ - Om meddelandet innehåller JSON försöker Azure Functions att avserialisera JSON-data.
* `BrokeredMessage`- Ger dig det deserialiserade meddelandet med metoden [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Dessa parametertyper är för Azure Functions version 1.x; för 2.x och [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) högre, `BrokeredMessage`använd istället för .

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Följande parametertyper är tillgängliga för kö- eller ämnesmeddelandet:

* `string`- Om meddelandet är text.
* `byte[]`- Användbart för binära data.
* En anpassad typ - Om meddelandet innehåller JSON försöker Azure Functions att avserialisera JSON-data.
* `BrokeredMessage`- Ger dig det deserialiserade meddelandet med metoden [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Dessa parametrar är för Azure Functions version 1.x; för 2.x och [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) högre, `BrokeredMessage`använd istället för .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Öppna kö- eller ämnesmeddelandet med hjälp `context.bindings.<name from function.json>`av . Service Bus-meddelandet skickas in i funktionen som antingen en sträng eller ett JSON-objekt.

# <a name="python"></a>[Python](#tab/python)

Kömeddelandet är tillgängligt för funktionen via `func.ServiceBusMessage`en parameter som skrivs som . Service Bus-meddelandet skickas in i funktionen som antingen en sträng eller ett JSON-objekt.

# <a name="java"></a>[Java](#tab/java)

Det inkommande servicebussmeddelandet `ServiceBusQueueMessage` är `ServiceBusTopicMessage` tillgängligt via en eller en parameter.

[Se exemplet för mer information](#example).

---

## <a name="poison-messages"></a>Giftmeddelanden

Hantering av Giftmeddelande kan inte styras eller konfigureras i Azure Functions. Service Bus hanterar giftmeddelanden själv.

## <a name="peeklock-behavior"></a>PeekLock beteende

Funktionskörningen tar emot ett meddelande i [PeekLock-läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Meddelandet `Complete` anropas om funktionen har slutförts eller `Abandon` om funktionen misslyckas. Om funktionen körs längre `PeekLock` än tidsgränsen förnyas låset automatiskt så länge funktionen körs. 

Den `maxAutoRenewDuration` är konfigurerbar i *host.json*, som kartor till [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Det högsta tillåtna värdet för den här inställningen är 5 minuter enligt servicebussdokumentationen, medan du kan öka tidsgränsen för Funktioner från standardvärdet på 5 minuter till 10 minuter. För Service Bus-funktioner vill du inte göra det då, eftersom du skulle överskrida förnyelsegränsen för servicebuss.

## <a name="message-metadata"></a>Metadata för meddelanden

Service Bus-utlösaren innehåller flera [metadataegenskaper](./functions-bindings-expressions-patterns.md#trigger-metadata). Dessa egenskaper kan användas som en del av bindningsuttryck i andra bindningar eller som parametrar i koden. Dessa egenskaper är medlemmar i klassen [BrokeredMessage.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Antalet leveranser.|
|`DeadLetterSource`|`string`|Den döda brevkällan.|
|`ExpiresAtUtc`|`DateTime`|Utgångstiden i UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Den enqueued tiden i UTC.|
|`MessageId`|`string`|Ett användardefinierat värde som Service Bus kan använda för att identifiera dubblettmeddelanden, om det är aktiverat.|
|`ContentType`|`string`|En innehållstypidentifierare som används av avsändaren och mottagaren för programspecifik logik.|
|`ReplyTo`|`string`|Svaret på köadressen.|
|`SequenceNumber`|`Int64`|Det unika nummer som tilldelats ett meddelande av servicebussen.|
|`To`|`string`|Skicka till adress.|
|`Label`|`string`|Den programspecifika etiketten.|
|`CorrelationId`|`string`|Korrelations-ID.|

Se [kodexempel](#example) som använder dessa egenskaper tidigare i den här artikeln.

## <a name="next-steps"></a>Nästa steg

- [Skicka Azure Service Bus-meddelanden från Azure Functions (utdatabindning)](./functions-bindings-service-bus-output.md)
