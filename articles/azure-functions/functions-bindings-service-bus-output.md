---
title: Azure Service Bus-bindningar för Azure-funktioner
description: Lär dig att skicka Azure Service Bus-meddelanden från Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277445"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Utdatabindning för Azure Service Bus för Azure-funktioner

Använd Azure Service Bus-utdatabindning för att skicka kö- eller ämnesmeddelanden.

Information om inställnings- och konfigurationsinformation finns i [översikten](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som skickar ett kömeddelande för Service Bus:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

I följande exempel visas en Service Bus-utdatabindning i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en timerutlösare för att skicka ett kömeddelande var 15:e sekund.

Här är bindningsdata i *filen function.json:*

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

Här är C#-skriptkod som skapar ett enda meddelande:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Här är C#-skriptkod som skapar flera meddelanden:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en Service Bus-utdatabindning i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen använder en timerutlösare för att skicka ett kömeddelande var 15:e sekund.

Här är bindningsdata i *filen function.json:*

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

Följande exempel visar hur du skriver ut till en Service Bus-kö i Python.

En Service Bus-bindningsdefinition definieras i *function.json* där *typen* är inställd på `serviceBus`.

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

I `set` init *\_.py kan du skriva ut ett meddelande till kön genom att skicka ett värde till metoden. _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en Java-funktion som `myqueue` skickar ett meddelande till en servicebusskö när den utlöses av en HTTP-begäran.

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

 I [Java-funktionernas körningsbibliotek](/java/api/overview/azure/functions/runtime)använder du anteckningen `@QueueOutput` om funktionsparametrar vars värde skulle skrivas till en servicebusskö.  Parametertypen ska `OutputBinding<T>`vara , där T är en inbyggd Java-typ av en POJO.

Java-funktioner kan också skriva till ett servicebussämne. I följande exempel `@ServiceBusTopicOutput` används anteckningen för att beskriva konfigurationen för utdatabindningen. 

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

I [klassbibliotek för C#](functions-dotnet-class-library.md)använder du [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Attributets konstruktor tar namnet på kön eller ämnet och prenumerationen. Du kan också ange anslutningens åtkomsträttigheter. Hur du väljer åtkomsträttigheter-inställningen förklaras i [avsnittet Utdata - konfiguration.](#configuration) Här är ett exempel som visar attributet som tillämpas på funktionens returvärde:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Du kan `Connection` ange egenskapen för att ange namnet på en appinställning som innehåller den servicebussanslutningssträng som ska användas, vilket visas i följande exempel:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Ett fullständigt exempel finns i [Utdata - exempel](#example).

Du kan `ServiceBusAccount` använda attributet för att ange det Service Bus-konto som ska användas på klass-, metod- eller parameternivå.  Mer information finns i [Trigger - attribut](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueOutput` Anteckningarna `ServiceBusTopicOutput` och är tillgängliga för att skriva ett meddelande som en funktionsutdata. Parametern som dekoreras med dessa anteckningar `OutputBinding<T>` `T` måste deklareras som en var är den typ som motsvarar meddelandets typ.

---

## <a name="configuration"></a>Konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `ServiceBus` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
|**Typ** | Saknas | Måste ställas in på "serviceBus". Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen.|
|**riktning** | Saknas | Måste ställas in på "ut". Den här egenskapen ställs in automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kö- eller ämnesmeddelandet i funktionskoden. Ställ in på "$return" för att referera till funktionens returvärde. |
|**queueName (queueName)**|**QueueName (QueueName)**|Namnet på kön.  Ange endast om du skickar kömeddelanden, inte för ett ämne.
|**topicName (ämnesnamn)**|**TopicName (Ämnesnamn)**|Namnet på ämnet. Ange endast om du skickar ämnesmeddelanden, inte för en kö.|
|**Anslutning**|**Anslutning**|Namnet på en appinställning som innehåller anslutningssträngen servicebuss som ska användas för den här bindningen. Om appinställningsnamnet börjar med "AzureWebJobs" kan du bara ange resten av namnet. Om du till `connection` exempel anger "MyServiceBus" söker körtiden Funktioner efter en appinställning med namnet "AzureWebJobsMyServiceBus". Om du `connection` lämnar tom använder funktionskörningen standardkopplingssträngen för Service Bus i appinställningen med namnet "AzureWebJobsServiceBus".<br><br>Om du vill hämta en anslutningssträng följer du stegen som visas på [Hämta hanteringsautentiseringsuppgifter .](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string) Anslutningssträngen måste vara för ett servicebussnamnområde, inte begränsat till en viss kö eller ett visst ämne.|
|**accessRights**|**Åtkomst**|Åtkomsträttigheter för anslutningssträngen. Tillgängliga värden `manage` `listen`är och . Standard är `manage`, vilket anger `connection` att behörigheten Hantera har behörigheten **Hantera.** Om du använder en anslutningssträng **Manage** som inte `accessRights` har behörigheten Hantera anger du att "lyssna". Annars kan körningen Funktioner misslyckas med att försöka utföra åtgärder som kräver hanteringsrättigheter. I Azure Functions version 2.x och senare är den här egenskapen inte tillgänglig eftersom den senaste versionen av Service Bus SDK inte stöder hantera åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

I Azure Functions 1.x skapar körningen kön om den inte finns `accessRights` `manage`och du har angett . I Functions version 2.x och högre måste kön eller ämnet redan finnas. Om du anger en kö eller ett ämne som inte finns misslyckas funktionen. 

# <a name="c"></a>[C#](#tab/csharp)

Använd följande parametertyper för utdatabindningen:

* `out T paramName` - `T`kan vara vilken JSON-serialiserbar typ som helst. Om parametervärdet är null när funktionen avslutas skapas meddelandet med ett null-objekt.
* `out string`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande i Functions.
* `out byte[]`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande i Functions.
* `out BrokeredMessage`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande (för Funktioner 1.x)
* `out Message`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande (för Funktioner 2.x och högre)
* `ICollector<T>`eller `IAsyncCollector<T>` - För att skapa flera meddelanden. Ett meddelande skapas när `Add` du anropar metoden.

När du arbetar med C#-funktioner:

* Async-funktioner behöver ett `IAsyncCollector` returvärde `out` eller i stället för en parameter.

* Om du vill komma åt [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessions-ID:et binder du till en typ och använder egenskapen. `sessionId`

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Använd följande parametertyper för utdatabindningen:

* `out T paramName` - `T`kan vara vilken JSON-serialiserbar typ som helst. Om parametervärdet är null när funktionen avslutas skapas meddelandet med ett null-objekt.
* `out string`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande i Functions.
* `out byte[]`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande i Functions.
* `out BrokeredMessage`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande (för Funktioner 1.x)
* `out Message`- Om parametervärdet är null när funktionen avslutas skapas inget meddelande (för Funktioner 2.x och högre)
* `ICollector<T>`eller `IAsyncCollector<T>` - För att skapa flera meddelanden. Ett meddelande skapas när `Add` du anropar metoden.

När du arbetar med C#-funktioner:

* Async-funktioner behöver ett `IAsyncCollector` returvärde `out` eller i stället för en parameter.

* Om du vill komma åt [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessions-ID:et binder du till en typ och använder egenskapen. `sessionId`

# <a name="javascript"></a>[Javascript](#tab/javascript)

Öppna kön eller ämnet `context.bindings.<name from function.json>`med hjälp av . Du kan tilldela en sträng, en bytematris eller ett JavaScript-objekt `context.binding.<name>`(deserialiserat till JSON) till .

# <a name="python"></a>[Python](#tab/python)

Använd [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) i stället för den inbyggda utdatabindningen.

# <a name="java"></a>[Java](#tab/java)

Använd [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) i stället för den inbyggda utdatabindningen.

---

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Service Bus | [Felkoder för servicebuss](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Begränsningar för servicebuss](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>värd.json-inställningar

I det här avsnittet beskrivs de globala konfigurationsinställningar som är tillgängliga för den här bindningen i version 2.x och senare. Exempelvärden.json-filen nedan innehåller endast inställningarna för den här bindningen. Mer information om globala konfigurationsinställningar finns i [host.json-referens för Azure Functions-versionen](functions-host-json.md).

> [!NOTE]
> En referens för host.json i Functions 1.x finns i [host.json-referens för Azure Functions 1.x](functions-host-json-v1.md).

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
|maxAutoRenewDuration|00:05:00|Den maximala varaktighet inom vilken meddelandelåset förnyas automatiskt.|
|Komplettera automatiskt|true|Om utlösaren omedelbart ska markera meddelandet som fullständigt (komplettera automatiskt) eller vänta tills funktionen avslutas.|
|maxConcurrentCalls|16|Det maximala antalet samtidiga anrop till motringningen som meddelandepumpen ska initiera. Som standard bearbetar funktionskörningen flera meddelanden samtidigt. Om du vill att körningen ska bearbeta endast ett `maxConcurrentCalls` enskilt kö- eller ämnesmeddelande åt gången anger du till 1. |

## <a name="next-steps"></a>Nästa steg

- [Kör en funktion när en servicebusskö eller ett ämnesmeddelande skapas (utlösare)](./functions-bindings-service-bus-trigger.md)
