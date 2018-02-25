---
title: "Azure Service Bus-bindningar för Azure Functions"
description: "Förstå hur du använder Azure Service Bus-utlösare och bindningar i Azure Functions."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, funktioner, händelsebearbetning, dynamiska beräkning serverlösa arkitektur"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: tdykstra
ms.openlocfilehash: 472d61debff016cfd3df79bae1f63e176c14849d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-bindningar för Azure Functions

Den här artikeln förklarar hur du arbetar med Azure Service Bus-bindningar i Azure Functions. Azure Functions stöder utlösa och utgående bindningar för Service Bus-köer och ämnen.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>Utlösare

Använda Service Bus-utlösaren ska svara på meddelanden från en Service Bus-kö eller ett ämne. 

## <a name="trigger---example"></a>Utlösaren - exempel

Finns i det språkspecifika:

* [C#](#trigger---c-example)
* [C#-skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Utlösaren - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som loggar meddelandet Service Bus-kö.

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

Det här exemplet är för Azure Functions version 1.x; för 2.x [utelämna parametern åtkomst rättigheter](#trigger---configuration).
 
### <a name="trigger---c-script-example"></a>Utlösaren - exempel på C#-skript

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen loggar meddelandet Service Bus-kö.

Här är de bindande data den *function.json* fil:

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

Här är skriptkod C#:

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---f-example"></a>Utlösaren - F #-exempel

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [F # funktionen](functions-reference-fsharp.md) som använder bindningen. Funktionen loggar meddelandet Service Bus-kö. 

Här är de bindande data den *function.json* fil:

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

Här är skriptkod F #:

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Utlösaren - JavaScript-exempel

I följande exempel visas en Service Bus-utlösare bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen loggar meddelandet Service Bus-kö. 

Här är de bindande data den *function.json* fil:

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

Här är JavaScript-skriptkod:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

## <a name="trigger---attributes"></a>Utlösaren - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), Använd följande attribut för att konfigurera en Service Bus-utlösare:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs), som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus)

  Attributets konstruktorn har namnet på kön eller ämnet och prenumerationen. I Azure Functions version 1.x, du kan också ange åtkomstbehörigheter för den anslutningen. Om du inte anger åtkomsträttigheter standardvärdet är `Manage`. Mer information finns i [utlösaren - konfiguration](#trigger---configuration) avsnitt.

  Här är ett exempel som visar det attribut som används med en strängparameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  Du kan ange den `Connection` att ange det Service Bus-kontot som ska användas, som visas i följande exempel:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  En komplett exempel finns [utlösaren - C#-exempel](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs), som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus)

  Ger ett annat sätt att ange Service Bus-konto som ska användas. Konstruktorn får samma namn som en appinställning som innehåller en Service Bus-anslutningssträng. Attributet kan användas i parametern, metoden eller klassnivå. I följande exempel visas klassnivå och metoden:

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
* Den `ServiceBusAccount` attribut som används i klassen.
* Inställningen ”AzureWebJobsServiceBus” app.

## <a name="trigger---configuration"></a>Utlösaren - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `ServiceBusTrigger` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|Typ | Saknas | Måste anges till ”serviceBusTrigger”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**Riktning** | Saknas | Måste anges till ”i”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön eller avsnittet meddelandet i funktionskoden. Ange till ”$return” att referera till returvärde för funktion. | 
|**queueName**|**Könamn**|Namnet på den kö som ska övervakas.  Ange endast om övervakning av en kö, inte för ett ämne.
|topicName|**topicName**|Namnet på avsnittet om du vill övervaka. Ange endast om övervakning av ett ämne, inte för en kö.|
|**subscriptionName**|**SubscriptionName**|Namnet på prenumerationen du övervakar. Ange endast om övervakning av ett ämne, inte för en kö.|
|**Anslutning**|**Anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträng för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Till exempel om du ställer in `connection` för ”MyServiceBus” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyServiceBus”. Om du lämnar `connection` tom Functions-runtime använder standard Service Bus-anslutningssträng i appinställningen som heter ”AzureWebJobsServiceBus”.<br><br>Om du vill hämta en anslutningssträng att följa anvisningarna på [hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Anslutningssträngen måste vara för Service Bus-namnrymd inte begränsat till en särskild kö eller ett ämne. |
|**accessRights**|**Åtkomst**|Behörighet som krävs för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** , behörighetsgrupp `accessRights` ”lyssna”. Annars kan hantera funktionerna runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2.x kan den här egenskapen är inte tillgänglig eftersom den senaste versionen av Storage SDK: N inte stöder hantera åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Utlösaren - användning

I C# och C#-skript kan använda du följande parametertyper för kö eller ett ämne meddelandet:

* `string` – Om meddelandet är text.
* `byte[]` -Användbart för binära data.
* En anpassad typ - om meddelandet innehåller JSON, Azure Functions görs ett försök att deserialisera JSON-data.
* `BrokeredMessage` – Ger dig avserialiserat meddelandet med den [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) metod.

Få åtkomst till kö eller ett ämne meddelandet i JavaScript, med hjälp av `context.bindings.<name from function.json>`. Service Bus-meddelande skickas till funktionen som en sträng eller en JSON-objekt.

## <a name="trigger---poison-messages"></a>Utlösaren - förgiftade meddelanden

Hantering av skadligt meddelande kan inte kontrolleras eller konfigurerats i Azure Functions. Service Bus hanterar förgiftade meddelanden sig själv.

## <a name="trigger---peeklock-behavior"></a>Utlösaren - PeekLock beteende

Functions-runtime tar emot ett meddelande i [PeekLock läge](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Anropar `Complete` på meddelandet om funktionen slutförs eller samtal `Abandon` om misslyckas åtgärden. Om funktionen körs längre än den `PeekLock` timeout låset förnyas automatiskt.

## <a name="trigger---hostjson-properties"></a>Utlösaren - host.json egenskaper

Den [host.json](functions-host-json.md#servicebus) filen innehåller inställningar som styr beteendet för Service Bus-utlösare.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Resultat

Använd Azure Service Bus-utdatabindning att skicka meddelanden i kö eller ett ämne.

## <a name="output---example"></a>Output - exempel

Finns i det språkspecifika:

* [C#](#output---c-example)
* [C#-skript (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Utdata - C#-exempel

Följande exempel visar en [C#-funktionen](functions-dotnet-class-library.md) som skickar ett meddelande för Service Bus-kö:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Utdata - exempel på C#-skript

I följande exempel visas en Service Bus bindning i en *function.json* fil och en [C#-skriptfunktion](functions-reference-csharp.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett meddelande i kön var 15: e sekund.

Här är de bindande data den *function.json* fil:

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

Här är C# skriptkod som skapar ett enda meddelande:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

Här följer C# skriptkod som skapar flera meddelanden:

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Utdata - F #-exempel

I följande exempel visas en Service Bus bindning i en *function.json* fil och en [F # skriptfunktion](functions-reference-fsharp.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett meddelande i kön var 15: e sekund.

Här är de bindande data den *function.json* fil:

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

Här är F # skriptkod som skapar ett enda meddelande:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Utdata - JavaScript-exempel

I följande exempel visas en Service Bus bindning i en *function.json* fil och en [JavaScript-funktionen](functions-reference-node.md) som använder bindningen. Funktionen använder en timer som utlösare för att skicka ett meddelande i kön var 15: e sekund.

Här är de bindande data den *function.json* fil:

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
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

Här är JavaScript-skriptkod som skapar flera meddelanden:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Utdata - attribut

I [C#-klassbibliotek](functions-dotnet-class-library.md), använda den [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), som har definierats i NuGet-paketet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus).

Attributets konstruktorn har namnet på kön eller ämnet och prenumerationen. Du kan också ange åtkomstbehörigheter för den anslutningen. Så här väljer du de åtkomstbehörigheter som inställningen förklaras i den [utdata - configuration](#output---configuration) avsnittet. Här är ett exempel som visar de attribut som används i det returnera värdet för funktionen:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

Du kan ange den `Connection` att ange det Service Bus-kontot som ska användas, som visas i följande exempel:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

En komplett exempel finns [utdata - C#-exempel](#output---c-example).

Du kan använda den `ServiceBusAccount` -attribut som anger Service Bus-konto som ska användas på klass, metoden eller parametern-nivå.  Mer information finns i [utlösaren - attribut](#trigger---attributes).

## <a name="output---configuration"></a>Output - konfiguration

I följande tabell beskrivs konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `ServiceBus` attribut.

|Egenskapen Function.JSON | Egenskap |Beskrivning|
|---------|---------|----------------------|
|Typ | Saknas | Måste anges till ”serviceBus”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen.|
|**Riktning** | Saknas | Måste anges till ”out”. Den här egenskapen anges automatiskt när du skapar utlösaren i Azure-portalen. |
|**Namn** | Saknas | Namnet på variabeln som representerar kön eller -avsnittet i funktionskoden. Ange till ”$return” att referera till returvärde för funktion. | 
|**queueName**|**Könamn**|Namnet på kön.  Ange endast om att skicka meddelanden till kön, inte för ett ämne.
|topicName|**topicName**|Namnet på avsnittet om du vill övervaka. Ange endast om avsnittet meddelanden, inte för en kö.|
|**subscriptionName**|**SubscriptionName**|Namnet på prenumerationen du övervakar. Ange endast om avsnittet meddelanden, inte för en kö.|
|**Anslutning**|**Anslutning**|Namnet på en appinställning som innehåller Service Bus-anslutningssträng för den här bindningen. Om appen Inställningens namn börjar med ”AzureWebJobs” kan ange du endast resten av namnet. Till exempel om du ställer in `connection` för ”MyServiceBus” Functions-runtime ut för en app inställningen som heter ”AzureWebJobsMyServiceBus”. Om du lämnar `connection` tom Functions-runtime använder standard Service Bus-anslutningssträng i appinställningen som heter ”AzureWebJobsServiceBus”.<br><br>Om du vill hämta en anslutningssträng att följa anvisningarna på [hämta autentiseringsuppgifter för hantering](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials). Anslutningssträngen måste vara för Service Bus-namnrymd inte begränsat till en särskild kö eller ett ämne.|
|**accessRights**|**Åtkomst**|Behörighet som krävs för anslutningssträngen. Tillgängliga värden är `manage` och `listen`. Standardvärdet är `manage`, vilket indikerar att den `connection` har den **hantera** behörighet. Om du använder en anslutningssträng som inte har den **hantera** , behörighetsgrupp `accessRights` ”lyssna”. Annars kan hantera funktionerna runtime misslyckas försöker att utföra åtgärder som kräver rättigheter. I Azure Functions version 2.x kan den här egenskapen är inte tillgänglig eftersom den senaste versionen av Storage SDK: N inte stöder hantera åtgärder.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Utdata - användning

I Azure Functions 1.x, körningsmiljön skapar kön om det inte finns och att du har angett `accessRights` till `manage`. I funktion version måste 2.x, kö eller avsnittet redan finnas; funktionen kommer att misslyckas om du anger en kö eller ett ämne som inte finns. 

I C# och C#-skript kan använda du följande parametertyper för bindningen utdata:

* `out T paramName` - `T` kan vara JSON-serialiserbara typer. Om parametervärdet är null när funktionen avslutas skapar funktioner meddelandet med ett null-objekt.
* `out string` -Om parametervärdet är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `out byte[]` -Om parametervärdet är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `out BrokeredMessage` -Om parametervärdet är null när funktionen avslutas funktioner inte att skapa ett meddelande.
* `ICollector<T>` eller `IAsyncCollector<T>` – för att skapa flera meddelanden. Skapa ett meddelande när du anropar den `Add` metoden.

I async-funktion, använder du det returnera värdet eller `IAsyncCollector` i stället för en `out` parameter.

I JavaScript, få åtkomst till den kö eller ett ämne med hjälp av `context.bindings.<name from function.json>`. Du kan tilldela en sträng, en bytematris eller ett Javascript-objekt (deserialisera till JSON) `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Undantag och returkoder

| Bindning | Referens |
|---|---|
| Service Bus | [Felkoder för Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus-gränser](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
