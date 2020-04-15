---
title: Metodtips för att skapa luis-appen
description: Lär dig de bästa metoderna för att få bästa resultat från LUIS-appens modell.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 525d450084723a53ae090319d9ebf3f68d63beee
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382391"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Metodtips för att skapa en LUIS-app (Language Understanding)
Använd apputfattningsprocessen för att skapa luis-appen:

* Skapa språkmodeller (avsikter och entiteter)
* Lägg till några exempelyttranden för träning (15–30 per avsikt)
* Publicera till slutpunkt
* Testa från slutpunkten

När appen [har publicerats](luis-how-to-publish-app.md)använder du utvecklingslivscykeln för att lägga till funktioner, publicera och testa från slutpunkten. Börja inte nästa redigeringscykel genom att lägga till fler exempelyttranden eftersom det inte låter LUIS lära sig din modell med verkliga användaryttranden.

Expandera inte yttrandena förrän den aktuella uppsättningen med både exempel- och slutpunktsyttranden returnerar självsäkra, höga förutsägelsepoäng. Förbättra poäng med [aktivt lärande](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Gör och inte
Följande lista innehåller metodtips för LUIS-appar:

|Gör följande|Gör inte följande|
|--|--|
|[Definiera distinkta avsikter](#do-define-distinct-intents)<br>[Lägga till beskrivningar i avsikter](#do-add-descriptors-to-intents) |[Lägga till många exempelyttranden i avsikter](#dont-add-many-example-utterances-to-intents)<br>[Använda få eller enkla entiteter](#dont-use-few-or-simple-entities) |
|[Hitta en sweet spot mellan för generisk och för specifik för varje avsikt](#do-find-sweet-spot-for-intents)|[Använda LUIS som träningsplattform](#dont-use-luis-as-a-training-platform)|
|[Skapa din app iterativt med versioner](#do-build-your-app-iteratively-with-versions)<br>[Skapa entiteter för modelldets sönderdelning](#do-build-for-model-decomposition)|[Lägga till många exempel yttranden av samma format, ignorera andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägga till mönster i senare iterationer](#do-add-patterns-in-later-iterations)|[Blanda definitionen av avsikter och entiteter](#dont-mix-the-definition-of-intents-and-entities)|
|[Balansera dina yttranden över alla avsikter](#balance-your-utterances-across-all-intents) utom avsikten Ingen.<br>[Lägga till exempelyttranden i ingen avsikt](#do-add-example-utterances-to-none-intent)|[Skapa beskrivningar med alla möjliga värden](#dont-create-descriptors-with-all-the-possible-values)|
|[Utnyttja förslagsfunktionen för aktiv inlärning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägga till för många mönster](#dont-add-many-patterns)|
|[Övervaka appens prestanda med batchtestning](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enskilt exempel yttrande tillagd](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definierar olika avsikter
Se till att ordförrådet för varje avsikt är bara för den avsikten och inte överlappande med en annan avsikt. Om du till exempel vill ha en app som hanterar researrangemang som flyg och hotell kan du välja att ha dessa ämnesområden som separata avsikter eller samma avsikt med entiteter för specifika data i uttrycket.

Om ordförrådet mellan två avsikter är detsamma kombinerar du avsikten och använder entiteter.

Tänk på följande exempelyttranden:

|Exempel på yttranden|
|--|
|Boka ett flyg|
|Boka ett hotell|

`Book a flight`och `Book a hotel` använda samma ordförråd `book a `. Det här formatet är detsamma så det bör `flight` vara `hotel` samma avsikt med de olika orden i och som extraherade entiteter.

## <a name="do-add-descriptors-to-intents"></a>Lägg till beskrivningar i avsikter

Deskriptorer hjälper till att beskriva funktioner för en avsikt. En deskriptor kan vara en fraslista med ord som är viktiga för den avsikten eller en entitet som är viktig för den avsikten.

## <a name="do-find-sweet-spot-for-intents"></a>Hittar sweet spot för avsikter
Använd förutsägelsedata från LUIS för att avgöra om dina avsikter överlappar varandra. Överlappande avsikter förvirrar LUIS. Resultatet är att den högsta poängavsikten är för nära en annan avsikt. Eftersom LUIS inte använder exakt samma sökväg genom data för träning varje gång, har en överlappande avsikt en chans att bli första eller andra i träning. Du vill att yttrandets poäng för varje avsikt ska vara längre ifrån varandra så att denna flip /flop inte händer. Bra distinktion för avsikter bör resultera i den förväntade högsta avsikten varje gång.

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Bygg din app iterativt med versioner

Varje redigeringscykel ska finnas i en ny [version](luis-concept-version.md), klonad från en befintlig version.

## <a name="do-build-for-model-decomposition"></a>Gör bygg för modelldets sönderdelning

Modell nedbrytning har en typisk process av:

* skapa **avsikt** baserat på klient-appens användaravsikter
* lägga till 15-30 exempel yttranden baserat på verkliga användarindata
* etikett på den översta nivåns datakoncept i exempelutsyn
* bryta datakoncept i delkomponenter
* lägga till beskrivningar (funktioner) i delkomponenter
* lägga till beskrivningar (funktioner) i avsikt

När du har skapat avsikten och lagt till exempelyttranden beskriver följande exempel entitetsuppdelning.

Börja med att identifiera fullständiga databegrepp som du vill extrahera i ett uttryck. Det här är din maskininlärda entitet. Sedan sönderdelas frasen i dess delar. Detta inkluderar identifiering av delkomponenter (som entiteter), tillsammans med beskrivningar och begränsningar.

Om du till exempel vill extrahera en adress kan den `Address`översta datorinlärda entiteten kallas . När du skapar adressen identifierar du några av dess underkomponenter, till exempel gatuadress, ort, delstat och postnummer.

Fortsätt att förmultna dessa element genom **att begränsa** postnumret till ett reguljärt uttryck. Förmultna gatuadressen i delar av ett gatunummer (med ett fördefinierat nummer), ett gatunamn och en gatutyp. Gatutypen kan beskrivas med en **beskrivningslista** som aveny, cirkel, väg och körfält.

V3-redigerings-API:et möjliggör nedbrytning av modeller.

## <a name="do-add-patterns-in-later-iterations"></a>Lägg till mönster i senare iterationer

Du bör förstå hur appen beter sig innan du lägger till [mönster](luis-concept-patterns.md) eftersom mönster viktas hårdare än exempelyttranden och förvränger förtroendet.

När du förstår hur appen fungerar lägger du till mönster när de gäller för din app. Du behöver inte lägga till dem med varje [iteration](luis-concept-app-iteration.md).

Det skadar inte att lägga till dem i början av modelldesignen, men det är lättare att se hur varje mönster ändrar modellen efter att modellen har testats med yttranden.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Balansera dina yttranden över alla avsikter

För att LUIS-förutsägelser ska vara korrekta måste antalet exempelyttranden i varje avsikt (förutom avsikten Ingen) vara relativt lika.

Om du har en avsikt med 100 exempelyttranden och en avsikt med 20 exempelyttranden, har 100-yttrandet en högre förutsägelsehastighet.

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempelyttranden i ingen avsikt

Den här avsikten är reservavsikten och anger allt utanför ditt program. Lägg till ett exempel yttrande till ingen avsikt för varje 10 exempel yttranden i resten av luis-appen.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Utnyttja funktionen föreslå för aktivt lärande

Använd [aktivt inlärnings](luis-how-to-review-endpoint-utterances.md) **granskningsslutpunktsyttranden** regelbundet, i stället för att lägga till fler exempelyttranden i avsikter. Eftersom appen ständigt tar emot slutpunktsyttranden växer och ändras den här listan.

## <a name="do-monitor-the-performance-of-your-app"></a>Övervaka appens prestanda

Övervaka förutsägelsenoggrannheten med hjälp av en [batchtestuppsättning.](luis-concept-batch-test.md)

Behåll en separat uppsättning yttranden som inte används som [exempelyttranden](luis-concept-utterance.md) eller slutpunktsyttranden. Fortsätt att förbättra appen för din testuppsättning. Anpassa testuppsättningen så att den återspeglar verkliga användaryttranden. Använd den här testuppsättningen för att utvärdera varje iteration eller version av appen.

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempelyttranden i avsikter

När appen har publicerats lägger du bara till yttranden från aktiv inlärning i livscykelprocessen för utveckling. Om yttranden är för lika lägger du till ett mönster.

## <a name="dont-use-few-or-simple-entities"></a>Använd inte få eller enkla entiteter

Entiteter är byggda för datautvinning och förutsägelse. Det är viktigt att varje avsikt har datorinlärda entiteter som beskriver data i avsikten. Detta hjälper LUIS förutsäga avsikten, även om klientprogrammet inte behöver använda den extraherade entiteten.

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte LUIS som träningsplattform

LUIS är specifikt för en språkmodells domän. Det är inte tänkt att fungera som en allmän naturlig språk utbildning plattform.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Lägg inte till många exempelyttranden i samma format och ignorera andra format

LUIS förväntar sig variationer i en avsikts yttranden. Yttrandena kan variera samtidigt som de har samma övergripande innebörd. Variationer kan omfatta uttryckslängd, ordval och ordplacering.

|Använd inte samma format|Använd varierande format|
|--|--|
|Köp en biljett till Seattle<br>Köp en biljett till Paris<br>Köp en biljett till Orlando|Köp 1 biljett till Seattle<br>Boka två platser på röda ögon till Paris nästa måndag<br>Jag skulle vilja boka 3 biljetter till Orlando för vårlovet|

Den andra kolumnen använder olika verb (köp, reserv, bok), olika kvantiteter (1, två, 3) och olika ordarrangemang, men alla har samma avsikt att köpa flygbiljetter för resor.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Blanda inte definitionen av avsikter och entiteter

Skapa en avsikt för alla åtgärder som din bot kommer att vidta. Använd entiteter som parametrar som gör åtgärden möjlig.

Skapa en **BookFlight-avsikt** för en robot som bokar flygresor. Skapa inte en avsikt för varje flygbolag eller varje destination. Använd dessa data som [entiteter](luis-concept-entity-types.md) och markera dem i exempelyttrandena.

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Skapa inte deskriptorer med alla möjliga värden

Ge några exempel i [deskriptorfraslistorna](luis-concept-feature.md) men inte varje ord. LUIS generaliserar och tar hänsyn till sammanhanget.

## <a name="dont-add-many-patterns"></a>Lägg inte till många mönster

Lägg inte till för många [mönster.](luis-concept-patterns.md) LUIS är tänkt att lära sig snabbt med färre exempel. Överbelasta inte systemet i onödan.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Träna och publicera inte med varje enskilt exempel yttrande

Lägg till 10 eller 15 yttranden före utbildning och publicering. Det gör att du kan se effekten på förutsägelse noggrannhet. Att lägga till ett enda uttryck kanske inte har en synlig inverkan på poängen.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [planerar appen](luis-how-plan-your-app.md) i LUIS-appen.
