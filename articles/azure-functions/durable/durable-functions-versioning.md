---
title: Versionshantering i varaktiga funktioner - Azure
description: Lär dig hur du implementerar versionshantering i tillägget Varaktiga funktioner för Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232760"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Versionshantering i varaktiga funktioner (Azure-funktioner)

Det är oundvikligt att funktioner kommer att läggas till, tas bort och ändras under ett programs livstid. [Varaktiga funktioner](durable-functions-overview.md) möjliggör kedjefunktioner tillsammans på ett sätt som inte tidigare var möjligt, och den här chainringen påverkar hur du kan hantera versionshantering.

## <a name="how-to-handle-breaking-changes"></a>Så här hanterar du brottsändringar

Det finns flera exempel på att bryta förändringar att vara medveten om. I den här artikeln beskrivs de vanligaste. Huvudtemat bakom dem alla är att både nya och befintliga funktionsorkestreringar påverkas av ändringar i funktionskoden.

### <a name="changing-activity-or-entity-function-signatures"></a>Ändra aktivitets- eller entitetsfunktionssignaturer

En signaturändring refererar till en ändring av namn, indata eller utdata för en funktion. Om den här typen av ändring görs i en aktivitets- eller entitetsfunktion kan den bryta en orchestrator-funktion som är beroende av den. Om du uppdaterar orchestrator-funktionen för att hantera den här ändringen kan du bryta befintliga instanser under flygning.

Anta att vi har följande orchestrator-funktion som exempel.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Denna förenklade funktion tar resultaten av **Foo** och skickar den till **Bar**. Låt oss anta att vi måste ändra avkastningsvärdet för **Foo** från `bool` till `int` att stödja ett bredare utbud av resultatvärden. Resultatet ser ut så här:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> De tidigare C#-exemplen är inriktade på varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

Den här ändringen fungerar bra för alla nya instanser av orchestrator-funktionen, men bryter alla instanser under flygning. Tänk till exempel på det fall där en `Foo`orkestreringsinstans anropar en funktion med namnet , får tillbaka ett booleskt värde och sedan kontrollpunkter. Om signaturändringen distribueras vid denna punkt misslyckas den kontrollpunkterade instansen `context.CallActivityAsync<int>("Foo")`omedelbart när den återupptas och återupptas samtalet till . Det här felet inträffar eftersom `bool` resultatet i historiktabellen är men `int`den nya koden försöker avserialisera den till .

Det här exemplet är bara ett av många olika sätt som en signaturändring kan bryta befintliga instanser. I allmänhet, om en orchestrator behöver ändra hur den anropar en funktion, då förändringen sannolikt kommer att vara problematisk.

### <a name="changing-orchestrator-logic"></a>Ändra orchestrator logik

Den andra klassen av versionshanteringsproblem kommer från att ändra orchestrator-funktionskoden på ett sätt som förvirrar reprislogiken för inflygningsinstanser.

Tänk på följande orchestrator-funktion:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Låt oss nu anta att du vill göra en till synes oskyldig förändring för att lägga till en annan funktion samtal.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> De tidigare C#-exemplen är inriktade på varaktiga funktioner 2.x. För varaktiga funktioner 1.x `DurableOrchestrationContext` måste `IDurableOrchestrationContext`du använda i stället för . Mer information om skillnaderna mellan versioner finns i artikeln [Över huvudversioner för varaktiga funktioner.](durable-functions-versions.md)

Den här ändringen lägger till ett nytt funktionsanrop **i SendNotification** mellan **Foo** och **Bar**. Det finns inga signaturändringar. Problemet uppstår när en befintlig instans återupptas från anropet till **Stapel**. Under repris, om det ursprungliga anropet till **Foo** returneras `true`, kommer orchestrator reprisen att anropa **SendNotification**, vilket inte är i dess körningshistorik. Därför misslyckas ramverket för varaktiga uppgifter med en `NonDeterministicOrchestrationException` eftersom den påträffade ett anrop till **SendNotification** när det förväntades visas ett anrop till **stapel**. Samma typ av problem kan uppstå när du lägger `CreateTimer`till `WaitForExternalEvent`några anrop till "hållbara" API:er, inklusive , , etc.

