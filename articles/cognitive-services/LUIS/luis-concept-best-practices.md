---
title: Bästa praxis – LUIS
titleSuffix: Azure Cognitive Services
description: Läs metodtipsen LUIS för att få bästa resultat från LUIS-app-modellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 91ff99f674439580d369aad1490ded85d39d377c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382882"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Metodtips för att skapa en app med language understanding med Cognitive Services
Använd redigerings processen för appar för att skapa LUIS-appen: 

* Skapa språkmodell
* Lägg till några få utbildning exempel yttranden (10 – 15 per avsikt)
* Publicera 
* Testa från slutpunkten 
* Lägga till funktioner

När din app har [publicerats](luis-how-to-publish-app.md)använder du redigerings cykeln för att lägga till funktioner, publicera och testa från slut punkten. Börja inte nästa redigering cykel genom att lägga till fler exempel yttranden. Som tillåter inte LUIS Läs din modell med riktiga användare yttranden. 

För LUIS för att effektivt på dess jobbet i utbildning du inte expandera talade tills den aktuella uppsättningen både exempel och slutpunkt yttranden returnerar trygg, hög förutsägelse poäng. Förbättra poängen med [aktiva inlärnings](luis-concept-review-endpoint-utterances.md)-, [mönster](luis-concept-patterns.md)-och [fras listor](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Gör och inte
I följande lista innehåller metodtips för LUIS appar:

|Gör följande|Gör inte följande|
|--|--|
|[Definiera olika avsikter](#do-define-distinct-intents) |[Lägg till många exempel yttranden till avsikter](#dont-add-many-example-utterances-to-intents) |
|[Hitta en plats för söta mellan för Allmänt och för specifika för varje avsikt](#do-find-sweet-spot-for-intents)|[Använd LUIS som en plattform för utbildning](#dont-use-luis-as-a-training-platform)|
|[Skapa din app upprepade gånger](#do-build-the-app-iteratively)|[Lägg till många exempel yttranden i samma format som ignorerar andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägg till frasen listor och mönster i senare iterationer](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Blanda definitionen av avsikter och entiteter](#dont-mix-the-definition-of-intents-and-entities)|
|[Balansera din yttranden för alla syften](#balance-your-utterances-across-all-intents) förutom ingen avsikt.<br>[Lägg till exempel yttranden NONE avsikt](#do-add-example-utterances-to-none-intent)|[Skapa frasen listor med alla möjliga värden](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Utnyttja funktionen Föreslå för aktiv inlärning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägg till för många mönster](#dont-add-many-patterns)|
|[Övervaka prestanda för din app](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enskild exempel-uttryck som har lagts till](#dont-train-and-publish-with-every-single-example-utterance)|
|[Använda versioner för varje app-iteration](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definiera olika avsikter
Kontrollera att ordförråd för varje avsikten är bara för detta syfte och inte överlappande med en annan typ av avsikt. Om du till exempel vill ha en app som hanterar rese arrangemang som flyg bolags flygningar och hotell kan du välja att ha dessa ämnes områden som separata avsikter eller samma avsikt med entiteter för särskilda data inuti uttryck.

Om ordförråd mellan två avsikter är samma, kombinera avsikten och Använd entiteter. 

Överväg följande exempel talade:

|Exempel på yttranden|
|--|
|Boka en flygning|
|Boka ett hotell|

”Boka en flygning” och ”boka ett hotell” använder samma vokabulär ”boken en”. Det här formatet är detsamma, så det bör vara identiskt med de olika orden av flyg och hotell som extraherade entiteter. 

Mer information:
* Concept [Begrepp om avsikter i din LUIS-app](luis-concept-intent.md)
* Självstudier: [Bygg LUIS-app för att fastställa användar avsikter](luis-quickstart-intents-only.md)
* Anvisningar: [Lägg till avsikter för att fastställa användar avsikt för yttranden](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Hitta söta plats för avsikter
Använd förutsägelsedata från LUIS för att avgöra om dina avsikter överlappar. Överlappande avsikter förvirrar LUIS. Resultatet är att upp bedömning avsikt ligger för nära ett annat syfte. Eftersom LUIS inte använder exakt samma sökväg-informationen för att träna varje gång har ett intent som överlappande chansen som första eller andra i utbildning. Du vill att uttryck-poängen för varje avsikt att ligga längre bort, så att den här flip/vippa inte sker. Bra skillnad för avsikter resulterar i förväntade främsta syftet varje gång. 
 
## <a name="do-build-the-app-iteratively"></a>Skapar då appen upprepade gånger
Behåll en separat uppsättning yttranden som inte används som [exempel yttranden](luis-concept-utterance.md) eller slut punkts yttranden. Förbättras ständigt app för din test. Anpassa testet måste återspegla hur användarna yttranden. Använd den här test uppsättningen för att utvärdera varje iteration eller version av appen. 

Utvecklare bör ha tre uppsättningar av data. Först är exempel talade för att bygga modellen. Andra är för att testa modellen vid slutpunkten. Tredje är blinda testdata som används i [batch testning](luis-how-to-batch-test.md). Den här sista uppsättningen används inte i träna programmet eller skickas på slut punkten.  

Mer information:
* Concept [Redigerings cykel för LUIS-appen](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Lägg till frasen listor och mönster i senare iterationer

Vi rekommenderar att du inte tillämpar dessa metoder innan appen har testats. Du bör förstå hur appen beter sig innan du lägger till [fras listor](luis-concept-feature.md) och [mönster](luis-concept-patterns.md) , eftersom de här funktionerna viktas mer kraftigt än yttranden och kommer att skeva förtroendet. 

När du förstår hur appen beter sig utan dessa kan du lägga till var och en av dessa funktioner som de gäller för din app. Du behöver inte lägga till dessa funktioner med varje [iteration](luis-concept-app-iteration.md) eller ändra funktionerna med respektive version. 

Det går inte att lägga till dem i början av din modell design, men det är enklare att se hur varje funktion ändrar resultatet när modellen har testats med yttranden. 

Vi rekommenderar att du testar via [slut punkten](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) så att du får den extra fördelen med [aktiv inlärning](luis-concept-review-endpoint-utterances.md). Det [interaktiva test fönstret](luis-interactive-test.md) är också en giltig test metod. 
 

### <a name="phrase-lists"></a>Fraslistor

[Fras listor](luis-concept-feature.md) kan du definiera ordlistor av ord som rör din appdomän. Dirigera din fras lista med några få ord och Använd sedan funktionen föreslå så att LUIS känner till mer ord i den vokabulär som är speciell för din app. En fras lista förbättrar identifieringen av Matchnings-och enhets klassificering genom att förstärka signalen som är kopplad till ord eller fraser som är viktiga för din app. 

Lägg inte till alla ord vokabulär eftersom den frasen inte är en exakt matchning. 

Mer information:
* Concept [Fras List funktioner i LUIS-appen](luis-concept-feature.md)
* Så här gör du: [Använd fras listor för att förstärka signalen av ord listan](luis-how-to-add-features.md)

### <a name="patterns"></a>Mönster

Hur användarna yttranden från slutpunkten, liknar varandra, kan avslöja mönster för val av word och placering. Den [mönstret](luis-concept-patterns.md) funktionen tar den här word valmöjligheter och placering tillsammans med reguljära uttryck för att förbättra prognosens noggrannhet. Ett reguljärt uttryck i mönstret kan ord och skiljetecken som du vill ignorera när fortfarande matchar mönstret. 

Använd mönsters [valfria syntax](luis-concept-patterns.md) för interpunktion så att interpunktion kan ignoreras. Använd den [explicita listan](luis-concept-patterns.md#explicit-lists) för att kompensera för mönster. eventuella syntaxfel. 

Mer information:
* Concept [Mönster förbättrar förutsägelse noggrannhet](luis-concept-patterns.md)
* Så här gör du: [Så här lägger du till mönster för att förbättra förutsägelse noggrannhet](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Balansera din yttranden för alla avsikter

För att LUIS-förutsägelserna ska vara korrekta, måste antalet exempel yttranden i varje avsikt (utom för ingen avsikt) vara relativt lika. 

Om du har en avsikt med 100-exempel yttranden och en avsikt med 20 exempel yttranden, har 100-uttryck-avsikten en högre frekvens av förutsägelse.  

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempel yttranden NONE avsikt

Avsikten är återställnings avsikten, vilket innebär att allting utanför ditt program. Lägg till en exempel-uttryck på Ingen avsiktshantering för varje 10 exempel yttranden i resten av LUIS-appen.

Mer information:
* Concept [Förstå vilka bra yttranden är för din LUIS-app](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Utnyttja funktionen Föreslå för aktiv inlärning

Använd [aktiv inlärning](luis-how-to-review-endpoint-utterances.md)'s **granska endpoint yttranden** regelbundet, i stället för att lägga till fler exempel yttranden avsikter. Eftersom appen ständigt tar emot endpoint yttranden, den här listan växer och ändra.

Mer information:
* Concept [Begrepp för att aktivera aktiv inlärning genom att granska slut punkts yttranden](luis-concept-review-endpoint-utterances.md)
* Självstudier: [Självstudier: Åtgärda osäker förutsägelse genom att granska slut punkts yttranden](luis-tutorial-review-endpoint-utterances.md)
* Så här gör du: [Så här granskar du slut punkts yttranden i LUIS-portalen](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Att övervaka prestanda för din app

Övervaka förutsägelse noggrannheten med hjälp av [batch](luis-concept-batch-test.md) -testuppsättningen. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempel yttranden till avsikter

När appen har publicerats kan du bara lägga till yttranden från aktiv inlärning i iterativ process. Om yttranden liknar för, lägger du till ett mönster. 

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte LUIS som en plattform för utbildning

LUIS är specifik för en språkmodell domän. Den är inte avsedd att fungera som en allmän utbildnings plattform för naturliga språk. 

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

## <a name="do-use-versions-for-each-app-iteration"></a>Använd versioner för varje app-iteration

Varje redigerings cykel bör vara i en ny [version](luis-concept-version.md)som klonas från en befintlig version. LUIS har ingen gräns för versioner. Ett versions namn används som en del av API-vägen, så det är viktigt att du väljer tecken som tillåts i en URL och att de ligger inom antalet 10 tecken i en version. Utveckla en strategi för versions namn för att hålla dina versioner organiserade. 

Mer information:
* Concept [Förstå hur och när du ska använda en LUIS-version](luis-concept-version.md)
* Så här gör du: [Använda versioner för att redigera och testa utan att påverka mellanlagrings-eller produktions program](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [planera din app](luis-how-plan-your-app.md) i LUIS-appen.
