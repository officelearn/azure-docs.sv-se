---
title: Azure Functions uppvärmningsutlösare
description: Förstå hur du använder uppvärmningsutlösaren i Azure Functions.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure funktioner, funktioner, händelsebearbetning, uppvärmning, kallstart, premium, dynamisk beräkning, serverlös arkitektur
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167317"
---
# <a name="azure-functions-warm-up-trigger"></a>Uppvärmningsutlösning för Azure Functions

I den här artikeln beskrivs hur du arbetar med uppvärmningsutlösaren i Azure Functions. Uppvärmningsutlösaren stöds endast för funktionsappar som körs i en [Premium-plan](functions-premium-plan.md). En uppvärmningsutlösare anropas när en instans läggs till för att skala en funktionsapp som körs. Du kan använda en uppvärmningsutlösare för att förinläsning av anpassade beroenden under [förvärmningsprocessen](./functions-premium-plan.md#pre-warmed-instances) så att dina funktioner är redo att börja bearbeta begäranden omedelbart. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Paket - Funktioner 2.x och högre

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-paketet, version **3.0.5 eller senare** krävs. Källkoden för paketet finns i [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub-databasen. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Utlösare

Med uppvärmningsutlösaren kan du definiera en funktion som ska köras på en ny instans när den läggs till i din app som körs. Du kan använda en uppvärmningsfunktion för att öppna anslutningar, läsa in beroenden eller köra någon annan anpassad logik innan appen börjar ta emot trafik. 

Uppvärmningsutlösaren är avsedd att skapa delade beroenden som ska användas av de andra funktionerna i appen. [Se exempel på delade beroenden här](./manage-connections.md#client-code-examples).

Observera att uppvärmningsutlösaren endast anropas under utskalningsåtgärder, inte vid omstarter eller andra icke-skalningsstarter. Du måste se till att din logik kan läsa in alla nödvändiga beroenden utan att använda uppvärmningsutlösaren. Lazy lastning är ett bra mönster för att uppnå detta.

## <a name="trigger---example"></a>Utlösare - exempel

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som körs på varje ny instans när den läggs till i appen. Ett returvärdeattribut krävs inte.


* Din funktion måste ```warmup``` namnges (skiftlägesokänslig) och det kanske bara finns en uppvärmningsfunktion per app.
* Om du vill använda uppvärmning som en .NET-klassbiblioteksfunktion kontrollerar du att du har en paketreferens till **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Platshållarkommentarer visar var i programmet som ska deklarera och initiera delade beroenden. 
[Läs mer om delade beroenden här](./manage-connections.md#client-code-examples).

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
# <a name="c-script"></a>[C# Skript](#tab/csharp-script)


I följande exempel visas en uppvärmningsutlösare i en *function.json-fil* och en [C#-skriptfunktion](functions-reference-csharp.md) som körs på varje ny instans när den läggs till i appen.

Din funktion måste ```warmup``` namnges (skiftlägesokänslig), och det kanske bara finns en uppvärmningsfunktion per app.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#trigger---configuration) förklarar dessa egenskaper.

Här är C # skriptkod `HttpRequest`som binder till:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

I följande exempel visas en uppvärmningsutlösare i en *function.json-fil* och en [JavaScript-funktion](functions-reference-node.md) som körs på varje ny instans när den läggs till i din app.

Din funktion måste ```warmup``` namnges (skiftlägesokänslig) och det kanske bara finns en uppvärmningsfunktion per app.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#trigger---configuration) förklarar dessa egenskaper.

Här är JavaScript-koden:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en uppvärmningsutlösare i en *function.json-fil* och en [Python-funktion](functions-reference-python.md) som körs på varje ny instans när den läggs till i din app.

Din funktion måste ```warmup``` namnges (skiftlägesokänslig) och det kanske bara finns en uppvärmningsfunktion per app.

Här är *filen function.json:*

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

[Konfigurationsavsnittet](#trigger---configuration) förklarar dessa egenskaper.

Här är Python-koden:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

I följande exempel visas en uppvärmningsutlösare som körs när varje ny instans läggs till i appen.

Din funktion måste `warmup` namnges (skiftlägesokänslig) och det kanske bara finns en uppvärmningsfunktion per app.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Utlösare - attribut

I [klassbibliotek för C#](functions-dotnet-class-library.md)är `WarmupTrigger` attributet tillgängligt för att konfigurera funktionen.

# <a name="c"></a>[C#](#tab/csharp)

Det här exemplet visar [warmup](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) hur du använder warmup-attributet.

Observera att din funktion ```Warmup``` måste anropas och att det bara kan finnas en uppvärmningsfunktion per app.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Ett fullständigt exempel finns i [utlösarexempeln](#trigger---example).

# <a name="c-script"></a>[C# Skript](#tab/csharp-script)

Attribut stöds inte av C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Attribut stöds inte av JavaScript.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av Python.

# <a name="java"></a>[Java](#tab/java)

Uppvärmningsutlösaren stöds inte i Java som attribut.

---

## <a name="trigger---configuration"></a>Utlösare - konfiguration

I följande tabell beskrivs de bindningskonfigurationsegenskaper som `WarmupTrigger` du anger i *filen function.json* och attributet.

|egenskapen function.json | Egenskapen Attribute |Beskrivning|
|---------|---------|----------------------|
| **Typ** | Saknas| Obligatoriskt - måste `warmupTrigger`ställas in på . |
| **riktning** | Saknas| Obligatoriskt - måste `in`ställas in på . |
| **Namn** | Saknas| Obligatoriskt - variabelnamnet som används i funktionskoden.|

## <a name="trigger---usage"></a>Utlösare - användning

Ingen ytterligare information ges till en uppvärmningsutlöst funktion när den anropas.

## <a name="trigger---limits"></a>Utlösare - gränser

* Uppvärmningsutlösaren är endast tillgänglig för appar som körs på [Premium-planen](./functions-premium-plan.md).
* Uppvärmningsutlösaren anropas endast under uppskalningsåtgärder, inte vid omstarter eller andra icke-skalningsstarter. Du måste se till att din logik kan läsa in alla nödvändiga beroenden utan att använda uppvärmningsutlösaren. Lazy lastning är ett bra mönster för att uppnå detta.
* Uppvärmningsutlösaren kan inte anropas när en instans redan körs.
* Det kan bara finnas en uppvärmningsutlösare funktion per funktionsapp.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Utlösare och bindningar för Azure-funktioner](functions-triggers-bindings.md)
