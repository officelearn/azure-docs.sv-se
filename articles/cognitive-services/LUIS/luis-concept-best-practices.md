---
title: Bästa praxis – LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig metod tipsen för LUIS för att få bästa möjliga resultat från din LUIS-Apps modell.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 64d67edaf5affbc908fba7b6c261096589bc84d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487622"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Metod tips för att skapa en språk förståelse för appen med Cognitive Services
Använd redigerings processen för appar för att skapa LUIS-appen: 

* Bygg språks modeller (avsikter och entiteter)
* Lägg till några utbildnings exempel yttranden (15-30 per avsikt)
* Publicera till slut punkt
* Testa från slut punkt 

När din app har [publicerats](luis-how-to-publish-app.md)använder du utvecklings livs cykeln för att lägga till funktioner, publicera och testa från slut punkten. Påbörja inte nästa redigerings cykel genom att lägga till fler exempel yttranden eftersom det inte tillåter LUIS att lära sig din modell med verkliga användar yttranden. 

Expandera inte yttranden förrän den aktuella uppsättningen av både exempel-och slut punkts yttranden returnerar trygg, hög förutsägelse poäng. Förbättra poängen med hjälp av [aktiv inlärning](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Gör och inte
I följande lista finns metod tips för LUIS-appar:

|Gör följande|Gör inte följande|
|--|--|
|[Definiera distinkta avsikter](#do-define-distinct-intents)<br>[Lägg till beskrivningar till avsikter](#do-add-descriptors-to-intents) |[Lägg till många exempel yttranden till avsikter](#dont-add-many-example-utterances-to-intents)<br>[Använd få eller enkla entiteter](#dont-use-few-or-simple-entities) |
|[Hitta en söt punkt mellan för allmän och för varje avsikt](#do-find-sweet-spot-for-intents)|[Använd LUIS som utbildnings plattform](#dont-use-luis-as-a-training-platform)|
|[Bygg din app iterativt med versioner](#do-build-your-app-iteratively-with-versions)<br>[Bygg entiteter för modell nedbrytning](#do-build-for-model-decomposition)|[Lägg till många exempel yttranden i samma format, ignorera andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägga till mönster i senare iterationer](#do-add-patterns-in-later-iterations)|[Blanda definitionen av avsikter och entiteter](#dont-mix-the-definition-of-intents-and-entities)|
|[Balansera din yttranden för alla syften](#balance-your-utterances-across-all-intents) förutom ingen avsikt.<br>[Lägg till exempel yttranden i ingen avsikt](#do-add-example-utterances-to-none-intent)|[Skapa beskrivningar med alla möjliga värden](#dont-create-descriptors-with-all-the-possible-values)|
|[Dra nytta av funktionen föreslå för aktiv inlärning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägg till för många mönster](#dont-add-many-patterns)|
|[Övervaka appens prestanda med batch-testning](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enda exempel-uttryck tillagt](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definiera distinkta avsikter
Se till att ord listan för varje avsikt är just för den avsikten och inte överlappar med en annan avsikt. Om du till exempel vill ha en app som hanterar rese arrangemang som flyg bolags flygningar och hotell kan du välja att ha dessa ämnes områden som separata avsikter eller samma avsikt med entiteter för särskilda data inuti uttryck.

Om ord listan mellan två avsikter är densamma kombinerar du avsikten och använder entiteter. 

Överväg följande exempel yttranden:

|Exempel på yttranden|
|--|
|Boka en flygning|
|Boka ett hotell|

`Book a flight` och `Book a hotel` använda samma vokabulär av `book a `. Det här formatet är detsamma, så det bör vara samma sak med de olika orden i `flight` och `hotel` som extraherade entiteter. 

## <a name="do-add-descriptors-to-intents"></a>Lägg till beskrivningar till avsikter

Med hjälp av beskrivningar kan du beskriva funktioner för ett avsikts sätt. En beskrivning kan vara en fras lista med ord som är viktiga för avsikten eller en enhet som är viktig för avsikten. 

## <a name="do-find-sweet-spot-for-intents"></a>Hittar du söt för avsikter
Använd förutsägelse data från LUIS för att avgöra om dina avsikter överlappar. Överlappande avsikter förvirrar LUIS. Resultatet är att den översta bedömnings avsikten är för nära en annan avsikt. Eftersom LUIS inte använder exakt samma sökväg via data för utbildning varje gång, har en överlappande avsikt möjlighet att vara första eller andra i utbildningen. Du vill att uttryck-poängen för varje avsikt att ligga längre bort, så att den här flip/vippa inte sker. Bra distinktion för avsikter bör resultera i förväntat topp-avsikt varje gång. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Skapa en app iterativt med versioner

Varje redigerings cykel bör vara i en ny [version](luis-concept-version.md)som klonas från en befintlig version. 

## <a name="do-build-for-model-decomposition"></a>Skapa modell sammansättning

Modell dispositionen har en typisk process för:

* skapa **avsikt** baserat på klientens användar avsikter
* Lägg till 15-30-exempel yttranden baserat på verkliga indata från användaren
* Märk data koncept på översta nivån i exemplet uttryck
* Bryt data koncept i del komponenter
* Lägg till beskrivningar (funktioner) i under komponenter
* Lägg till beskrivningar (funktioner) i avsikten 

När du har skapat avsikten och lagt till exempel yttranden, beskriver följande exempel enhets diskompositionen. 

Börja med att identifiera fullständiga data koncept som du vill extrahera i en uttryck. Det här är din enhet som du har lärt dig. Dela sedan upp frasen i dess delar. Detta omfattar att identifiera under komponenter (som entiteter) tillsammans med beskrivningar och begränsningar. 

Om du till exempel vill extrahera en adress, kan den översta enheten som sparats på datorn anropas `Address`. När du skapar adressen identifierar du några av dess del komponenter, till exempel gatuadress, stad, region och post nummer. 

Fortsätt sammanställningen av dessa element genom att **begränsa** post numret till ett reguljärt uttryck. Dela upp gatuadressen i delar av ett gatu nummer (med ett fördefinierat nummer), ett gatu namn och en gatu typ. Gatu typen kan beskrivas med en **beskrivnings** lista, till exempel minimering, cirkel, väg och Lane.

V3-redigerings-API: n möjliggör modell dekomposition. 

## <a name="do-add-patterns-in-later-iterations"></a>Lägg till mönster i senare iterationer

Du bör förstå hur appen beter sig innan du lägger till [mönster](luis-concept-patterns.md) eftersom mönstren viktas mer kraftigt än yttranden och kommer att skeva förtroendet. 

När du förstår hur appen beter sig lägger du till mönster som de gäller för din app. Du behöver inte lägga till dem med varje [iteration](luis-concept-app-iteration.md). 

Det går inte att lägga till dem i början av din modell design, men det är enklare att se hur varje mönster ändrar modellen när modellen har testats med yttranden. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Balansera din yttranden för alla avsikter

För att LUIS-förutsägelserna ska vara korrekta, måste antalet exempel yttranden i varje avsikt (utom för ingen avsikt) vara relativt lika. 

Om du har en avsikt med 100-exempel yttranden och en avsikt med 20 exempel yttranden, har 100-uttryck-avsikten en högre frekvens av förutsägelse.  

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempel yttranden i ingen avsikt

Avsikten är återställnings avsikten, vilket indikerar allt utanför ditt program. Lägg till ett exempel uttryck till ingen avsikt för varje 10-exempel yttranden i resten av din LUIS-app.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Använd funktionen föreslå funktion för aktiv inlärning

Använd den [aktiva utbildnings](luis-how-to-review-endpoint-utterances.md) **slut punkten yttranden** regelbundet, i stället för att lägga till fler exempel yttranden till avsikter. Eftersom appen ständigt tar emot slut punkts yttranden, växer och ändras den här listan.

## <a name="do-monitor-the-performance-of-your-app"></a>Övervaka appens prestanda

Övervaka förutsägelse noggrannheten med hjälp av [batch](luis-concept-batch-test.md) -testuppsättningen. 

Behåll en separat uppsättning yttranden som inte används som [exempel yttranden](luis-concept-utterance.md) eller slut punkts yttranden. Fortsätt att förbättra appen för din test uppsättning. Anpassa test uppsättningen så att den återspeglar Real User yttranden. Använd den här test uppsättningen för att utvärdera varje iteration eller version av appen. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempel yttranden till avsikter

När appen har publicerats kan du bara lägga till yttranden från Active Learning i utvecklings livs cykel processen. Om yttranden är för likartade lägger du till ett mönster. 

## <a name="dont-use-few-or-simple-entities"></a>Använd inte några få eller enkla entiteter

Entiteter skapas för data extrahering och förutsägelser. Det är viktigt att varje avsikt har enheter som har registrerats av enheten som beskriver data i avsikten. Det hjälper LUIS att förutsäga avsikten, även om klient programmet inte behöver använda den extraherade entiteten. 

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte LUIS som utbildnings plattform

LUIS är specifik för en språk modells domän. Den är inte avsedd att fungera som en allmän utbildnings plattform för naturliga språk. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Lägg inte till många exempel yttranden i samma format, ignorera andra format

LUIS förväntar sig variationer i ett avsikts yttranden. Yttranden kan variera med samma övergripande betydelse. Variationer kan vara uttryck längd, Word-val och ord placering. 

|Använd inte samma format|Använd varierande format|
|--|--|
|Köp ett ärende till Seattle<br>Köp ett ärende till Paris<br>Köp ett ärende till Orlando|Köp 1-biljett till Seattle<br>Reservera två platser på det röda ögat till Paris nästa måndag<br>Jag vill boka 3 biljetter till Orlando för fjädrad rast|

Den andra kolumnen använder olika verb (Köp, reservera, bok), olika kvantiteter (1, 2, 3) och olika ordningar av ord, men alla har samma avsikt att köpa flyg biljetter för resor. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Blanda inte definitionen av avsikter och entiteter

Skapa ett avsikts sätt för alla åtgärder som din robot tar. Använd entiteter som parametrar som gör åtgärden möjlig. 

För en robot som ska boka flyg Plans flygningar skapar du en **BookFlight** avsikt. Skapa inte en avsikt för varje flyg bolag eller varje mål. Använd dessa delar av data som [entiteter](luis-concept-entity-types.md) och markera dem i exemplet yttranden. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Skapa inte beskrivningar med alla möjliga värden

Ange några exempel i [listorna](luis-concept-feature.md) beskrivnings fraser, men inte alla ord. LUIS generaliserar och tar hänsyn till sammanhang i kontot. 

## <a name="dont-add-many-patterns"></a>Lägg inte till många mönster

Lägg inte till för många [mönster](luis-concept-patterns.md). LUIS är tänkt att lära sig snabbt med färre exempel. Överbelasta inte systemet i onödan.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Träna och publicera inte med alla enkla exempel uttryck

Lägg till 10 eller 15 yttranden innan du tränar och publicerar. Det gör att du kan se påverkan på förutsägelse noggrannhet. Att lägga till en enskild uttryck kanske inte har en synlig inverkan på poängen. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [planerar din app](luis-how-plan-your-app.md) i Luis-appen.
