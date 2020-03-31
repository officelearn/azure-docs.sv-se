---
title: Funktionstyper i Azure Durable Functions
description: Lär dig mer om de typer av funktioner och roller som stöder funktionskommunikation i en varaktig funktionsorkestrering i Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277887"
---
# <a name="durable-functions-types-and-features"></a>Typer och funktioner för varaktiga funktioner

Varaktiga funktioner är ett tillägg av [Azure Functions](../functions-overview.md). Du kan använda varaktiga funktioner för tillståndskänslig orkestrering av funktionskörning. En hållbar funktionsapp är en lösning som består av olika Azure-funktioner. Funktioner kan spela olika roller i en varaktig funktion orkestrering. 

Det finns för närvarande fyra varaktiga funktionstyper i Azure Functions: aktivitet, orchestrator, entitet och klient. Resten av det här avsnittet går in mer på detaljer om vilka typer av funktioner som ingår i en orkestrering.

## <a name="orchestrator-functions"></a>Orchestrator-funktioner

Orchestrator-funktioner beskriver hur åtgärder utförs och i vilken ordning åtgärder utförs. Orchestrator-funktioner beskriver orkestreringen i kod (C# eller JavaScript) som visas i [programmönster för varaktiga funktioner](durable-functions-overview.md#application-patterns). En orkestrering kan ha många olika typer av åtgärder, inklusive [aktivitetsfunktioner,](#activity-functions) [underorkestreringar](durable-functions-orchestrations.md#sub-orchestrations), [väntar på externa händelser,](durable-functions-orchestrations.md#external-events) [HTTP](durable-functions-http-features.md)och [timers](durable-functions-orchestrations.md#durable-timers). Orchestrator-funktioner kan också interagera med [entitetsfunktioner](#entity-functions).

> [!NOTE]
> Orchestrator-funktioner skrivs med vanlig kod, men det finns strikta krav på hur du skriver koden. Orchestrator-funktionskoden måste vara *deterministisk*. Om du inte följer dessa determinismkrav kan orchestrator-funktioner misslyckas med att köras korrekt. Detaljerad information om dessa krav och hur du kan kringgå dem finns i [kodbegränsningsavsnittet.](durable-functions-code-constraints.md)

Mer detaljerad information om orchestrator-funktioner och deras funktioner finns i artikeln [Varaktiga orkestreringar.](durable-functions-orchestrations.md)

## <a name="activity-functions"></a>Aktivitetsfunktioner

Aktivitetsfunktioner är den grundläggande arbetsenheten i en varaktig funktionsorkestrering. Aktivitetsfunktioner är de funktioner och uppgifter som är iscensatta i processen. Du kan till exempel skapa en orchestrator-funktion för att bearbeta en order. Uppgifterna innebär att kontrollera lagret, debitera kunden och skapa en leverans. Varje uppgift skulle vara en separat aktivitetsfunktion. Dessa aktivitetsfunktioner kan utföras seriellt, parallellt, eller någon kombination av båda.

Till skillnad från orchestrator-funktioner är aktivitetsfunktioner inte begränsade i den typ av arbete du kan utföra i dem. Aktivitetsfunktioner används ofta för att ringa nätverksåtgärder eller köra CPU-intensiva åtgärder. En aktivitetsfunktion kan också returnera data tillbaka till orchestrator-funktionen. Den varaktiga uppgiftsramen garanterar att varje anropad aktivitetsfunktion körs *minst en gång* under en orkestrerings körning.

> [!NOTE]
> Eftersom aktivitetsfunktioner bara garanterar *minst en gång* körning rekommenderar vi att du gör din aktivitetsfunktionslogik *idempotent* när det är möjligt.

Använd en [aktivitetsutlösare](durable-functions-bindings.md#activity-trigger) för att definiera en aktivitetsfunktion. .NET-funktioner `DurableActivityContext` får en som en parameter. Du kan också binda utlösaren till alla andra JSON-serialiseras objekt för att passera i indata till funktionen. I JavaScript kan du komma `<activity trigger binding name>` åt en indata via egenskapen [ `context.bindings` ](../functions-reference-node.md#bindings)på objektet . Aktivitetsfunktioner kan bara ha ett enda värde skickat till dem. Om du vill skicka flera värden måste du använda tupplar, matriser eller komplexa typer.

> [!NOTE]
> Du kan bara utlösa en aktivitetsfunktion från en orchestrator-funktion.

## <a name="entity-functions"></a>Funktioner för entitet

Entitetsfunktioner definierar åtgärder för läsning och uppdatering av små delar av tillståndet. Vi hänvisar ofta till dessa tillståndskänsliga enheter som *varaktiga enheter*. Liksom orchestrator-funktioner är entitetsfunktioner funktioner med en speciell utlösartyp, *entitetsutlösare*. De kan också anropas från klientfunktioner eller från orchestrator-funktioner. Till skillnad från orchestrator-funktioner har entitetsfunktioner inga specifika kodbegränsningar. Entitetsfunktioner hanterar också tillstånd explicit i stället för att implicit representera tillstånd via kontrollflöde.

> [!NOTE]
> Entitetsfunktioner och relaterade funktioner är endast tillgängliga i Varaktiga funktioner 2.0 och högre.

Mer information om entitetsfunktioner finns i artikeln [Varaktiga entiteter.](durable-functions-entities.md)

## <a name="client-functions"></a>Klientfunktioner

Orchestrator-funktioner utlöses av en [orchestration-utlösarebindning](durable-functions-bindings.md#orchestration-trigger) och entitetsfunktioner utlöses av en [entitetsutlösarebindning](durable-functions-bindings.md#entity-trigger). Båda dessa utlösare fungerar genom att reagera på meddelanden som är incheckade i en [aktivitetsnav](durable-functions-task-hubs.md). Det primära sättet att leverera dessa meddelanden är att använda en [orchestrator-klientbindning](durable-functions-bindings.md#orchestration-client) eller en [entitetsklientbindning](durable-functions-bindings.md#entity-client) inifrån en *klientfunktion*. Alla icke-orchestrator-funktioner kan vara en *klientfunktion*. Du kan till exempel utlösa orchestrator från en HTTP-utlöst funktion, en Azure Event Hub-utlöst funktion osv. Det som gör en funktion till en *klientfunktion* är dess användning av den varaktiga klientutdatabindningen.

> [!NOTE]
> Till skillnad från andra funktionstyper kan orchestrator- och entitetsfunktioner inte utlösas direkt med knapparna i Azure Portal. Om du vill testa en orchestrator eller entitetsfunktion i Azure Portal måste du i stället köra en *klientfunktion* som startar en orchestrator eller entitetsfunktion som en del av implementeringen. För den enklaste testupplevelsen rekommenderas en *manuell utlösarfunktion.*

Förutom att utlösa orchestrator eller entitetsfunktioner kan den *varaktiga* klientbindningen användas för att interagera med löpande orkestreringar och entiteter. Orkestreringar kan till exempel efterfrågas, avslutas och kan ha händelser som har aktiverats för dem. Mer information om hur du hanterar orkestreringar och entiteter finns i artikel [om instanshantering.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Nästa steg

För att komma igång, skapa din första hållbara funktion i [C#](durable-functions-create-first-csharp.md) eller [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Läs mer om varaktiga funktioner orkestreringar](durable-functions-orchestrations.md)