## <a name="mitigation-strategies"></a>Begränsningsstrategier

Här är några av de strategier för att hantera versionshantering utmaningar:

* Gör ingenting
* Stoppa alla instanser under flygning
* Distributioner sida vid sida

### <a name="do-nothing"></a>Gör ingenting

Det enklaste sättet att hantera en bryta förändring är att låta under flygning orkestrering instanser misslyckas. Nya instanser har kört den ändrade koden.

Om denna typ av fel är ett problem beror på vikten av dina fall under flygning. Om du är i aktiv utveckling och inte bryr dig om under flygning fall, kan detta vara tillräckligt bra. Du måste dock hantera undantag och fel i diagnostikpipelinen. Om du vill undvika dessa saker bör du överväga de andra versionsalternativen.

### <a name="stop-all-in-flight-instances"></a>Stoppa alla instanser under flygning

Ett annat alternativ är att stoppa alla fall ombord. Stoppa alla instanser kan göras genom att rensa innehållet i den interna **kontrollkön** och **arbetsytakökön.** Instanserna kommer alltid att vara fast där de är, men de kommer inte röran dina loggar med felmeddelanden. Detta tillvägagångssätt är idealiskt i snabb prototyputveckling.

> [!WARNING]
> Information om dessa köer kan ändras med tiden, så lita inte på den här tekniken för produktionsarbetsbelastningar.

### <a name="side-by-side-deployments"></a>Distributioner sida vid sida

Det mest felsäkra sättet att se till att bryta ändringar distribueras på ett säkert sätt är genom att distribuera dem sida vid sida med dina äldre versioner. Detta kan göras med någon av följande tekniker:

* Distribuera alla uppdateringar som helt nya funktioner, vilket gör att befintliga funktioner som de är. Detta kan vara knepigt eftersom de som ringer i de nya funktionsversionerna måste uppdateras och följa samma riktlinjer.
* Distribuera alla uppdateringar som en ny funktionsapp med ett annat lagringskonto.
* Distribuera en ny kopia av funktionsappen med `taskHub` samma lagringskonto men med ett uppdaterat namn. Distributioner sida vid sida är den rekommenderade tekniken.

### <a name="how-to-change-task-hub-name"></a>Så här ändrar du namn på aktivitetsnav

Aktivitetshubben kan konfigureras i *filen host.json* på följande sätt:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funktioner 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Standardvärdet för varaktiga funktioner v1.x är `DurableFunctionsHub`. Från och med varaktiga funktioner v2.0 är standardaktivitetsnavets namn `TestHubName` detsamma som funktionsappnamnet i Azure, eller om det körs utanför Azure.

Alla Azure Storage-entiteter `hubName` namnges baserat på konfigurationsvärdet. Genom att ge aktivitetshubben ett nytt namn ser du till att separata köer och historiktabeller skapas för den nya versionen av programmet. Funktionsappen stoppar dock bearbetning av händelser för orkestreringar eller entiteter som skapats under det tidigare aktivitetsnavnamnet.

Vi rekommenderar att du distribuerar den nya versionen av funktionsappen till en ny [distributionsplats](../functions-deployment-slots.md). Med distributionsplatser kan du köra flera kopior av funktionsappen sida vid sida med bara en av dem som aktiv *produktionsplats.* När du är redo att exponera den nya orchestration logiken till din befintliga infrastruktur, kan det vara så enkelt som att byta den nya versionen till produktionsplatsen.

> [!NOTE]
> Den här strategin fungerar bäst när du använder HTTP- och webhook-utlösare för orchestrator-funktioner. För icke-HTTP-utlösare, till exempel köer eller eventhubbar, bör utlösardefinitionen [härledas från en appinställning](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) som uppdateras som en del av växlingsåtgärden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar prestanda- och skalningsproblem](durable-functions-perf-and-scale.md)
