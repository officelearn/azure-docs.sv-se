---
title: Migrera till v3-enhet med maskin inlärd
titleSuffix: Azure Cognitive Services
description: V3-redigeringen tillhandahåller en ny entitetstyp, den dator som har lärts, tillsammans med möjligheten att lägga till relationer till den enhet som har registrerats av enheten och andra entiteter eller funktioner i programmet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: diberry
ms.openlocfilehash: b5dbcd9033d9a41e43ea907d043e0c0486b236db
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563838"
---
# <a name="migrate-to-v3-authoring-entity"></a>Migrera till v3-redigering av entitet

V3-redigeringen tillhandahåller en ny entitetstyp, den dator som har lärts, tillsammans med möjligheten att lägga till relationer till den enhet som har registrerats av enheten och andra entiteter eller funktioner i programmet.

## <a name="entities-are-decomposable-in-v3"></a>Entiteter är sammanställnings bara i v3

Entiteter som skapats med v3-redigering av API: er, antingen med hjälp av [API: erna](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview) eller med för [hands versionen av portalen](https://preview.luis.ai/), kan du skapa en modell för skiktad enhet med en över-och underordnade. Den överordnade enheten är känd som den **enhet** som sparats av datorn och de underordnade är kända som **del komponenter** i enheten som har lärts in.

Varje del komponent är också en enhet som har lärts in, men med de tillagda konfigurations alternativen för begränsningar och beskrivningar.

* **Begränsningar** är exakt text matchnings regler som garanterar att en entitet extraheras när den matchar en regel. Regeln definieras av en exakt text matchnings enhet, för närvarande: en [fördefinierad entitet](luis-reference-prebuilt-entities.md), en [entitet för reguljära uttryck](reference-entity-regular-expression.md)eller en [lista med entiteter](reference-entity-list.md).
* **Beskrivningar** är [funktioner](luis-concept-feature.md), till exempel fras listor eller entiteter, som används för att bekräfta entiteten.

V3-redigeringen tillhandahåller en ny entitetstyp, den dator som har lärts, tillsammans med möjligheten att lägga till relationer till den enhet som har registrerats av enheten och andra entiteter eller funktioner i programmet.

## <a name="how-do-these-new-relationships-compare-to-v2-authoring"></a>Hur kan de här nya relationerna jämföra med v2-redigering

V2-redigering tillhandahåller hierarkiska och sammansatta entiteter tillsammans med roller och funktioner för att utföra samma uppgift. Eftersom entiteter, funktioner och roller inte uttryckligen är relaterade till varandra, var det svårt att förstå hur LUIS underförstådda relationen under förutsägelse.

Med v3 är relationen explicit och utformad av appens författare. På så sätt kan du, som appens författare, till:

* Visuellt se hur LUIS förutsäger dessa relationer i exempel yttranden
* Testa för dessa relationer antingen med det [interaktiva test fönstret](luis-interactive-test.md) eller på slut punkten
* Använd de här relationerna i klient programmet, via ett välstrukturerat, namngivet, kapslat [JSON-objekt](reference-entity-machine-learned-entity.md)

## <a name="planning"></a>Planering

När du migrerar bör du tänka på följande i migrerings planen:

* Säkerhetskopiera LUIS-appen och utför migreringen på en separat app. Med en v2-och v3-app tillgänglig samtidigt kan du verifiera de ändringar som krävs och konsekvenserna av förutsägelse resultatet.
* Avbilda aktuella mått för förutsägelse av förutsägelse
* Avbilda aktuell instrument panels information som en ögonblicks bild av appens status
* Granska befintliga avsikter, entiteter, fras listor, mönster och batch-tester
* Följande element kan migreras **utan ändring**:
    * Avsikter
    * Entiteter
        * Entitet för reguljära uttryck
        * Lista entitet
    * Funktioner
        * Fras lista
* Följande element måste migreras **med ändringar**:
    * Entiteter
        * Hierarkisk entitet
        * Sammansatt entitet
    * Roller – roller kan bara tillämpas på en enhet som har lärts (överordnad). Det går inte att använda roller i del komponenter
    * Batch-tester och mönster som använder hierarkiska och sammansatta entiteter

När du utformar din migrering, lämna tid för att granska de slutliga enhets identifierade entiteterna efter att alla hierarkiska och sammansatta entiteter har migrerats. När en rak migrering fungerar, efter att du har gjort ändringen och granskat batch-testresultat och förutsägelse-JSON, kan det mer enhetliga JSON-nätverket leda till att du gör ändringar så att den slutliga informationen som skickas till klient sidan är ordnad annorlunda. Detta liknar kod omstrukturering och bör behandlas med samma gransknings process som organisationen har på plats.

Om du inte har batch-test för din v2-modell och migrerar batch-test till v3-modellen som en del av migreringen, kommer du inte att kunna verifiera hur migreringen påverkar resultatet av slut punkten.

## <a name="migrating-from-v2-entities"></a>Migrera från v2 entiteter

När du börjar flytta till v3-redigerings modellen bör du fundera över hur du flyttar till den enhet som har lärts och dess del komponenter, inklusive begränsningar och beskrivningar.

I följande tabell noterar du vilka entiteter som behöver migrera från en v2 till en v3-enhets design.

|V2 redigerings enhets typ|V3-redigering av enhets typ|Exempel|
|--|--|--|
|Sammansatt entitet|Enheten har lärts|[Lära sig mer](#migrate-v2-composite-entity)|
|Hierarkisk entitet|Enhets roll som har registrerats av enheten|[Lära sig mer](#migrate-v2-hierarchical-entity)|

## <a name="migrate-v2-composite-entity"></a>Migrera den sammansatta v2-entiteten

Varje underordnad till v2-sammansatt ska representeras av en del komponenten i den v3-enhet som har registrerats av enheten. Om den sammansatta underordnade är ett fördefinierat reguljärt uttryck eller en List-entitet, ska detta användas som en **begränsning** på del komponenten som representerar den underordnade.

Att tänka på när du planerar att migrera en sammansatt entitet till en enhet som har lärts ur enheten:
* Det går inte att använda underordnade entiteter i mönster
* Underordnade entiteter delas inte längre
* Underordnade entiteter måste märkas om de inte har använts av datorn

### <a name="existing-descriptors"></a>Befintliga beskrivningar

Alla fras listor som används för att öka ord i den sammansatta entiteten ska användas som en beskrivning för antingen den enhet som har lärts (överordnad), under komponenten (underordnad) eller avsikten (om fras listan bara gäller för ett avsikts objekt). Planera för att lägga till beskrivningen till entiteten som den ska förbättra markant. Lägg inte till beskrivningen allmänt till den dator som är inlärd (överordnad), om den oftast ökar förutsägelsen för en del komponent (underordnad).

### <a name="new-descriptors"></a>Nya beskrivningar

I v3-redigering lägger du till ett planerings steg för att utvärdera entiteter som möjliga beskrivningar för alla entiteter och avsikter.

### <a name="example-entity"></a>Exempel entitet

Den här entiteten är endast ett exempel. Din egen enhets migrering kan kräva andra överväganden.

Överväg en v2-sammansatt för att ändra en pizza-`order` som använder:
* fördefinierad datetimeV2 för leverans tid
* fras lista för att öka vissa ord, till exempel pizza, cirkel, crust och topping
* lista entiteter för att identifiera toppings, till exempel svamp, oliver, pepperoni.

Ett exempel på en uttryck för den här entiteten är:

`Change the toppings on my pie to mushrooms and delivery it 30 minutes later`

Följande tabell visar migreringen:

|V2-modeller|V3-modeller|
|--|--|
|Överordnad komponent enhet med namnet `Order`|Överordnad enhet som har belärts med namnet `Order`|
|Underordnade-fördefinierade datetimeV2|* Migrera en fördefinierad entitet till en ny app.<br>* Lägg till begränsning för överordnad datetimeV2.|
|Entitet med underordnad lista för toppings|* Migrera List entiteten till en ny app.<br>* Lägg sedan till en begränsning i den överordnade entiteten för List-entiteten.|


## <a name="migrate-v2-hierarchical-entity"></a>Migrera v2-hierarkisk entitet

I v2-redigering tillhandahölls en hierarkisk entitet innan befintliga roller i LUIS. Båda har samma syfte att extrahera entiteter baserat på Sammanhangs användning. Om du har hierarkiska entiteter kan du tänka på dem som enkla entiteter med roller.

I v3-redigering:
* En roll kan tillämpas på den dator som har lärts (överordnad) entiteten.
* Det går inte att använda en roll för några under komponenter.

Den här entiteten är endast ett exempel. Din egen enhets migrering kan kräva andra överväganden.

Överväg en hierarkisk entitet i v2 för att ändra en pizza `order`:
* var varje underordnad bestämmer antingen en ursprunglig topping eller den slutliga topping

Ett exempel på en uttryck för den här entiteten är:

`Change the topping from mushrooms to olives`

Följande tabell visar migreringen:

|V2-modeller|V3-modeller|
|--|--|
|Överordnad komponent enhet med namnet `Order`|Överordnad enhet som har belärts med namnet `Order`|
|Underordnad-hierarkisk entitet med ursprunglig och slutgiltig pizza-topping|* Lägg till roll i `Order` för varje topping.|

## <a name="next-steps"></a>Nästa steg

* [Resurser för utvecklare](developer-reference-resource.md)
