---
title: Azure Functions uppvärmnings-utlösare
description: Lär dig hur du använder uppvärmnings-utlösaren i Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: Azure Functions, functions, Event Processing, uppvärmnings, kall start, Premium, dynamisk beräkning, Server lös arkitektur
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: a947ff11fbbe418af84ff49033cba3857bb8447f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925185"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions värme utlösare

Den här artikeln förklarar hur du arbetar med uppvärmnings-utlösaren i Azure Functions. Uppvärmnings-utlösaren stöds bara för Function-appar som körs i en [Premium-plan](functions-premium-plan.md). En uppvärmnings-utlösare anropas när en instans läggs till för att skala en app som körs i funktion. Du kan använda en uppvärmnings-utlösare för att i förväg läsa in anpassade beroenden under för [uppvärmnings processen](./functions-premium-plan.md#pre-warmed-instances) , så att funktionerna kan börja bearbeta begär Anden direkt. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket-funktioner 2. x och högre

[Microsoft. Azure. WebJobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet, version **3.0.5 eller högre** krävs. Käll koden för paketet finns i [Azure-WebJobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) lagringsplatsen. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Utlösare

Med uppvärmnings-utlösaren kan du definiera en funktion som ska köras på en instans när den läggs till i appen som körs. Du kan använda en uppvärmnings-funktion för att öppna anslutningar, läsa in beroenden eller köra någon annan anpassad logik innan appen tar emot trafik. 

Uppvärmnings-utlösaren är avsedd att skapa delade beroenden som kommer att användas av andra funktioner i din app. [Se exempel på delade beroenden här](./manage-connections.md#client-code-examples).

Observera att uppvärmnings-utlösaren endast anropas under skalnings åtgärder, inte under omstarter eller andra icke-skalnings starter. Du måste se till att din logik kan läsa in alla nödvändiga beroenden utan att använda uppvärmnings-utlösaren. Enkel inläsning är ett utmärkt mönster för att uppnå detta.

## <a name="trigger---example"></a>Utlösare - exempel

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

I följande exempel visas en [ C# funktion](functions-dotnet-class-library.md) som körs på varje ny instans när den läggs till i din app. Ett attribut för retur värde är inte obligatoriskt.


* Din funktion måste ha namnet ```warmup``` (inte Skift läges känsligt) och det kan bara finnas en uppvärmnings-funktion per app.
* Om du vill använda uppvärmnings som en .NET-klass biblioteks funktion kontrollerar du att du har en paket referens till **Microsoft. Azure. WebJobs. extensions > = 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Plats hållare kommentarer visar var i programmet att deklarera och initiera delade beroenden. 
[Lär dig mer om delade beroenden här](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)


I följande exempel visas en uppvärmnings-utlösare i en *Function. JSON* -fil och en [ C# skript funktion](functions-reference-csharp.md) som ska köras på varje ny instans när den läggs till i din app.

Din funktion måste ha namnet ```warmup``` (inte Skift läges känsligt) och det kan bara finnas en uppvärmnings-funktion per app.

Här är den *function.json* fil:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är C# skript koden som binder till `HttpRequest`:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en uppvärmnings-utlösare i en *Function. JSON* -fil och en [JavaScript-funktion](functions-reference-node.md) som ska köras på varje ny instans när den läggs till i din app.

Din funktion måste ha namnet ```warmup``` (inte Skift läges känsligt) och det kan bara finnas en uppvärmnings-funktion per app.

Här är den *function.json* fil:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är JavaScript-kod:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

I följande exempel visas en uppvärmnings-utlösare i en *Function. JSON* -fil och en [python-funktion](functions-reference-python.md) som ska köras på varje ny instans när den läggs till i din app.

Din funktion måste ha namnet ```warmup``` (inte Skift läges känsligt) och det kan bara finnas en uppvärmnings-funktion per app.

Här är den *function.json* fil:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Den [configuration](#trigger---configuration) förklaras de här egenskaperna.

Här är python-koden:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="javatabjava"></a>[Java](#tab/java)

I följande exempel visas en uppvärmnings-utlösare i en *Function. JSON* -fil och en [Java-funktion](functions-reference-java.md) som ska köras på varje ny instans när den läggs till i din app.

Din funktion måste ha namnet ```warmup``` (inte Skift läges känsligt) och det kan bara finnas en uppvärmnings-funktion per app.

Här är den *function.json* fil:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

Här är den Java-kod:

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Utlösare - attribut

I [ C# klass bibliotek](functions-dotnet-class-library.md)är `WarmupTrigger` attributet tillgängligt för att konfigurera funktionen.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Det här exemplet visar hur du använder attributet [uppvärmnings](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) .

Observera att funktionen måste anropas ```Warmup``` och det får bara finnas en uppvärmnings-funktion per app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Ett fullständigt exempel finns i [utlösaren exempel](#trigger---example).

# <a name="c-scripttabcsharp-script"></a>[C#Över](#tab/csharp-script)

Attribut stöds inte av C# skript.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="pythontabpython"></a>[Python](#tab/python)

Attribut stöds inte av python.

# <a name="javatabjava"></a>[Java](#tab/java)

Uppvärmnings-utlösaren stöds inte i Java som ett attribut.

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell förklaras konfigurationsegenskaper för bindning som du anger i den *function.json* fil och `WarmupTrigger` attribut.

|Function.JSON egenskap | Attributegenskapen |Beskrivning|
|---------|---------|----------------------|
| **typ** | Ej tillämpligt| Required-måste anges till `warmupTrigger`. |
| **riktning** | Ej tillämpligt| Required-måste anges till `in`. |
| **Namn** | Ej tillämpligt| Obligatoriskt – variabel namnet som används i funktions koden.|

## <a name="trigger---usage"></a>Utlösare - användning

Ingen ytterligare information ges till en uppvärmnings-utlöst funktion när den anropas.

## <a name="trigger---limits"></a>Utlösare – gränser

* Uppvärmnings-utlösaren är endast tillgänglig för appar som körs i [Premium-planen](./functions-premium-plan.md).
* Uppvärmnings-utlösaren anropas bara under skalnings åtgärder, inte vid omstarter eller andra icke-skalbara starter. Du måste se till att din logik kan läsa in alla nödvändiga beroenden utan att använda uppvärmnings-utlösaren. Enkel inläsning är ett utmärkt mönster för att uppnå detta.
* Uppvärmnings-utlösaren kan inte anropas när en instans redan körs.
* Det får bara finnas en funktion för uppvärmnings-utlösare per Function-app.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure functions-utlösare och bindningar](functions-triggers-bindings.md)
