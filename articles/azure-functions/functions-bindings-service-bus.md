---
title: Azure Service Bus-bindningar för Azure Functions
description: Förstå hur du använder Azure Service Bus-utlösare och bindningar i Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktioner, händelsebearbetning, dynamisk beräkning, serverlös arkitektur
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: ee5b11bc04a7e13354c30b64dc55c165eea4f028
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303973"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Service Bus-bindningar i Azure Functions. Azure Functions stöder Utlös och utdatabindningar för Service Bus-köer och ämnen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paket - instruktion i 1.x-funktioner

Service Bus-bindningar finns i den [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-paketet, version 2.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paket - fungerar 2.x

Service Bus-bindningar finns i den [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-paketet, version 3.x. Källkoden för paketet finns i den [azure webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/) GitHub-lagringsplatsen.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Utlösare

Använda Service Bus-utlösare ska svara på meddelanden från en Service Bus-kö eller ett ämne. 

## <a name="trigger---example"></a>Utlösare - exempel

Se exempel språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Utlösare – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som läser [meddelande metadata](#trigger---message-metadata) och loggar ett meddelande med Service Bus-kö:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
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

Det här exemplet är för Azure Functions-version 1.x; för 2.x, [utelämna parametern åtkomst rights](#trigger---configuration).
 
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

### <a name="trigger---f-example"></a>Utlösare – F #-exempel

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [F #-funktion](functions-reference-fsharp.md) som använder bindningen. Funktionen loggar ett meddelande med Service Bus-kö. 

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

Här är F #-skriptkoden:

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
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

### <a name="trigger---java-example"></a>Utlösare - Java-exemplet

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [Java funktionen](functions-reference-java.md) som använder bindningen. Funktionen som utlöses av ett meddelande placeras i en Service Bus-kö och funktionen loggar kömeddelandet.

Här är bindningsdata i den *function.json* fil:

```json
{
"bindings": [
    {
    "queueName": "myqueuename",
    "connection": "MyServiceBusConnection",
    "name": "msg",
    "type": "ServiceBusQueueTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Här är den Java-kod:

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

## <a name="trigger---attributes"></a>Utlösare - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en Service Bus-utlösare:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)

  Attributets konstruktorn tar namnet på kön eller ämnet och prenumerationen. I Azure Functions-version 1.x, du kan också ange anslutningens åtkomsträttigheter. Om du inte anger rätt, är standardvärdet `Manage`. Mer information finns i den [utlösare - konfigurationen](#trigger---configuration) avsnittet.

  Här är ett exempel som visar det attribut som används med en strängparameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Du kan ange den `Connection` egenskapen att ange det Service Bus-kontot som ska användas, som visas i följande exempel:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Ett komplett exempel finns i [utlösare – C#-exempel](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)

  Innehåller ett annat sätt att ange det Service Bus-kontot som ska användas. Konstruktorn tar namnet på en appinställning som innehåller en Service Bus-anslutningssträng. Attributet kan användas på parametern, metoden eller klassnivå. I följande exempel visas klassen och metoden:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
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
|**typ** | Saknas | Måste anges till ”serviceBusTrigger”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till ”in”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön eller ämnet meddelandet i funktionskoden. Ange till ”$return” att referera till returvärde för funktion. | 
|**Könamn**|**Könamn**|Namnet på kön som ska övervaka.  Ange bara om övervakning av en kö, inte för ett ämne.
|**topicName**|**topicName**|Namnet på ämnet du övervakar. Ange bara om övervakning av ett ämne, inte för en kö.|
|**subscriptionName**|**subscriptionName**|Namnet på prenumerationen som ska övervaka. Ange bara om övervakning av ett ämne, inte för en kö.|
|**anslutning**|**anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträngen för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Exempel: Om du ställer in `connection` till ”MyServiceBus” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyServiceBus”. Om du lämnar `connection` tom funktionskörningen använder standard Service Bus-anslutningssträngen i appinställningen som heter ”AzureWebJobsServiceBus”.<br><br>Om du vill ha en anslutningssträng, följ anvisningarna som visas på [hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Anslutningssträngen måste vara för en Service Bus-namnområde, inte begränsat till en viss kö eller ämne. |
|**accessRights**|**Åtkomst**|Behörigheten för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** behörigheten, `accessRights` ”lyssna”. Annars kan hantera Functions runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2.x kan den här egenskapen är inte tillgänglig eftersom den senaste versionen av Storage SDK inte stöder hantera åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösare - användning

I C# och C#-skript, kan du använda följande parametertyper för kön eller ämnet meddelandet:

* `string` -Om meddelandet är text.
* `byte[]` -Användbart för binära data.
* En anpassad typ - om meddelandet innehåller JSON, Azure Functions försöker deserialisera JSON-data.
* `BrokeredMessage` – Ger dig avserialiserade meddelandet med den [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metod.

De här parametrarna används för Azure Functions-version 1.x; 2.x kan använda [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage`.

Få åtkomst till kön eller ämnet meddelandet i JavaScript, med hjälp av `context.bindings.<name from function.json>`. Service Bus-meddelande skickas till funktionen en sträng eller en JSON-objekt.

## <a name="trigger---poison-messages"></a>Utlösare – skadliga meddelanden

Hantering av skadligt meddelande kan inte styras eller konfigurerats i Azure Functions. Service Bus hanterar skadliga meddelanden själva.

## <a name="trigger---peeklock-behavior"></a>Utlösare – PeekLock beteende

Functions-körning tar emot ett meddelande i [PeekLock läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Den anropar `Complete` på meddelande om funktionen har slutförts eller anrop `Abandon` om misslyckas åtgärden. Om funktionen körs längre än den `PeekLock` tidsgräns, låset förnyas automatiskt så länge funktionen körs. 

Functions 1.x kan du konfigurera `autoRenewTimeout` i *host.json*, som mappar till [OnMessageOptions.AutoRenewTimeout](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout?view=azure-dotnet#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout). Högsta tillåtna för den här inställningen är 5 minuter enligt dokumentation för Service Bus, medan du kan öka tidsgränsen funktioner från standardvärdet 5 minuter till 10 minuter. För Service Bus-funktionerna skulle du vill göra det sedan eftersom du skulle överskrida gränsen för Service Bus-förnyelse.

## <a name="trigger---message-metadata"></a>Utlösare - meddelande metadata

Service Bus-utlösare innehåller flera [metadataegenskaper](functions-triggers-bindings.md#binding-expressions---trigger-metadata). De här egenskaperna kan användas som en del av bindning uttryck i andra bindningar eller som parametrar i din kod. Dessa är egenskaper för den [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) klass.

|Egenskap |Typ|Beskrivning|
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
|`Properties`|`IDictionary<String,Object>`|Programmet specifika meddelandeegenskaper.|

Se [kodexempel](#trigger---example) som använder de här egenskaperna tidigare i den här artikeln.

## <a name="trigger---hostjson-properties"></a>Utlösare - host.json egenskaper

Den [host.json](functions-host-json.md#servicebus) filen innehåller inställningar som styr beteendet för Service Bus-utlösare.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Resultat

Använd Azure Service Bus-utdatabindning att skicka meddelanden från kön eller ämnet.

## <a name="output---example"></a>Utdata - exempel

Se exempel språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output--java-example)

### <a name="output---c-example"></a>Resultat – C#-exempel

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skickar ett meddelande för Service Bus-kö:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
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
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Här är C#-skriptkoden som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Resultat – F #-exempel

I följande exempel visas en Service Bus-utdatabindning i en *function.json* fil och en [F #-skriptfunktion](functions-reference-fsharp.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett kömeddelande var 15: e sekund.

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

Här är F #-skriptkoden som skapar ett enda meddelande:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
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

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs).

Attributets konstruktorn tar namnet på kön eller ämnet och prenumerationen. Du kan också ange anslutningens åtkomsträttigheter. Så här väljer du åtkomsträttigheterna inställningen förklaras i den [utdata - konfigurationen](#output---configuration) avsnittet. Här är ett exempel som visar de attribut som används i det returnera värdet för funktionen:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Du kan ange den `Connection` egenskapen att ange det Service Bus-kontot som ska användas, som visas i följande exempel:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
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
|**typ** | Saknas | Måste anges till ”serviceBus”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till ”ut”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön eller ämnet i funktionskoden. Ange till ”$return” att referera till returvärde för funktion. | 
|**Könamn**|**Könamn**|Namnet på kön.  Ange endast om skickar Kömeddelanden, inte för ett ämne.
|**topicName**|**topicName**|Namnet på ämnet du övervakar. Ange endast om avsnittet meddelanden, inte för en kö.|
|**anslutning**|**anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträngen för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Exempel: Om du ställer in `connection` till ”MyServiceBus” funktionskörningen söker efter en app som inställning som heter ”AzureWebJobsMyServiceBus”. Om du lämnar `connection` tom funktionskörningen använder standard Service Bus-anslutningssträngen i appinställningen som heter ”AzureWebJobsServiceBus”.<br><br>Om du vill ha en anslutningssträng, följ anvisningarna som visas på [hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Anslutningssträngen måste vara för en Service Bus-namnområde, inte begränsat till en viss kö eller ämne.|
|**accessRights**|**Åtkomst**|Behörigheten för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** behörigheten, `accessRights` ”lyssna”. Annars kan hantera Functions runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2.x kan den här egenskapen är inte tillgänglig eftersom den senaste versionen av Storage SDK inte stöder hantera åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I Azure Functions 1.x, körningen skapar kön om det inte finns och du har angett `accessRights` till `manage`. I funktioner version måste 2.x, kön eller ämnet redan finnas; funktionen kommer att misslyckas om du anger en kö eller ämne som inte finns. 

I C# och C#-skript, kan du använda följande parametertyper av för utdata-bindning:

* `out T paramName` - `T` kan vara valfri JSON-serialiserbara. Om värdet för parametern är null när funktionen avslutas skapar funktioner meddelandet med ett null-objekt.
* `out string` -Om värdet för parametern är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `out byte[]` -Om värdet för parametern är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `out BrokeredMessage` -Om värdet för parametern är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `ICollector<T>` eller `IAsyncCollector<T>` – för att skapa flera meddelanden. Ett meddelande skapas när du anropar den `Add` metoden.

I async-funktioner, använder du det returnera värdet eller `IAsyncCollector` i stället för en `out` parametern.

De här parametrarna används för Azure Functions-version 1.x; 2.x kan använda [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i stället för `BrokeredMessage`.

I JavaScript, få åtkomst till kön eller ämnet med hjälp av `context.bindings.<name from function.json>`. Du kan tilldela en sträng, en bytematris eller ett Javascript-objekt (deserialisera till JSON) till `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Service Bus | [Service Bus-felkoder](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus-gränser](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
