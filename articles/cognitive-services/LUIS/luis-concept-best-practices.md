---
title: Metodtips för att skapa appar med LUIS - Språkförståelse
titleSuffix: Azure Cognitive Services
description: Läs metodtipsen LUIS för att få bästa resultat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 511e6c732613cc577644365e38b271135659f2d3
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042252"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Metodtips för att skapa en app med language understanding med Cognitive Services
Använd appredigeringsprocessen för att skapa LUIS-appen. 

* Skapa språkmodell
* Lägg till några få utbildning exempel yttranden (10 – 15 per avsikt)
* Publicera 
* Testa från slutpunkten 
* Lägga till funktioner

När din app är [publicerade](luis-how-to-publish-app.md), använda rutinen redigering av Lägg till funktioner, publicera och testa från slutpunkten. Börja inte nästa redigering cykel genom att lägga till fler exempel yttranden. Som tillåter inte LUIS Läs din modell med riktiga användare yttranden. 

För LUIS för att effektivt på dess jobbet i utbildning du inte expandera talade tills den aktuella uppsättningen både exempel och slutpunkt yttranden returnerar trygg, hög förutsägelse poäng. Förbättra resultat med aktiv inlärning [mönster](luis-concept-patterns.md), och [fras listor](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Gör och inte
I följande lista innehåller metodtips för LUIS appar:

|Gör följande|Gör inte följande|
|--|--|
|[Definiera olika avsikter](#do-define-distinct-intents) |[Lägg till många exempel yttranden till avsikter](#dont-add-many-example-utterances-to-intents) |
|[Hitta en plats för söta mellan för Allmänt och för specifika för varje avsikt](#do-find-sweet-spot-for-intents)|[Använd LUIS som en plattform för utbildning](#dont-use-luis-as-a-training-platform)|
|[Skapa din app upprepade gånger](#do-build-the-app-iteratively)|[Lägg till många exempel yttranden i samma format som ignorerar andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägg till frasen listor och mönster i senare iterationer](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Blanda definitionen av avsikter och entiteter](#dont-mix-the-definition-of-intents-and-entities)|
|[Lägg till exempel yttranden NONE avsikt](#do-add-example-utterances-to-none-intent)|[Skapa frasen listor med alla möjliga värden](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Utnyttja funktionen Föreslå för aktiv inlärning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägg till så många mönster](#dont-add-many-patterns)|
|[Övervaka prestanda för din app](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enskild exempel-uttryck som har lagts till](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definiera olika avsikter
Kontrollera att ordförråd för varje avsikten är bara för detta syfte och inte överlappande med en annan typ av avsikt. Till exempel om du vill ha en app som hanterar resa placeringar, till exempel flygbolag flera flygsträckor och övernattningar kan du har dessa som separata avsikter eller samma avsikten med entiteter för specifika data i uttryck.

Om ordförråd mellan två avsikter är samma, kombinera avsikten och Använd entiteter. 

Överväg följande exempel talade:

```
Book a flight
Book a hotel
```

”Boka en flygning” och ”boka ett hotell” använder samma vokabulär ”boken en”. Detta överlappande bör det vara samma avsikten med andra ord. flygning och hotell extraherade entiteter. 

## <a name="do-find-sweet-spot-for-intents"></a>Hitta söta plats för avsikter
Använd förutsägelsedata från LUIS för att avgöra om dina avsikter överlappar. Överlappande avsikter confuses LUIS. Resultatet är att upp bedömning avsikt ligger för nära ett annat syfte. Eftersom LUIS inte använder exakt samma sökväg-informationen för att träna varje gång har ett intent som överlappande chansen som första eller andra i utbildning. Vill du den uttryck poängen för varje avsikt att vara längre ifrån varandra, så att det inte inträffar. Bra skillnad för avsikter resulterar i förväntade främsta syftet varje gång. 
 
## <a name="do-build-the-app-iteratively"></a>Skapar då appen upprepade gånger
Håll en separat *hemlig* testa som inte används som [exempel yttranden](luis-concept-utterance.md) eller slutpunkten yttranden. Förbättras ständigt app för din test. Anpassa testet måste återspegla hur användarna yttranden. Använda hemlig testet ange att utvärdera varje iteration. 

Utvecklare bör ha tre uppsättningar av data. Först är exempel talade för att bygga modellen. Andra är för att testa modellen vid slutpunkten. Tredje är blinda testdata som används i [batch testning](luis-how-to-batch-test.md). Den här senaste uppsättningen är inte används i utbildning för programmet eller skickats på slutpunkten.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Lägg till frasen listor och mönster i senare iterationer
[Fras listor](luis-concept-feature.md) kan du definiera ordlistor av ord som rör din appdomän. Startvärdesklassen din frasen lista med några få ord och Använd sedan funktionen Föreslå så LUIS vet om flera ord i ordlistan. Lägg inte till alla ord vokabulär eftersom den frasen inte är en exakt matchning. 

Hur användarna yttranden från slutpunkten, liknar varandra, kan avslöja mönster för val av word och placering. Den [mönstret](luis-concept-patterns.md) funktionen tar den här word valmöjligheter och placering tillsammans med reguljära uttryck för att förbättra prognosens noggrannhet. Ett reguljärt uttryck i mönstret kan ord och skiljetecken som du vill ignorera när fortfarande matchar mönstret. 

Använd mönstret valfria syntax för skiljetecken så skiljetecken kan ignoreras.

Använd inte dessa metoder innan din app har tagit emot endpoint begäranden eftersom som snedställer förtroende.  

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempel yttranden NONE avsikt
Det här är reserven avsikt, anges allt utanför ditt program. Lägg till en exempel-uttryck på Ingen avsiktshantering för varje 10 exempel yttranden i resten av LUIS-appen.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Utnyttja funktionen Föreslå för aktiv inlärning
Använd [aktiv inlärning](luis-how-to-review-endoint-utt.md)'s **granska endpoint yttranden** regelbundet, i stället för att lägga till fler exempel yttranden avsikter. Eftersom appen ständigt tar emot endpoint yttranden, den här listan växer och ändra.

## <a name="do-monitor-the-performance-of-your-app"></a>Att övervaka prestanda för din app
Övervaka förutsägelsefunktionen med en test-uppsättning. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempel yttranden till avsikter
När appen har publicerats kan du bara lägga till yttranden från aktiv inlärning i iterativ process. Om yttranden liknar för, lägger du till ett mönster. 

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte LUIS som en plattform för utbildning
LUIS är specifik för en språkmodell domän. Den är inte avsedd att fungera som en allmän utbildning plattform. 

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

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [planera din app](luis-how-plan-your-app.md) i LUIS-appen.