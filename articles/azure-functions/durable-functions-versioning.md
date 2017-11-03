---
title: "Versionshantering i varaktiga funktioner – Azure"
description: "Lär dig hur du implementerar versionshantering i tillägget varaktiga funktioner för Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 0a86e4a87f5ec23c284aa4e5cfb2c67622b3ebe9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versionshantering i varaktiga funktioner (Azure-funktioner)

Det är ofrånkomligt att funktioner lagts till, tas bort och ändras under livslängden för ett program. [Beständiga funktioner](durable-functions-overview.md) kan länkning fungerar tillsammans på ett sätt som inte var möjligt tidigare och den här länkning påverkar hur du kan hantera versionshantering.

## <a name="how-to-handle-breaking-changes"></a>Hur du hanterar viktiga förändringar för

Det finns flera exempel på viktiga förändringar för att vara medveten om. Den här artikeln beskrivs vanligaste. Den huvudsakliga teman bakom alla är att både nya och befintliga funktionen orkestreringarna påverkas av ändringar i funktionskoden.

### <a name="changing-activity-function-signatures"></a>Ändra aktivitet funktionssignaturer

En Signaturändring refererar till en ändring i namn, indata eller utdata för en funktion. Om den här typen av ändring görs till en aktivitet funktion, kan det innebära att funktionen orchestrator som är beroende av den. Om du uppdaterar orchestrator-funktionen för att hantera förändringen, kan du dela befintliga relä instanser.

Anta att vi har följande funktion som ett exempel.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Den här simplistic funktionen använder resultatet av **Foo** och skickar det till **fältet**. Vi antar vi behöver ändra returvärdet för **Foo** från `bool` till `int` att stödja fler resultatvärden. Resultatet ser ut så här:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Den här ändringen fungerar bra för alla nya instanser av orchestrator-funktionen men bryter alla pågående instanser. Anta exempelvis att de fall där en instans av orchestration anropar **Foo**, hämtar tillbaka ett booleskt värde och kontrollpunkter. Om signaturen ändringen distribueras nu kontrollpunkt instansen misslyckas omedelbart när den återupptas och spelar upp anropet till `context.CallActivityAsync<int>("Foo")`. Detta beror resultatet i historiktabellen är `bool` men den nya koden försöker att deserialisera den till `int`.

Detta är bara en av många olika sätt att ändra en signatur kan dela befintliga instanser. I allmänhet en orchestrator behöver ändra hur den anropar en funktion sedan ändringen kan förväntas vara problematiskt.

### <a name="changing-orchestrator-logic"></a>Ändra orchestrator-logik

Klassen versionshantering problem komma från ändra koden för orchestrator-funktion på ett sätt som confuses replay-logik för relä instanser.

Överväg följande orchestrator-funktion:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nu antar vi att du vill ändra till synes ofarliga försöker att lägga till en annan funktionsanrop.

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

Den här ändringen lägger till ett nytt funktionsanrop till **SendNotification** mellan **Foo** och **fältet**. Det finns inga ändringar i signatur. Problemet uppstår när en befintlig instans återställs vid anrop till **fältet**. Under spela upp som ursprungligt anrop till **Foo** returnerade `true`, och sedan anropar orchestrator replay **SendNotification** som inte är körningen historiken. Därför ramverket beständiga aktiviteten misslyckas med en `NonDeterministicOrchestrationException` p.g.a. ett anrop till **SendNotification** när det förväntas finns ett anrop till **fältet**.

## <a name="mitigation-strategies"></a>Strategier för lösning

Här följer några strategier för att hantera versionshantering utmaningar:

* Gör ingenting
* Stoppa alla pågående instanser
* Distributioner sida vid sida

### <a name="do-nothing"></a>Gör ingenting

Det enklaste sättet att hantera en ny ändring är att låta relä orchestration instanser misslyckas. Nya instanser körts ändrade koden.

Om detta är ett problem som beror på vikten av dina relä instanser. Om du i aktiv utveckling och inte bryr dig om relä instanser, kan det vara bra tillräckligt. Men behöver du hantera undantag och fel i pipeline diagnostik. Om du vill undvika dessa saker du andra alternativ för versionshantering.

### <a name="stop-all-in-flight-instances"></a>Stoppa alla pågående instanser

Ett annat alternativ är att stoppa alla pågående instanser. Detta kan göras genom att radera innehållet i det interna **kontroll kön** och **arbetsobjekt kön** köer. Instanserna ska ha fastnat alltid där de är, men de kommer inte skapar oreda i din telemetri med felmeddelanden. Detta är idealiskt prototyp snabb utveckling.

> [!WARNING]
> Information om dessa köer kan ändras med tiden, så att förlita dig inte på den här tekniken för produktionsarbetsbelastningar.

### <a name="side-by-side-deployments"></a>Distributioner sida vid sida

Det är det mest misslyckas bevis sättet att säkerställa att viktiga förändringar distribueras på ett säkert sätt genom att distribuera dem sida vid sida med din äldre versioner. Detta kan göras med hjälp av följande metoder:

* Distribuera alla uppdateringar som helt nya funktioner (nya namn).
* Distribuera alla uppdateringar som en ny funktionsapp med ett annat lagringskonto.
* Distribuera en ny kopia av appen funktionen men med en uppdaterad `TaskHub` namn. Det här är den rekommenderade metoden.

### <a name="how-to-change-task-hub-name"></a>Så här ändrar du hubb aktivitet

Uppgiften hubben kan konfigureras i den *host.json* enligt följande:

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

Standardvärdet är `DurableFunctionsHub`.

Alla entiteter i Azure Storage är namngivna baserat på den `HubName` konfigurationsvärde. Genom att ge ett nytt namn för aktiviteten hubben, kontrollera att separat köer och historiktabellen skapas för den nya versionen av programmet.

Vi rekommenderar att du distribuerar den nya versionen av appen funktionen till en ny [distributionsplatsen](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Distributionsplatser kan du köra flera kopior av dina funktionen app sida-vid-sida med endast en av dem som aktiv *produktion* plats. När du är redo att visa den nya orchestration logiken till din befintliga infrastruktur kan det vara så enkelt som att byta den nya versionen till produktionsplatsen.

> [!NOTE]
> Den här strategin fungerar bäst när du använder HTTP och webhook utlösare för orchestrator-funktioner. Utlösardefinition måste härledas från en appinställning uppdateras som en del av byte för icke-HTTP-utlösare, till exempel köer eller Event Hubs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att hantera prestanda och skala problem](durable-functions-perf-and-scale.md)
