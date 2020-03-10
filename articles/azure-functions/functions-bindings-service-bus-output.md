---
title: Azure Service Bus-bindningar för Azure Functions
description: Lär dig att skicka Azure Service Bus meddelanden från Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357014"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus utgående bindning för Azure Functions

Använd Azure Service Bus-utdatabindning att skicka meddelanden från kön eller ämnet.

Information om konfiguration och konfigurations information finns i [översikten](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

I följande exempel visas en Service Bus utgående bindning i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett kömeddelande var 15: e sekund.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Service Bus utgående bindning i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett kömeddelande var 15: e sekund.

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

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar hur du skriver ut till en Service Bus kö i python.

En Service Bus bindnings definition definieras i *Function. JSON* där *typ* är inställd på `serviceBus`.

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

# <a name="java"></a>[Java](#tab/java)

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

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)i [ C# klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktorn tar namnet på kön eller ämnet och prenumerationen. Du kan också ange anslutningens åtkomsträttigheter. Hur du väljer inställningen åtkomst rättigheter beskrivs i avsnittet [utdata-konfiguration](#configuration) . Här är ett exempel som visar de attribut som används i det returnera värdet för funktionen:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Du kan ange egenskapen `Connection` för att ange namnet på en app-inställning som innehåller den Service Bus anslutnings sträng som ska användas, som du ser i följande exempel:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [utdata-exempel](#example).

Du kan använda attributet `ServiceBusAccount` för att ange det Service Bus konto som ska användas på klass-, metod-eller parameter nivå.  Mer information finns i avsnittet om [Utlösar-attribut](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput`-och `ServiceBusTopicOutput`-anteckningarna är tillgängliga för att skriva ett meddelande som ett funktions resultat. Parametern dekorerade dessa anteckningar måste deklareras som en `OutputBinding<T>` där `T` är den typ som motsvarar meddelandets typ.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i filen *Function. JSON* och `ServiceBus`-attributet.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
|**typ** | Saknas | Måste anges till ”serviceBus”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste anges till ”ut”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön eller ämnet meddelandet i funktionskoden. Ange till ”$return” att referera till returvärde för funktion. |
|**queueName**|**QueueName**|Namnet på kön.  Ange endast om skickar Kömeddelanden, inte för ett ämne.
|**topicName**|**TopicName**|Namn på ämnet. Ange endast om avsnittet meddelanden, inte för en kö.|
|**anslutningen**|**Anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträngen för den här bindningen. Om namnet på inställningen börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Om du till exempel anger `connection` till "MyServiceBus" söker Functions runtime efter en app-inställning med namnet "AzureWebJobsMyServiceBus". Om du lämnar `connection` tomt använder Functions-körningen standard anslutnings strängen Service Bus i appens inställning med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutnings sträng följer du stegen som visas i [Hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Anslutningssträngen måste vara för en Service Bus-namnområde, inte begränsat till en viss kö eller ämne.|
|**accessRights**|**Åtkomst**|Behörigheten för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket anger att `connection` har behörigheten **Hantera** . Om du använder en anslutnings sträng som inte har behörigheten **Hantera** , anger du `accessRights` till "lyssna". Annars kan hantera Functions runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2. x och högre är den här egenskapen inte tillgänglig eftersom den senaste versionen av Service Bus SDK inte stöder hanterings åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

I Azure Functions 1. x skapar körnings kön om den inte finns och du har angett `accessRights` till `manage`. I functions version 2. x och senare måste kön eller ämnet redan finnas. Om du anger en kö eller ett ämne som inte finns fungerar inte funktionen. 

# <a name="c"></a>[C#](#tab/csharp)

Använd följande parameter typer för utgående bindning:

* `out T paramName` - `T` kan vara vilken JSON-serialiserbar typ som helst. Om värdet för parametern är null när funktionen avslutas skapar funktioner meddelandet med ett null-objekt.
* `out string`-om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out byte[]`-om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out BrokeredMessage`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 1. x)
* `out Message`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 2. x och högre)
* `ICollector<T>` eller `IAsyncCollector<T>` – för att skapa flera meddelanden. Ett meddelande skapas när du anropar metoden `Add`.

När du arbetar C# med funktioner:

* Asynkrona funktioner behöver ett retur värde eller `IAsyncCollector` i stället för en `out`-parameter.

* För att få åtkomst till sessions-ID: t binder du till en [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) typ och använder egenskapen `sessionId`.

# <a name="c-script"></a>[C#Över](#tab/csharp-script)

Använd följande parameter typer för utgående bindning:

* `out T paramName` - `T` kan vara vilken JSON-serialiserbar typ som helst. Om värdet för parametern är null när funktionen avslutas skapar funktioner meddelandet med ett null-objekt.
* `out string`-om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out byte[]`-om parametervärdet är null när funktionen avslutas, skapas inget meddelande i functions.
* `out BrokeredMessage`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 1. x)
* `out Message`-om parametervärdet är null när funktionen avslutas, skapar inte Functions ett meddelande (för funktioner 2. x och högre)
* `ICollector<T>` eller `IAsyncCollector<T>` – för att skapa flera meddelanden. Ett meddelande skapas när du anropar metoden `Add`.

När du arbetar C# med funktioner:

* Asynkrona funktioner behöver ett retur värde eller `IAsyncCollector` i stället för en `out`-parameter.

* För att få åtkomst till sessions-ID: t binder du till en [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) typ och använder egenskapen `sessionId`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Använd `context.bindings.<name from function.json>`för att komma åt kön eller ämnet. Du kan tilldela en sträng, en byte mat ris eller ett JavaScript-objekt (avserialiserat till JSON) till `context.binding.<name>`.

# <a name="python"></a>[Python](#tab/python)

Använd [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) i stället för den inbyggda utgående bindningen.

# <a name="java"></a>[Java](#tab/java)

Använd [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) i stället för den inbyggda utgående bindningen.

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Service Bus | [Service Bus felkoder](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus gränser](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Host.JSON-inställningar

I det här avsnittet beskrivs de globala konfigurations inställningarna som är tillgängliga för den här bindningen i version 2. x och högre. Exempel filen Host. JSON nedan innehåller bara inställningarna för den här bindningen. Mer information om globala konfigurations inställningar finns i [Host. JSON-referens för Azure Functions version](functions-host-json.md).

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
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Egenskap  |Default | Beskrivning |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Maximal varaktighet inom vilken meddelandelåset förnyas automatiskt.|
|automatisk komplettering|true|Om utlösaren omedelbart ska markera meddelandet som slutfört (komplettera automatiskt) eller vänta tills funktionen avslutas för att anropa slutförd.|
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till återanrop som meddelandet pump ska starta. Som standard bearbetar funktionskörningen flera meddelanden samtidigt. Ange `maxConcurrentCalls` till 1 om du vill att körningen bara ska bearbeta en enskild kö eller ett ämne i taget. |

## <a name="next-steps"></a>Nästa steg

- [Köra en funktion när en Service Bus kö eller ett ämnes meddelande skapas (utlösare)](./functions-bindings-service-bus-trigger.md)
