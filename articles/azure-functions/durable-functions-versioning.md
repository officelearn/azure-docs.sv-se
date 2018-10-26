---
title: Versionshantering i varaktiga funktioner – Azure
description: Lär dig mer om att implementera versionshantering i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 9cd8e21ede794fcb46683ea7cedd2bf9ed833204
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086989"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versionshantering i varaktiga funktioner (Azure Functions)

Det är ofrånkomligt att funktioner läggs till, tas bort och ändras under livslängden för ett program. [Varaktiga funktioner](durable-functions-overview.md) tillåter länkning fungerar tillsammans på ett sätt som inte var möjligt tidigare och länkning av den här påverkar hur du kan hantera versionshantering.

## <a name="how-to-handle-breaking-changes"></a>Hur du hanterar icke-bakåtkompatibla ändringar

Det finns flera exempel på större ändringar känna till. Den här artikeln beskriver vanligaste. Sammanfatta bakom alla är att både nya och befintliga funktionen orkestreringar som påverkas av ändringar i funktionskoden.

### <a name="changing-activity-function-signatures"></a>Ändra aktivitet funktionssignaturer

En Signaturändring av refererar till en ändring i namn, indata eller utdata för en funktion. Om den här typen av ändring görs i en aktivitet funktion, kan det innebära att orchestrator-funktion som är beroende av den. Om du uppdaterar orchestrator-funktion för att hantera den här ändringen kan du dela befintliga pågående instanser.

Anta att vi har följande funktion som ett exempel.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Den här förenklad funktionen använder resultatet av **Foo** och skickar det till **fältet**. Anta att vi behöver ändra returvärdet för **Foo** från `bool` till `int` för en mängd olika resultatvärden. Resultatet ser ut så här:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Den här ändringen fungerar bra för alla nya instanser av orchestrator-funktion, men delar alla pågående instanser. Anta exempelvis att fall där en orchestration-instans anropar **Foo**, får tillbaka ett booleskt värde och sedan kontrollpunkter. Om signaturen ändringen har distribuerats i det här läget, med kontrollpunkt instansen misslyckas omedelbart när den återupptas och spelar upp anropet till `context.CallActivityAsync<int>("Foo")`. Detta beror resultatet i historiktabellen är `bool` men den nya koden försöker deserialisera det till `int`.

Det här är bara en av många olika sätt att en Signaturändring av kan bryta befintliga instanser. I allmänhet om en initierare måste ändra hur den anropar en funktion och sedan ändringen troligen kommer att vara problematiskt.

### <a name="changing-orchestrator-logic"></a>Ändra orchestrator-logik

Andra klassen för versionshantering problem kommer från att ändra koden för orchestrator-funktion på ett sätt som confuses repetitionsattacker logiken för pågående instanser.

Överväg följande orchestrator-funktion:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nu antar vi att du vill ändra verka ofarliga försöker att lägga till en annan funktionsanrop.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Den här ändringen lägger till ett nytt funktionsanrop till **SendNotification** mellan **Foo** och **fältet**. Det finns ingen signatur-ändringar. Problemet uppstår när en befintlig instans återställs vid anrop till **fältet**. Under repetitionsattacker, om ursprungligt anrop till **Foo** returnerade `true`, och sedan anropar orchestrator-repetitionsattacker **SendNotification** som inte är i dess körningshistorik. Därför ramen varaktiga uppgift misslyckas med ett `NonDeterministicOrchestrationException` eftersom den påträffade ett anrop till **SendNotification** när det förväntas finns i ett anrop till **fältet**.

## <a name="mitigation-strategies"></a>Begränsningsstrategier

Här följer några strategier för att hantera versionshantering utmaningar:

* Gör ingenting
* Stoppa alla pågående instanser
* Distributioner sida vid sida

### <a name="do-nothing"></a>Gör ingenting

Det enklaste sättet att hantera en stor förändring är att låta pågående orchestration instanser misslyckas. Nya instanser som körts ändrade koden.

Om detta är ett problem beror på vikten av att dina pågående instanser. Om du är i aktivt med utveckling och inte bryr dig om pågående instanser, kan det vara tillräckligt bra. Men måste du hantera undantag och fel i din pipeline för diagnostik. Om du vill undvika dessa saker att tänka på de andra alternativen för versionshantering.

### <a name="stop-all-in-flight-instances"></a>Stoppa alla pågående instanser

Ett annat alternativ är att stoppa alla pågående instanser. Detta kan göras genom att radera innehållet i den interna **kontroll kö** och **arbetsobjekt kö** köer. Instanserna ska ha fastnat alltid där de är, men de kommer inte att störa din telemetri med felmeddelanden. Det är perfekt i prototyp för snabb utveckling.

> [!WARNING]
> Information om dessa köer kan ändras med tiden, så att du inte använda den här tekniken för produktionsarbetsbelastningar.

### <a name="side-by-side-deployments"></a>Distributioner sida vid sida

Det är det mest misslyckas bevis sättet att säkerställa att de senaste ändringarna distribueras på ett säkert sätt genom att distribuera dem sida vid sida med dina äldre versioner. Detta kan göras med hjälp av någon av följande metoder:

* Distribuera alla uppdateringar som helt nya funktioner (nytt namn).
* Distribuera alla uppdateringar som en ny funktionsapp med ett annat lagringskonto.
* Distribuera en ny kopia av funktionsappen men med en uppdaterad `TaskHub` namn. Det här är den rekommenderade tekniken.

### <a name="how-to-change-task-hub-name"></a>Så här ändrar du hub aktivitetsnamn

Uppgiften hubben kan konfigureras i den *host.json* så här:

```json
{
    "extensions": {
        "durableTask": {
            "HubName": "MyTaskHubV2"
        }
    }
}
```

> [!NOTE]
> För V1-funktion, *host.json* ska konfigureras så här i stället
>```json
>{
>    "durableTask": {
>        "HubName": "MyTaskHubV2"
>    }
>}
>```

Standardvärdet är `DurableFunctionsHub`.

Alla Azure Storage-entiteter är namngivna baserat på den `HubName` Konfigurationsvärdet. Genom att ge aktiviteten hubben ett nytt namn, du se till att separat köer och historiktabellen skapas för den nya versionen av ditt program.

Vi rekommenderar att du distribuerar den nya versionen av funktionsappen till en ny [Deployment Slot](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Distributionsplatser kan du köra flera kopior av dina funktionen app sida-vid-sida med endast en av dem som en aktiv *produktion* fack. När du är redo att exponera den nya orchestration-logiken till din befintliga infrastruktur, kan det vara så enkla som växlar den nya versionen till produktionsplatsen.

> [!NOTE]
> Den här strategin fungerar bäst när du använder HTTP och webhook-utlösare för orchestrator-funktioner. För icke-HTTP-utlösare, t.ex köer eller Event Hubs, måste en utlösardefinition härledas från en appinställning uppdateras som en del av växlingen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att hantera prestanda och skala problem](durable-functions-perf-and-scale.md)
