---
title: Migrera till V3 maskininlärd entitet
titleSuffix: Azure Cognitive Services
description: V3-redigeringen ger en ny entitetstyp, den maskininlärda entiteten, tillsammans med möjligheten att lägga till relationer till den maskininlärda entiteten och andra entiteter eller funktioner i programmet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75563838"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrera till V3-redigeringsenhet

V3-redigeringen ger en ny entitetstyp, den maskininlärda entiteten, tillsammans med möjligheten att lägga till relationer till den maskininlärda entiteten och andra entiteter eller funktioner i programmet.

## <a name="entities-are-decomposable-in-v3"></a>Entiteter kan delas ut i V3

Entiteter som skapats med V3-redigerings-API:erna, antingen med hjälp av [API:erna](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) eller med [förhandsgranskningsportalen,](https://preview.luis.ai/)kan du skapa en modell i lager med en överordnad och underordnad. Den överordnade är känd som den **maskininlärda entiteten** och de underordnade kallas **delkomponenter till** den inlärda datorn.

Varje delkomponent är också en maskininlärd entitet men med de tillagda konfigurationsalternativen för begränsningar och beskrivningar.

* **Begränsningar** är exakta textmatchningsregler som garanterar att en entitet extraheras när den matchar en regel. Regeln definieras av en exakt textmatchningsentitet, för närvarande: en [fördefinierad entitet](luis-reference-prebuilt-entities.md), en [enhet för reguljära uttryck](reference-entity-regular-expression.md)eller [entiteten](reference-entity-list.md).
* **Deskriptorer** är [funktioner](luis-concept-feature.md), till exempel fraslistor eller entiteter, som används för att starkt ange entiteten.

V3-redigeringen ger en ny entitetstyp, den maskininlärda entiteten, tillsammans med möjligheten att lägga till relationer till den maskininlärda entiteten och andra entiteter eller funktioner i programmet.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hur dessa nya relationer jämföra med V2 författande

V2-redigering gav hierarkiska och sammansatta entiteter tillsammans med roller och funktioner för att utföra samma uppgift. Eftersom entiteter, funktioner och roller inte uttryckligen var relaterade till varandra, var det svårt att förstå hur LUIS antydde relationerna under förutsägelse.

Med V3 är relationen explicit och designad av appförfattaren. På så sätt kan du som appförfattare:

* Visuellt se hur LUIS förutsäger dessa relationer, i exemplet yttranden
* Testa för dessa relationer antingen med den [interaktiva testfönstret](luis-interactive-test.md) eller vid slutpunkten
* Använd dessa relationer i klientprogrammet, via ett välstrukturerat, namngivet kapslat [.json-objekt](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planering

När du migrerar bör du tänka på följande i migreringsplanen:

* Säkerhetskopiera LUIS-appen och utför migreringen i en separat app. Med en V2 och V3 app tillgänglig samtidigt kan du validera de ändringar som krävs och effekten på förutsägelseresultat.
* Fånga aktuella förutsägelseframgångsmått
* Samla in aktuell instrumentpanelsinformation som en ögonblicksbild av appstatus
* Granska befintliga avsikter, entiteter, fraslistor, mönster och batchtester
* Följande element kan migreras **utan ändringar:**
    * Avsikter
    * Entiteter
        * Entitet för reguljära uttryck
        * Lista entitet
    * Funktioner
        * Fraslista
* Följande element måste migreras **med ändringar:**
    * Entiteter
        * Hierarkisk entitet
        * Sammansatt entitet
    * Roller - roller kan endast tillämpas på en datorinlärd (överordnad) entitet. Roller kan inte tillämpas på delkomponenter
    * Batchtester och mönster som använder hierarkiska och sammansatta entiteter

När du utformar migreringsplanen lämnar du tid att granska de slutliga datorinlärda entiteterna, efter att alla hierarkiska och sammansatta entiteter har migrerats. Medan en rak migrering fungerar, när du har gjort ändringen och granskar batchtestresultaten och förutsägelsen JSON, kan den mer enhetliga JSON leda till att du gör ändringar så att den slutliga informationen som levereras till klientappen är ordnad på olika sätt. Detta liknar kodåterfactoring och bör behandlas med samma granskningsprocess som organisationen har på plats.

Om du inte har batchtester på plats för din V2-modell och migrerar batchtesterna till V3-modellen som en del av migreringen, kan du inte validera hur migreringen påverkar slutpunktsförutsägelseresultaten.

## <a name="migrating-from-v2-entities"></a>Migrera från V2-enheter

När du börjar flytta till V3-redigeringsmodellen bör du överväga hur du flyttar till den maskininlärda entiteten och dess delkomponenter, inklusive begränsningar och beskrivningar.

Följande tabell noterar vilka entiteter som behöver migrera från en V2 till en V3-entitetsdesign.

|V2-redigeringsenhetstyp|V3-redigeringsenhetstyp|Exempel|
|--|--|--|
|Sammansatt entitet|Maskininlärd enhet|[Lära sig mer](#migrate-v2-composite-entity)|
|Hierarkisk entitet|Maskininlärd entitetsroll|[Lära sig mer](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrera V2-sammansatt entitet

Varje underordnad v2-komposit ska representeras med en delkomponent i V3-maskininlärda entiteten. Om det sammansatta underordnade är ett fördefinierat, reguljärt uttryck eller en listentitet, bör detta tillämpas som ett **villkor** för den delkomponent som representerar den underordnade.

Överväganden när du planerar att migrera en sammansatt entitet till en maskininlärd entitet:
* Underordnade entiteter kan inte användas i mönster
* Underordnade entiteter delas inte längre
* Underordnade enheter måste märkas om de tidigare inte var maskininlärda

### <a name="existing-descriptors"></a>Befintliga deskriptorer

Alla frasar lista som används för att öka ord i den sammansatta entiteten ska användas som en deskriptor på antingen den maskininlärda (överordnade) entiteten, den underkomponent (underordnade) entiteten eller avsikten (om fraslistan endast gäller för en avsikt). Planera att lägga till beskrivningen i entiteten som den bör öka mest avsevärt. Lägg inte till deskriptorn allmänt i den maskininlärda (överordnade) entiteten, om det mest påtagligt ökar förutsägelsen av en underkomponent (underordnad).

### <a name="new-descriptors"></a>Nya deskriptorer

I V3-redigering lägger du till ett planeringssteg för att utvärdera entiteter som möjliga beskrivningar för alla entiteter och avsikter.

### <a name="example-entity"></a>Exempel entitet

Den här entiteten är endast ett exempel. Din egen entitetsmigrering kan kräva andra överväganden.

Överväg en V2-komposit `order` för att ändra en pizza som använder:
* fördefinierad datetimeV2 för leveranstid
* fraslista för att öka vissa ord som pizza, paj, skorpa och toppning
* lista enhet för att upptäcka pålägg såsom svamp, oliver, pepperoni.

Ett exempel på uttryck för den här entiteten är:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Följande tabell visar migreringen:

|V2-modeller|V3-modeller|
|--|--|
|Överordnad - Komponententitet med namnet`Order`|Överordnad - Datorinlärd entitet med namnet`Order`|
|Barn - Förbyggt datetimeV2|* Migrera fördefinierade entiteten till ny app.<br>* Lägg till begränsning på överordnad för fördefinierade datetimeV2.|
|Child - listentitet för pålägg|* Migrera listentiteten till ny app.<br>* Lägg sedan till ett villkor för den överordnade för listentiteten.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrera V2-hierarkisk entitet

I V2-redigering angavs en hierarkisk entitet före roller som finns i LUIS. Båda tjänade samma syfte att extrahera entiteter baserat på kontextanvändning. Om du har hierarkiska entiteter kan du se dem som enkla entiteter med roller.

I V3 författande:
* En roll kan användas på den maskininlärda (överordnade) entiteten.
* Det går inte att använda en roll på några underkomponenter.

Den här entiteten är endast ett exempel. Din egen entitetsmigrering kan kräva andra överväganden.

Överväg en V2 hierarkisk `order`entitet för att ändra en pizza:
* där varje barn bestämmer antingen en ursprunglig toppning eller den slutliga toppning

Ett exempel på uttryck för den här entiteten är:

`Change the topping from mushrooms to olives`

Följande tabell visar migreringen:

|V2-modeller|V3-modeller|
|--|--|
|Överordnad - Komponententitet med namnet`Order`|Överordnad - Datorinlärd entitet med namnet`Order`|
|Barn - Hierarkisk enhet med original och slutlig pizza topping|* Lägg `Order` till roll till för varje toppning.|

## <a name="next-steps"></a>Nästa steg

* [Resurser för utvecklare](developer-reference-resource.md)
