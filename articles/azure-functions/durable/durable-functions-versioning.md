---
title: Versions hantering i Durable Functions – Azure
description: Lär dig hur du implementerar versioner i Durable Functions-tillägget för Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: ef64a43cbed7f033a938351506b7f78142ff044c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097624"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versions hantering i Durable Functions (Azure Functions)

Det är oundvikligt att funktioner kommer att läggas till, tas bort och ändras under ett programs livs längd. [Durable Functions](durable-functions-overview.md) tillåter länkning på ett sätt som inte tidigare var möjligt, och den här länkningen påverkar hur du kan hantera versions hantering.

## <a name="how-to-handle-breaking-changes"></a>Så här hanterar du avbryter ändringar

Det finns flera exempel på hur du kan bryta ändringar för att vara medveten om. Den här artikeln beskriver de vanligaste. Huvud temat bakom alla är att både nya och befintliga funktions dirigeringar påverkas av ändringar i funktions koden.

### <a name="changing-activity-function-signatures"></a>Ändra signaturer för aktivitets funktion

En signatur ändras till en ändring i namn, indata eller utdata för en funktion. Om den här typen av ändring görs i en aktivitets funktion kan den bryta Orchestrator-funktionen som är beroende av den. Om du uppdaterar Orchestrator-funktionen för att hantera den här ändringen kan du bryta befintliga instansen av flyg.

Anta till exempel att vi har följande funktion.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Den här förenklad-funktionen tar resultatet av **foo** och skickar det till **bar**. Vi antar att vi behöver ändra returvärdet för **foo** från `bool` till `int` som stöd för en större mängd resultat värden. Resultatet ser ut så här:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Den här ändringen fungerar bra för alla nya instanser av Orchestrator-funktionen men alla eventuella instansen bryts. Anta till exempel att en Dirigerings instans anropar **foo**, hämtar ett booleskt värde och sedan kontroll punkter. Om ändring av signaturen har distribuerats i det här läget Miss kan den inloggade instansen omedelbart när den återupptas och spelar upp `context.CallActivityAsync<int>("Foo")`anropet till. Detta beror på att resultatet i historik tabellen är `bool` men den nya koden försöker deserialisera den till. `int`

Det här är bara en av många olika sätt att ändra en signatur kan bryta befintliga instanser. I allmänhet är det troligt att om en Orchestrator behöver ändra hur den anropar en funktion, är ändringen sannolikt problematisk.

### <a name="changing-orchestrator-logic"></a>Ändra Orchestrator-logik

Den andra klassen av versions problem kommer att ändra koden för Orchestrator-funktionen på ett sätt som innebär att omuppspelnings logiken för instansen i flyget aktive.

Överväg följande Orchestrator-funktion:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nu ska vi anta att du vill göra en synes Innocent ändring för att lägga till ett annat funktions anrop.

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

Den här ändringen lägger till ett nytt funktions anrop till **SendNotification** mellan **foo** och **bar**. Det finns inga ändringar i signaturen. Problemet uppstår när en befintlig instans återupptas från anropet till **bar**. Om det ursprungliga anropet till **foo** returnerades `true`under uppspelningen, kommer Orchestrator-uppspelningen att anropas i **SendNotification** som inte finns i dess körnings historik. Därför Miss lyckas det varaktiga aktivitets ramverket med en `NonDeterministicOrchestrationException` eftersom det påträffade ett anrop till **SendNotification** när det förväntades Visa ett anrop till **bar**.

## <a name="mitigation-strategies"></a>Strategier för minskning

Här följer några av strategierna för att hantera problem med versions hantering:

* Gör ingenting
* Stoppa alla instansen av flyg
* Distributioner sida vid sida

### <a name="do-nothing"></a>Gör ingenting

Det enklaste sättet att hantera en brytande ändring är att tillåta att instansen av flygbaserade Orchestration-instanser inte fungerar. Nya instanser har kört den ändrade koden.

Om det här är ett problem beror det på din flyg instanss betydelse. Om du arbetar i aktiv utveckling och inte bryr dig om instansen av flyg kan detta vara tillräckligt tillräckligt. Men du kommer att behöva hantera undantag och fel i din diagnostiska pipeline. Om du vill undvika dessa saker bör du överväga de andra versions alternativen.

### <a name="stop-all-in-flight-instances"></a>Stoppa alla instansen av flyg

Ett annat alternativ är att stoppa alla instansen av flyg. Detta kan göras genom att rensa innehållet i köerna för interna **kontroller** , köer och **WorkItem-** köer. Instanserna har alltid fastnat där de är, men de kommer inte att röra sig om Telemetrin med felaktiga meddelanden. Detta är idealiskt i snabb prototyp utveckling.

> [!WARNING]
> Informationen om dessa köer kan ändras med tiden, så du behöver inte använda den här metoden för produktions arbets belastningar.

### <a name="side-by-side-deployments"></a>Distributioner sida vid sida

Det vanligaste sättet att se till att de hårda ändringarna distribueras på ett säkert sätt är att distribuera dem sida vid sida med dina äldre versioner. Detta kan göras med någon av följande metoder:

* Distribuera alla uppdateringar som helt nya funktioner (nya namn).
* Distribuera alla uppdateringar som en ny function-app med ett annat lagrings konto.
* Distribuera en ny kopia av Function-appen men med ett uppdaterat `TaskHub` namn. Detta är den rekommenderade metoden.

### <a name="how-to-change-task-hub-name"></a>Ändra namn på aktivitets hubb

Aktivitets navet kan konfigureras i *Host. JSON* -filen på följande sätt:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

Standardvärdet är `DurableFunctionsHub`.

Alla Azure Storage entiteter namnges baserat på `HubName` konfiguration svärdet. Genom att ge huvudhubben ett nytt namn ser du till att separata köer och historik tabell skapas för den nya versionen av programmet.

Vi rekommenderar att du distribuerar den nya versionen av Function-appen till en ny [distributions plats](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Med distributions platser kan du köra flera kopior av funktions programmet sida vid sida med bara en av dem som aktiv *produktions* plats. När du är redo att exponera den nya organisations logiken i din befintliga infrastruktur kan det vara så enkelt som att byta ut den nya versionen till produktions platsen.

> [!NOTE]
> Den här strategin fungerar bäst när du använder HTTP-och webhook-utlösare för Orchestrator functions. För icke-HTTP-utlösare, till exempel köer eller Event Hubs, ska utlösnings definitionen [härledas från en app-inställning](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) som uppdateras som en del av växlings åtgärden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar problem med prestanda och skalning](durable-functions-perf-and-scale.md)
