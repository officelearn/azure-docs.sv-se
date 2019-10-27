---
title: Azure Service Bus bindningar för Azure Functions
description: Förstå hur du använder Azure Service Bus utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, functions, Event Processing, dynamisk beräkning, Server lös arkitektur
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: c39a2e8daf9ca46902cf1a1fac89c59918a6854d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934337"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Service Bus-bindningar i Azure Functions. Azure Functions stöder utlösare och utgående bindningar för Service Bus köer och ämnen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket-funktioner 1. x

Service Bus bindningarna finns i [Microsoft. Azure. WebJobs. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-paketet, version 2. x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paket-funktioner 2. x

Service Bus-bindningarna finns i [Microsoft. Azure. WebJobs. Extensions. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet-paketet, version 3. x. Käll koden för paketet finns i [Azure-WebJobs-SDK GitHub-](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) lagringsplatsen.

> [!NOTE]
> Version 2. x skapar inte ämnet eller prenumerationen som kon figurer ATS i `ServiceBusTrigger`-instansen. Version 2. x är baserad på [Microsoft. Azure. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) och hanterar inte Queue Management.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Använd Service Bus-utlösaren för att svara på meddelanden från en Service Bus kö eller ett ämne. 

## <a name="trigger---example"></a>Utlös – exempel

Se språkspecifika exempel:

* [C#](#trigger---c-example)
* [C#skript (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Utlös – C# exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som läser [meddelandets metadata](#trigger---message-metadata) och loggar ett Service Bus Queue-meddelande:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
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

Det här exemplet är för Azure Functions version 1. x. För att den här koden ska fungera för 2. x:

- [utelämna parametern åtkomst rättigheter](#trigger---configuration)
- ändra typen för logg parametern från `TraceWriter` till `ILogger`
- ändra `log.Info` till `log.LogInformation`

### <a name="trigger---c-script-example"></a>Utlösare – C# skript exempel

I följande exempel visas en Service Bus trigger-bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser [meddelandets metadata](#trigger---message-metadata) och loggar ett Service Bus Queue-meddelande.

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

Här är C# skript koden:

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

### <a name="trigger---f-example"></a>Utlös – F# exempel

I följande exempel visas en Service Bus trigger-bindning i en *Function. JSON* -fil och en [ F# funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen loggar ett Service Bus Queue-meddelande. 

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

Här är F# skript koden:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Utlös – Java-exempel

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

### <a name="trigger---javascript-example"></a>Utlös – JavaScript-exempel

I följande exempel visas en Service Bus trigger-bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser [meddelandets metadata](#trigger---message-metadata) och loggar ett Service Bus Queue-meddelande. 

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

Här är skript koden för Java Script:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---python-example"></a>Utlös – python-exempel

I följande exempel visas hur du läser ett Service Bus-köat via en utlösare.

En Service Bus-bindning definieras i *Function. JSON* där *typ* har angetts till `serviceBusTrigger`.

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

## <a name="trigger---attributes"></a>Utlös ande attribut

I [ C# klass bibliotek](functions-dotnet-class-library.md)använder du följande attribut för att konfigurera en Service Bus-utlösare:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Attributets konstruktor tar namnet på kön eller ämnet och prenumerationen. I Azure Functions version 1. x kan du också ange anslutningens behörigheter. Om du inte anger åtkomst rättigheter är standardvärdet `Manage`. Mer information finns i avsnittet om [Utlösar-konfiguration](#trigger---configuration) .

  Här är ett exempel som visar attributet som används med en sträng parameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Du kan ställa in egenskapen `Connection` för att ange det Service Bus konto som ska användas, som du ser i följande exempel:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Ett fullständigt exempel finns i [Utlös ande- C# exempel](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Ger ett annat sätt att ange det Service Bus konto som ska användas. Konstruktorn tar namnet på en app-inställning som innehåller en Service Bus anslutnings sträng. Attributet kan användas på parameter-, metod-eller klass nivå. I följande exempel visas klass nivå och metod nivå:

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

Service Bus kontot som ska användas fastställs i följande ordning:

* `ServiceBusTrigger` attributets `Connection` egenskap.
* Attributet `ServiceBusAccount` som används för samma parameter som attributet `ServiceBusTrigger`.
* Det `ServiceBusAccount` attribut som används för funktionen.
* Det `ServiceBusAccount` attribut som används för klassen.
* App-inställningen "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Utlös konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `ServiceBusTrigger`-attributet.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste vara inställd på "serviceBusTrigger". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**riktning** | Ej tillämpligt | Måste vara inställt på "in". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar kön eller ämnes meddelandet i funktions koden. Ange till "$return" för att referera till funktionens retur värde. |
|**queueName**|**QueueName**|Namnet på kön som ska övervakas.  Ange endast om övervakning av en kö, inte för ett ämne.
|**topicName**|**TopicName**|Namn på det ämne som ska övervakas. Ange endast om du övervakar ett ämne, inte för en kö.|
|**subscriptionName**|**SubscriptionName**|Namnet på den prenumeration som ska övervakas. Ange endast om du övervakar ett ämne, inte för en kö.|
|**anslutningen**|**Anslutning**|Namnet på en app-inställning som innehåller den Service Bus anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet. Om du till exempel ställer in `connection` till "MyServiceBus" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyServiceBus". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen Service Bus i appens inställning med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutnings strängen måste vara för ett Service Bus-namnområde, inte begränsat till en viss kö eller ett ämne. |
|**accessRights**|**Åtkomst**|Åtkomst behörighet för anslutnings strängen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket anger att `connection` har behörigheten **Hantera** . Om du använder en anslutnings sträng som inte har behörigheten **Hantera** , anger du `accessRights` till "lyssna". I annat fall kan funktions körningen Miss kan försöka utföra åtgärder som kräver behörighet att hantera. Den här egenskapen är inte tillgänglig i Azure Functions version 2. x, eftersom den senaste versionen av Storage SDK inte stöder hanterings åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlös användning

I C# och C# -skript kan du använda följande parameter typer för kön eller ämnes meddelandet:

* `string` – om meddelandet är text.
* `byte[]` – användbart för binära data.
* En anpassad typ – om meddelandet innehåller JSON Azure Functions försöker deserialisera JSON-data.
* `BrokeredMessage` – ger dig det deserialiserade meddelandet med metoden [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Dessa parametrar är för Azure Functions version 1. x. Använd [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage`för 2. x.

I Java Script kan du komma åt kön eller ämnes meddelandet med hjälp av `context.bindings.<name from function.json>`. Service Bus meddelandet skickas till funktionen som antingen en sträng eller ett JSON-objekt.

## <a name="trigger---poison-messages"></a>Utlös ande – Poison-meddelanden

Hantering av skadligt meddelande kan inte styras eller konfigureras i Azure Functions. Service Bus hanterar själva Poison-meddelandena.

## <a name="trigger---peeklock-behavior"></a>Utlös – PeekLock beteende

Functions-körningen tar emot ett meddelande i [PeekLock-läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Den anropar `Complete` i meddelandet om funktionen har slutförts eller anropar `Abandon` om funktionen Miss lyckas. Om funktionen körs längre än `PeekLock` tids gränsen förnyas låset automatiskt så länge funktionen körs. 

`maxAutoRenewDuration` kan konfigureras i *Host. JSON*, som mappar till [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Det högsta tillåtna värdet för den här inställningen är 5 minuter enligt Service Bus-dokumentationen, men du kan öka tids gränsen för funktioner från standardvärdet 5 minuter till 10 minuter. För Service Bus funktioner vill du inte göra det eftersom du skulle överskrida den Service Bus förnyelse gränsen.

## <a name="trigger---message-metadata"></a>Utlös meddelande metadata

Service Bus utlösaren innehåller flera [Egenskaper för metadata](./functions-bindings-expressions-patterns.md#trigger-metadata). Dessa egenskaper kan användas som en del av bindnings uttryck i andra bindningar eller som parametrar i koden. Dessa är egenskaper för klassen [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Antalet leveranser.|
|`DeadLetterSource`|`string`|Källan för obeställbara meddelanden.|
|`ExpiresAtUtc`|`DateTime`|Förfallo tiden i UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Den köade tiden i UTC.|
|`MessageId`|`string`|Ett användardefinierat värde som Service Bus kan använda för att identifiera duplicerade meddelanden, om det är aktiverat.|
|`ContentType`|`string`|En innehålls typ identifierare som används av avsändaren och mottagaren för programspecifik logik.|
|`ReplyTo`|`string`|Svar på Queue-adress.|
|`SequenceNumber`|`Int64`|Det unika nummer som tilldelas ett meddelande av Service Bus.|
|`To`|`string`|Skicka till-adressen.|
|`Label`|`string`|Programspecifik etikett.|
|`CorrelationId`|`string`|Korrelations-ID: t.|

> [!NOTE]
> För närvarande är Service Bus-utlösare som fungerar med sessioner aktiverade köer och prenumerationer som för hands version. Spåra [det här objektet](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) om du vill ha fler uppdateringar om detta. 

Se [kod exempel](#trigger---example) som använder dessa egenskaper tidigare i den här artikeln.

## <a name="trigger---hostjson-properties"></a>Utlös ande-Host. JSON-egenskaper

[Host. JSON](functions-host-json.md#servicebus) -filen innehåller inställningar som styr Service Bus utlösarens beteende.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till motringning som meddelande pumpen ska initiera. Som standard bearbetar Functions-körningen flera meddelanden samtidigt. Ange `maxConcurrentCalls` till 1 för att dirigera körningen för att endast bearbeta en enskild kö eller ett ämne i taget. |
|prefetchCount|Ej tillämpligt|Standard-PrefetchCount som ska användas av den underliggande MessageReceiver.|
|maxAutoRenewDuration|00:05:00|Den längsta tid som meddelande låset ska förnyas automatiskt.|

## <a name="output"></a>Resultat

Använd Azure Service Bus utgående bindning för att skicka kö-eller ämnes meddelanden.

## <a name="output---example"></a>Utdata-exempel

Se språkspecifika exempel:

* [C#](#output---c-example)
* [C#skript (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Utdata- C# exempel

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som skickar ett Service Bus Queue-meddelande:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Exempel på C# utdata-skript

I följande exempel visas en Service Bus utgående bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en timer-utlösare för att skicka ett köat meddelande var 15: e sekund.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Här är C# skript koden som skapar ett enda meddelande:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Här är C# skript koden som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Utdata- F# exempel

I följande exempel visas en Service Bus utgående bindning i en *Function. JSON* -fil och en [ F# skript funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen använder en timer-utlösare för att skicka ett köat meddelande var 15: e sekund.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Här är F# skript koden som skapar ett enda meddelande:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Utdata – Java-exempel

I följande exempel visas en Java-funktion som skickar ett meddelande till en Service Bus kö `myqueue` när den utlöses av en HTTP-begäran.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@QueueOutput` kommentar på funktions parametrar vars värde skrivs till en Service Bus kö.  Parameter typen ska vara `OutputBinding<T>`, där T är en inbyggd Java-typ för en POJO.

Java-funktioner kan också skriva till ett Service Bus ämne. I följande exempel används den `@ServiceBusTopicOutput` kommentaren för att beskriva konfigurationen för utgående bindning. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

### <a name="output---javascript-example"></a>Exempel på utdata-JavaScript

I följande exempel visas en Service Bus utgående bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en timer-utlösare för att skicka ett köat meddelande var 15: e sekund.

Här är bindnings data i *Function. JSON* -filen:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Här är skript koden för Java Script som skapar ett enda meddelande:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Här är skript koden för Java Script som skapar flera meddelanden:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Utdata – python-exempel

Följande exempel visar hur du skriver ut till en Service Bus-kö i python.

En bindnings definition för ServiceBue definieras i *Function. JSON* där *typ* har angetts till `serviceBus`.

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
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

I  *_\_init_\_. py*kan du skriva ut ett meddelande till kön genom att skicka ett värde till `set`-metoden.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

## <a name="output---attributes"></a>Utdata-attribut

Använd [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar namnet på kön eller ämnet och prenumerationen. Du kan också ange anslutningens behörigheter. Hur du väljer inställningen åtkomst rättigheter beskrivs i avsnittet [utdata-konfiguration](#output---configuration) . Här är ett exempel som visar attributet som används för funktionens retur värde:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Du kan ställa in egenskapen `Connection` för att ange det Service Bus konto som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [utdata- C# exempel](#output---c-example).

Du kan använda attributet `ServiceBusAccount` för att ange det Service Bus konto som ska användas på klass-, metod-eller parameter nivå.  Mer information finns i avsnittet om [Utlösar-attribut](#trigger---attributes).

## <a name="output---configuration"></a>Utdata-konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `ServiceBus`-attributet.

|function. JSON-egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste vara inställd på "Service Bus". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal.|
|**riktning** | Ej tillämpligt | Måste anges till "out". Den här egenskapen anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar kön eller ämnet i funktions koden. Ange till "$return" för att referera till funktionens retur värde. |
|**queueName**|**QueueName**|Köns namn.  Ange endast om köa meddelanden ska skickas, inte för ett ämne.
|**topicName**|**TopicName**|Namn på det ämne som ska övervakas. Ange endast om meddelande ämnen skickas, inte för en kö.|
|**anslutningen**|**Anslutning**|Namnet på en app-inställning som innehåller den Service Bus anslutnings sträng som ska användas för den här bindningen. Om appens inställnings namn börjar med "AzureWebJobs" kan du bara ange resten av namnet. Om du till exempel ställer in `connection` till "MyServiceBus" söker Functions-körningen efter en app-inställning med namnet "AzureWebJobsMyServiceBus". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen Service Bus i appens inställning med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutnings strängen måste vara för ett Service Bus-namnområde, inte begränsat till en viss kö eller ett ämne.|
|**accessRights**|**Åtkomst**|Åtkomst behörighet för anslutnings strängen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket anger att `connection` har behörigheten **Hantera** . Om du använder en anslutnings sträng som inte har behörigheten **Hantera** , anger du `accessRights` till "lyssna". I annat fall kan funktions körningen Miss kan försöka utföra åtgärder som kräver behörighet att hantera. Den här egenskapen är inte tillgänglig i Azure Functions version 2. x, eftersom den senaste versionen av Storage SDK inte stöder hanterings åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utmatnings användning

I Azure Functions 1. x skapar körnings kön om den inte finns och du har angett `accessRights` till `manage`. I functions version 2. x måste kön eller ämnet redan finnas. Om du anger en kö eller ett ämne som inte finns fungerar inte funktionen. 

I C# och C# -skript kan du använda följande parameter typer för utgående bindning:

* `out T paramName` - `T` kan vara vilken JSON-serialiserbar typ som helst. Om parametervärdet är null när funktionen avslutas, skapar funktionen ett meddelande med ett null-objekt.
* `out string`-om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out byte[]`-om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out BrokeredMessage`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 1. x)
* `out Message`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 2. x)
* `ICollector<T>` eller `IAsyncCollector<T>` – för att skapa flera meddelanden. Ett meddelande skapas när du anropar metoden `Add`.

När du arbetar C# med funktioner:

* Asynkrona funktioner behöver ett retur värde eller `IAsyncCollector` i stället för en `out`-parameter.

* För att få åtkomst till sessions-ID: t binder du till en [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) typ och använder egenskapen `sessionId`.

I Java Script kan du komma åt kön eller ämnet med hjälp av `context.bindings.<name from function.json>`. Du kan tilldela en sträng, en byte mat ris eller ett JavaScript-objekt (avserialiserat till JSON) till `context.binding.<name>`.

Om du vill skicka ett meddelande till en kö som är aktiveC# rad i andra språk än använder du [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) i stället för den inbyggda utgående bindningen.

## <a name="exceptions-and-return-codes"></a>Undantag och retur koder

| Enheten | Referens |
|---|---|
| Service Bus | [Service Bus felkoder](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus gränser](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host. JSON-inställningar

I det här avsnittet beskrivs globala konfigurations inställningar som är tillgängliga för den här bindningen i version 2. x. Exemplet Host. JSON-filen nedan innehåller bara version 2. x-inställningarna för den här bindningen. Mer information om globala konfigurations inställningar i version 2. x finns i [Host. JSON-referens för Azure Functions version 2. x](functions-host-json.md).

> [!NOTE]
> En referens för Host. json i functions 1. x finns i [Host. JSON-referensen för Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|Egenskap  |Standard | Beskrivning |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Den längsta tid som meddelande låset ska förnyas automatiskt.|
|Automatisk|sant|Om utlösaren omedelbart ska markeras som slutförd (komplettera automatiskt) eller vänta på bearbetningen slutförs.|
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till motringning som meddelande pumpen ska initiera. Som standard bearbetar Functions-körningen flera meddelanden samtidigt. Ange `maxConcurrentCalls` till 1 för att dirigera körningen för att endast bearbeta en enskild kö eller ett ämne i taget. |
|prefetchCount|Ej tillämpligt|Standard-PrefetchCount som ska användas av den underliggande MessageReceiver.|


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
