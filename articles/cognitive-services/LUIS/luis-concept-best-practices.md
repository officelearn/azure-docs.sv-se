---
title: Bästa praxis
titleSuffix: Language Understanding - Azure Cognitive Services
description: Läs metodtipsen LUIS för att få bästa resultat från LUIS-app-modellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: diberry
ms.openlocfilehash: d7547ecf387e070efe788ba5f8582076388f422a
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984211"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Metodtips för att skapa en app med language understanding med Cognitive Services
Använd appredigeringsprocessen för att skapa LUIS-appen. 

* Skapa språkmodell
* Lägg till några få utbildning exempel yttranden (10 – 15 per avsikt)
* Publicera 
* Testa från slutpunkten 
* Lägga till funktioner

När din app är [publicerade](luis-how-to-publish-app.md), använda rutinen redigering av Lägg till funktioner, publicera och testa från slutpunkten. Börja inte nästa redigering cykel genom att lägga till fler exempel yttranden. Som tillåter inte LUIS Läs din modell med riktiga användare yttranden. 

För LUIS för att effektivt på dess jobbet i utbildning du inte expandera talade tills den aktuella uppsättningen både exempel och slutpunkt yttranden returnerar trygg, hög förutsägelse poäng. Förbättra resultat med hjälp av [aktiv inlärning](luis-concept-review-endpoint-utterances.md), [mönster](luis-concept-patterns.md), och [fras listor](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Gör och inte
I följande lista innehåller metodtips för LUIS appar:

|Gör följande|Gör inte följande|
|--|--|
|[Definiera olika avsikter](#do-define-distinct-intents) |[Lägg till många exempel yttranden till avsikter](#dont-add-many-example-utterances-to-intents) |
|[Hitta en plats för söta mellan för Allmänt och för specifika för varje avsikt](#do-find-sweet-spot-for-intents)|[Använd LUIS som en plattform för utbildning](#dont-use-luis-as-a-training-platform)|
|[Skapa din app upprepade gånger](#do-build-the-app-iteratively)|[Lägg till många exempel yttranden i samma format som ignorerar andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägg till frasen listor och mönster i senare iterationer](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Blanda definitionen av avsikter och entiteter](#dont-mix-the-definition-of-intents-and-entities)|
|[Belastningsutjämnas alla avsikter din yttranden](#balance-your-utterances-across-all-intents) förutom avsikt None.<br>[Lägg till exempel yttranden NONE avsikt](#do-add-example-utterances-to-none-intent)|[Skapa frasen listor med alla möjliga värden](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Utnyttja funktionen Föreslå för aktiv inlärning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägg till för många mönster](#dont-add-many-patterns)|
|[Övervaka prestanda för din app](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enskild exempel-uttryck som har lagts till](#dont-train-and-publish-with-every-single-example-utterance)|
|[Använda versioner för varje iteration av appen](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definiera olika avsikter
Kontrollera att ordförråd för varje avsikten är bara för detta syfte och inte överlappande med en annan typ av avsikt. Om du vill ha en app som hanterar reser placeringar, till exempel flygbolag flera flygsträckor och övernattningar välja du till exempel att ha dessa ämnesområden som separata avsikter eller samma avsikten med entiteter för specifika data i uttryck.

Om ordförråd mellan två avsikter är samma, kombinera avsikten och Använd entiteter. 

Överväg följande exempel talade:

|Exempel på yttranden|
|--|
|Boka en flygning|
|Boka ett hotell|

”Boka en flygning” och ”boka ett hotell” använder samma vokabulär ”boken en”. Det här formatet är samma bör det vara samma avsikten med andra ord. flygning och hotell som extraherade entiteter. 

Mer information:
* Begrepp: [Begrepp om avsikter i LUIS-appen](luis-concept-intent.md)
* Självstudier: [Skapa LUIS-app för att fastställa användarens avsikter](luis-quickstart-intents-only.md)
* Anvisningar: [Lägg till avsikter att fastställa användarens avsikt är att yttranden](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Hitta söta plats för avsikter
Använd förutsägelsedata från LUIS för att avgöra om dina avsikter överlappar. Överlappande avsikter ihop LUIS. Resultatet är att upp bedömning avsikt ligger för nära ett annat syfte. Eftersom LUIS inte använder exakt samma sökväg-informationen för att träna varje gång har ett intent som överlappande chansen som första eller andra i utbildning. Vill du den uttryck poängen för varje avsikt att vara längre ifrån varandra, så att den här andra/Vänd vertikalt inte upprättas. Bra skillnad för avsikter resulterar i förväntade främsta syftet varje gång. 
 
## <a name="do-build-the-app-iteratively"></a>Skapar då appen upprepade gånger
Håll en separat uppsättning yttranden som inte används som [exempel yttranden](luis-concept-utterance.md) eller slutpunkten yttranden. Förbättras ständigt app för din test. Anpassa testet måste återspegla hur användarna yttranden. Använd det här testet som anger att utvärdera varje iteration eller version av appen. 

Utvecklare bör ha tre uppsättningar av data. Först är exempel talade för att bygga modellen. Andra är för att testa modellen vid slutpunkten. Tredje är blinda testdata som används i [batch testning](luis-how-to-batch-test.md). Den här senaste uppsättningen inte används i utbildning för programmet och inte heller skickas på slutpunkten.  

Mer information:
* Begrepp: [Redigering för LUIS-appen](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Lägg till frasen listor och mönster i senare iterationer

Ett bra tips är att inte använda dessa metoder innan din app har testats. Du bör förstå hur appen fungerar innan du lägger till frasen listor och mönster eftersom de här funktionerna tyngre än exempel yttranden och kommer ge skeva förtroende. 

När du förstår hur din app fungerar utan dessa lägger du till de här funktionerna eftersom de gäller för din app. Du behöver inte lägga till dessa funktioner i varje [iteration](luis-concept-app-iteration.md) eller ändra funktionerna med varje version. 

Det finns inget om man lägger till dem i början av modelldesignen men det är lättare att se hur varje funktion ändras resultat när modellen har testats med yttranden. 

Ett bra tips är att testa den [endpoint](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance) så att du får den ytterligare fördelen med [aktiv inlärning](luis-concept-review-endpoint-utterances.md). Den [interaktiva testning fönstret](luis-interactive-test.md) är också en giltig test-metod. 
 

### <a name="phrase-lists"></a>Fraslistor

[Fras listor](luis-concept-feature.md) kan du definiera ordlistor av ord som rör din appdomän. Startvärdesklassen din frasen lista med några få ord och Använd sedan funktionen Föreslå så LUIS vet om flera ord i en viss terminologi till din app. En lista med frasen förbättrar avsikt identifiering och klassificering för entitet genom att öka signalen som är associerade med ord eller fraser som är relevanta för att din app. 

Lägg inte till alla ord vokabulär eftersom den frasen inte är en exakt matchning. 

Mer information:
* Begrepp: [Fras funktioner i din LUIS-app](luis-concept-feature.md)
* Så här gör du: [Använd frasen visar att boost signaler med ordlistan](luis-how-to-add-features.md)

### <a name="patterns"></a>Mönster

Hur användarna yttranden från slutpunkten, liknar varandra, kan avslöja mönster för val av word och placering. Den [mönstret](luis-concept-patterns.md) funktionen tar den här word valmöjligheter och placering tillsammans med reguljära uttryck för att förbättra prognosens noggrannhet. Ett reguljärt uttryck i mönstret kan ord och skiljetecken som du vill ignorera när fortfarande matchar mönstret. 

Användningsmönstret [valfria syntax](luis-concept-patterns.md) för skiljetecken så skiljetecken kan ignoreras. Använd den [explicit lista](luis-concept-patterns.md#explicit-lists) att kompensera för pattern.any syntax problem. 

Mer information:
* Begrepp: [Mönster förbättra prognosens noggrannhet](luis-concept-patterns.md)
* Så här gör du: [Hur du lägger till mönster för att förbättra förutsägelsefunktionen](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Belastningsutjämnas alla avsikter din yttranden

Mängden exempel yttranden i varje avsikt (förutom ingen avsikt), måste vara lika med relativt för LUIS förutsägelser vara korrekt. 

Om du har en avsikt med 100 exempel yttranden och syftet med 20 exempel yttranden har avsikten 100-uttryck en högre andel förutsägelse.  

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempel yttranden NONE avsikt

Den här är reserven avsikt, anges allt utanför ditt program. Lägg till en exempel-uttryck på Ingen avsiktshantering för varje 10 exempel yttranden i resten av LUIS-appen.

Mer information:
* Begrepp: [Förstå vad bra yttranden är avsedda för LUIS-app](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Utnyttja funktionen Föreslå för aktiv inlärning

Använd [aktiv inlärning](luis-how-to-review-endoint-utt.md)'s **granska endpoint yttranden** regelbundet, i stället för att lägga till fler exempel yttranden avsikter. Eftersom appen ständigt tar emot endpoint yttranden, den här listan växer och ändra.

Mer information:
* Begrepp: [Begrepp för att aktivera aktiv inlärning genom att granska endpoint yttranden](luis-concept-review-endpoint-utterances.md)
* Självstudier: [Självstudier: Åtgärda osäker förutsägelser genom att granska endpoint yttranden](luis-tutorial-review-endpoint-utterances.md)
* Så här gör du: [Granska endpoint yttranden LUIS-portalen](luis-how-to-review-endoint-utt.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Att övervaka prestanda för din app

Övervaka en förutsägelse Precision med hjälp av en [batch test](luis-concept-batch-test.md) ange. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempel yttranden till avsikter

När appen har publicerats kan du bara lägga till yttranden från aktiv inlärning i iterativ process. Om yttranden liknar för, lägger du till ett mönster. 

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte LUIS som en plattform för utbildning

LUIS är specifik för en språkmodell domän. Det är inte avsett att fungera som en allmän naturligt språk utbildning plattform. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Lägg inte till många exempel yttranden i samma format som ignorerar andra format

LUIS förväntar sig variationer i ett intent yttranden. Talade kan variera och ha innebörden övergripande samtidigt. Variationer kan innehålla uttryck längd, word val och word placering. 

|Använd inte samma format|Använd olika format|
|--|--|
|Köp en biljett till Seattle<br>Köp en biljett till Paris<br>Köp en biljett till Orlando|Köpa 1 biljett till Seattle<br>Reservera två platser på det röda ögat till Paris nästa måndag<br>Jag skulle vilja boka 3 biljetter till Orlando för spring break|

Den andra kolumnen använder olika verb (köp, Reservera, bok), olika kvantiteter (1, två, 3), och olika uppställningar av ord, men alla ha samma avsikt köpa flygbolag biljetter för resa. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Blanda inte definitionen av avsikter och entiteter

Skapa ett intent för alla åtgärder som tar din robot. Använd entiteter som parametrar som gör det möjligt att åtgärden. 

En chattrobot som kommer att boka flygbolag flyg, skapa en **BookFlight** avsikt. Skapa inte en avsikt för varje flygbolag eller varje mål. Använd dessa delar av data som [entiteter](luis-concept-entity-types.md) och markera dem i exempel-yttranden. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Skapa inte frasen listor med alla möjliga värden

Innehåller några exempel i den [fras listor](luis-concept-feature.md) men inte alla word. LUIS generaliserar och tar hänsyn till kontext. 

## <a name="dont-add-many-patterns"></a>Lägg inte till många mönster

Lägg inte till för många [mönster](luis-concept-patterns.md). LUIS är avsedd att lära dig snabbt med färre exempel. Systemet inte överbelasta onödan.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Inte träna och publicera med varje enskild exempel-uttryck

Lägg till 10 eller 15 yttranden innan utbildnings- och publicering. Där du kan se hur prognosens noggrannhet kan förbättras. Att lägga till en enda uttryck kan inte ha en synliga inverkan på poängen. 

## <a name="do-use-versions-for-each-app-iteration"></a>Använd versioner för varje iteration av appen

Varje redigering cykel ska vara i en ny [version](luis-concept-version.md), klonade från en befintlig version. LUIS har ingen gräns för versioner. Ett versionsnamn används som en del av API-väg så det är viktigt att välja tecken tillåts i en URL, samt att hålla inom 10 teckenantalet för en version. Utveckla en strategi för version namn om du vill behålla dina versioner ordnade. 

Mer information:
* Begrepp: [Förstå hur och när du använder en LUIS-version](luis-concept-version.md)
* Så här gör du: [Använda versioner för att redigera och testa utan att påverka mellanlagring eller produktion appar](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [planera din app](luis-how-plan-your-app.md) i LUIS-appen.
