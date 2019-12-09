---
title: Azure Service Bus-bindningar för Azure Functions
description: Förstå hur du använder Azure Service Bus-utlösare och bindningar i Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: c4e3ce148b3cc2db9681bd9c7a7ba0e33335d2cc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925766"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Service Bus-bindningar i Azure Functions. Azure Functions stöder Utlös och utdatabindningar för Service Bus-köer och ämnen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Service Bus-bindningar finns i den [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-paketet, version 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

Service Bus-bindningar finns i den [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet-paketet, version 3.x. Käll koden för paketet finns i GitHub-lagringsplatsen [Azure-Functions-Service Bus-Extensions](https://github.com/Azure/azure-functions-servicebus-extension) .

> [!NOTE]
> Version 2. x och högre skapar inte ämnet eller prenumerationen som kon figurer ATS i `ServiceBusTrigger`-instansen. De här versionerna är baserade på [Microsoft. Azure. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) som inte hanterar hantering av köer.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Använda Service Bus-utlösare ska svara på meddelanden från en Service Bus-kö eller ett ämne. 

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser [meddelande metadata](#trigger---message-metadata) och loggar ett meddelande med Service Bus-kö:

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

### <a name="trigger---c-script-example"></a>Utlösare – exempel på C#-skript

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen läser [meddelande metadata](#trigger---message-metadata) och loggar ett meddelande med Service Bus-kö.

Här är bindningsdata i den *function.json* fil:

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

### <a name="trigger---f-example"></a>Utlösare – F# exempel

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [ F# funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen loggar ett meddelande med Service Bus-kö. 

Här är bindningsdata i den *function.json* fil:

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

Här är den F# skript kod:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

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

### <a name="trigger---javascript-example"></a>Utlösare – JavaScript-exempel

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen läser [meddelande metadata](#trigger---message-metadata) och loggar ett meddelande med Service Bus-kö. 

Här är bindningsdata i den *function.json* fil:

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

## <a name="trigger---attributes"></a>Utlösare - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en Service Bus-utlösare:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Attributets konstruktorn tar namnet på kön eller ämnet och prenumerationen. I Azure Functions-version 1.x, du kan också ange anslutningens åtkomsträttigheter. Om du inte anger rätt, är standardvärdet `Manage`. Mer information finns i den [utlösare - konfigurationen](#trigger---configuration) avsnittet.

  Här är ett exempel som visar det attribut som används med en strängparameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Du kan ange den `Connection` egenskapen att ange det Service Bus-kontot som ska användas, som visas i följande exempel:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).

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

* Den `ServiceBusTrigger` attributets `Connection` egenskapen.
* Den `ServiceBusAccount` attribut som används i samma parameter som den `ServiceBusTrigger` attribut.
* Den `ServiceBusAccount` attribut som används i funktionen.
* Den `ServiceBusAccount` attribut som tillämpas på klassen.
* Appinställningen ”AzureWebJobsServiceBus”.

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `ServiceBusTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste anges till ”serviceBusTrigger”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Ej tillämpligt | Måste anges till ”in”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar kön eller ämnet meddelandet i funktionskoden. Ange till ”$return” att referera till returvärde för funktion. |
|**Könamn**|**Könamn**|Namnet på kön som ska övervaka.  Ange bara om övervakning av en kö, inte för ett ämne.
|**topicName**|**topicName**|Namnet på ämnet du övervakar. Ange bara om övervakning av ett ämne, inte för en kö.|
|**subscriptionName**|**subscriptionName**|Namnet på prenumerationen som ska övervaka. Ange bara om övervakning av ett ämne, inte för en kö.|
|**anslutning**|**Anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträngen för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Exempel: Om du ställer in `connection` till ”MyServiceBus” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyServiceBus”. Om du lämnar `connection` tom funktionskörningen använder standard Service Bus-anslutningssträngen i appinställningen som heter ”AzureWebJobsServiceBus”.<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutningssträngen måste vara för en Service Bus-namnområde, inte begränsat till en viss kö eller ämne. |
|**accessRights**|**Åtkomst**|Behörigheten för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** behörigheten, `accessRights` ”lyssna”. Annars kan hantera Functions runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2. x och högre är den här egenskapen inte tillgänglig eftersom den senaste versionen av Storage SDK inte stöder hanterings åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

I C# och C#-skript, kan du använda följande parametertyper för kön eller ämnet meddelandet:

* `string` -Om meddelandet är text.
* `byte[]` -Användbart för binära data.
* En anpassad typ - om meddelandet innehåller JSON, Azure Functions försöker deserialisera JSON-data.
* `BrokeredMessage` – ger dig det deserialiserade meddelandet med metoden [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Dessa parametrar är för Azure Functions version 1. x. för 2. x och högre använder du [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage`.

Få åtkomst till kön eller ämnet meddelandet i JavaScript, med hjälp av `context.bindings.<name from function.json>`. Service Bus-meddelande skickas till funktionen en sträng eller en JSON-objekt.

## <a name="trigger---poison-messages"></a>Utlösare – skadliga meddelanden

Hantering av skadligt meddelande kan inte styras eller konfigurerats i Azure Functions. Service Bus hanterar skadliga meddelanden själva.

## <a name="trigger---peeklock-behavior"></a>Utlösare – PeekLock beteende

Functions-körning tar emot ett meddelande i [PeekLock läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Den anropar `Complete` på meddelande om funktionen har slutförts eller anrop `Abandon` om misslyckas åtgärden. Om funktionen körs längre än den `PeekLock` tidsgräns, låset förnyas automatiskt så länge funktionen körs. 

`maxAutoRenewDuration` kan konfigureras i *Host. JSON*, som mappar till [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Högsta tillåtna för den här inställningen är 5 minuter enligt dokumentation för Service Bus, medan du kan öka tidsgränsen funktioner från standardvärdet 5 minuter till 10 minuter. För Service Bus-funktionerna skulle du vill göra det sedan eftersom du skulle överskrida gränsen för Service Bus-förnyelse.

## <a name="trigger---message-metadata"></a>Utlösare - meddelande metadata

Service Bus-utlösare innehåller flera [metadataegenskaper](./functions-bindings-expressions-patterns.md#trigger-metadata). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa är egenskaper för den [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) klass.

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Antal leveranser.|
|`DeadLetterSource`|`string`|Källan för obeställbara meddelanden.|
|`ExpiresAtUtc`|`DateTime`|Förfallotid i UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Kötid i UTC.|
|`MessageId`|`string`|Ett användardefinierat värde som Service Bus kan använda för att identifiera duplicerade meddelanden, om aktiverad.|
|`ContentType`|`string`|En innehållstyp identifierare som används av avsändaren och mottagaren för specifika programlogik.|
|`ReplyTo`|`string`|Svara till kö-adress.|
|`SequenceNumber`|`Int64`|Unikt nummer som tilldelats ett meddelande av Service Bus.|
|`To`|`string`|Skicka till adress.|
|`Label`|`string`|Den programspecifik etiketten.|
|`CorrelationId`|`string`|Korrelations-ID|

> [!NOTE]
> För närvarande är Service Bus-utlösare som fungerar med sessioner aktiverade köer och prenumerationer som för hands version. Spåra [det här objektet](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) om du vill ha fler uppdateringar om detta. 

Se [kodexempel](#trigger---example) som använder de här egenskaperna tidigare i den här artikeln.

## <a name="output"></a>Resultat

Använd Azure Service Bus-utdatabindning att skicka meddelanden från kön eller ämnet.

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skickar ett meddelande för Service Bus-kö:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Resultat – exempel på C#-skript

I följande exempel visas en Service Bus-utdatabindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett kömeddelande var 15: e sekund.

Här är bindningsdata i den *function.json* fil:

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

Här är C#-skriptkoden som skapar ett enda meddelande:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Här är C#-skriptkoden som skapar flera meddelanden:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

### <a name="output---f-example"></a>Utdata - F# exempel

I följande exempel visas en Service Bus-utdatabindning i en *function.json* fil och en [ F# skript funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett kömeddelande var 15: e sekund.

Här är bindningsdata i den *function.json* fil:

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

Här är F# skriva kod som skapar ett enda meddelande:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Resultat – Java-exemplet

I följande exempel visas en Java-funktion som skickar ett meddelande till en Service Bus-kö `myqueue` när det utlöses av en HTTP-begäran.

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

 I den [Java functions runtime-biblioteket](/java/api/overview/azure/functions/runtime), använda den `@QueueOutput` anteckningen i funktionsparametrar vars värde skulle skrivas till en Service Bus-kö.  Parametertypen ska vara `OutputBinding<T>`, där T är alla interna Java-typer av en POJO.

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

### <a name="output---javascript-example"></a>Resultat – JavaScript-exempel

I följande exempel visas en Service Bus-utdatabindning i en *function.json* fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett kömeddelande var 15: e sekund.

Här är bindningsdata i den *function.json* fil:

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

Här är JavaScript-skriptkod som skapar ett enda meddelande:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Här är JavaScript-skriptkod som skapar flera meddelanden:

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

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Attributets konstruktorn tar namnet på kön eller ämnet och prenumerationen. Du kan också ange anslutningens åtkomsträttigheter. Så här väljer du åtkomsträttigheterna inställningen förklaras i den [utdata - konfigurationen](#output---configuration) avsnittet. Här är ett exempel som visar de attribut som används i det returnera värdet för funktionen:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Du kan ange den `Connection` egenskapen att ange det Service Bus-kontot som ska användas, som visas i följande exempel:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Ett komplett exempel finns i [resultat – C#-exempel](#output---c-example).

Du kan använda den `ServiceBusAccount` attribut som anger Service Bus-konto som ska användas på klass, metod eller parametern-nivå.  Mer information finns i [utlösare - attribut](#trigger---attributes).

## <a name="output---configuration"></a>Utdata - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `ServiceBus` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Ej tillämpligt | Måste anges till ”serviceBus”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Ej tillämpligt | Måste anges till ”ut”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Ej tillämpligt | Namnet på variabeln som representerar kön eller ämnet i funktionskoden. Ange till ”$return” att referera till returvärde för funktion. |
|**Könamn**|**Könamn**|Namnet på kön.  Ange endast om skickar Kömeddelanden, inte för ett ämne.
|**topicName**|**topicName**|Namnet på ämnet du övervakar. Ange endast om avsnittet meddelanden, inte för en kö.|
|**anslutning**|**Anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträngen för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Exempel: Om du ställer in `connection` till ”MyServiceBus” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyServiceBus”. Om du lämnar `connection` tom funktionskörningen använder standard Service Bus-anslutningssträngen i appinställningen som heter ”AzureWebJobsServiceBus”.<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutningssträngen måste vara för en Service Bus-namnområde, inte begränsat till en viss kö eller ämne.|
|**accessRights**|**Åtkomst**|Behörigheten för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** behörigheten, `accessRights` ”lyssna”. Annars kan hantera Functions runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2. x och högre är den här egenskapen inte tillgänglig eftersom den senaste versionen av Storage SDK inte stöder hanterings åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I Azure Functions 1.x, körningen skapar kön om det inte finns och du har angett `accessRights` till `manage`. I functions version 2. x och senare måste kön eller ämnet redan finnas. Om du anger en kö eller ett ämne som inte finns fungerar inte funktionen. 

I C# och C#-skript, kan du använda följande parametertyper av för utdata-bindning:

* `out T paramName` - `T` kan vara valfri JSON-serialiserbara. Om värdet för parametern är null när funktionen avslutas skapar funktioner meddelandet med ett null-objekt.
* `out string` -Om värdet för parametern är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `out byte[]` -Om värdet för parametern är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `out BrokeredMessage`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 1. x)
* `out Message`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 2. x och högre)
* `ICollector<T>` eller `IAsyncCollector<T>` – för att skapa flera meddelanden. Ett meddelande skapas när du anropar den `Add` metoden.

När du arbetar C# med funktioner:

* Asynkrona funktioner behöver ett retur värde eller `IAsyncCollector` i stället för en `out`-parameter.

* För att få åtkomst till sessions-ID: t binder du till en [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) typ och använder egenskapen `sessionId`.

I JavaScript, få åtkomst till kön eller ämnet med hjälp av `context.bindings.<name from function.json>`. Du kan tilldela en sträng, en byte mat ris eller ett JavaScript-objekt (avserialiserat till JSON) till `context.binding.<name>`.

Om du vill skicka ett meddelande till en kö som är aktiveC# rad i andra språk än använder du [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) i stället för den inbyggda utgående bindningen.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Service Bus | [Service Bus-felkoder](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus-gränser](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exempel filen Host. JSON nedan innehåller bara inställningarna för den här bindningen. Mer information om globala konfigurations inställningar finns i [Host. JSON-referens för Azure Functions version](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x, se [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

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
|maxAutoRenewDuration|00:05:00|Maximal varaktighet inom vilken meddelandelåset förnyas automatiskt.|
|automatisk komplettering|sant|Utlösaren ska om omedelbart Markera som slutförd (Komplettera automatiskt) eller vänta på att processen för att anropa klar.|
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till återanrop som meddelandet pump ska starta. Som standard bearbetar funktionskörningen flera meddelanden samtidigt. För att dirigera körning för att bearbeta en enskild kö eller ett ämne meddelande i taget, ange `maxConcurrentCalls` till 1. |
|prefetchCount|Ej tillämpligt|Standard PrefetchCount som ska användas av den underliggande MessageReceiver.|


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
