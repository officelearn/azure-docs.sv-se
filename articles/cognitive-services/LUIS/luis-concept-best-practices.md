---
title: Förstå THOMAS metodtips - Azure | Microsoft Docs
description: Lär dig THOMAS bästa praxis för att få bästa möjliga resultat.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: f68e05ba40827375fbd76448e38916c682075228
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264017"
---
# <a name="best-practices"></a>Bästa praxis
Använd appen redigering processen för att bygga din app THOMAS. 

* Skapa språkmodell
* Lägg till några utbildning exempel utterances (10 – 15 per avsikt)
* Publicera 
* Testa från slutpunkten 
* Lägg till funktioner

När din app är [publicerade](publishapp.md), använder du Lägg till funktioner redigering cykeln, publicera och testa från slutpunkten. Börja inte nästa redigering cykel genom att lägga till fler exempel utterances. Som tillåter inte THOMAS Läs din modell med verkliga användaren utterances. 

För THOMAS vara effektivt fjäderlayoutjobbet learning du inte expandera utterances tills den aktuella mängden både exempel och slutpunkten utterances returnerar säker, hög förutsägelse resultat. Förbättra poäng med aktiva learning [mönster](luis-concept-patterns.md), och [fras listor](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Gör och inte
I följande lista innehåller metodtips för THOMAS appar:

|Gör följande|Gör inte följande|
|--|--|
|[Definiera olika avsikter](#do-define-distinct-intents) |[Lägg till många exempel utterances avsikter](#dont-add-many-example-utterances-to-intents) |
|[Hitta en plats för av mellan för Allmänt och för specifika för varje avsikt](#do-find-sweet-spot-for-intents)|[Använda THOMAS som en plattform för utbildning](#dont-use-luis-as-a-training-platform)|
|[Skapa din app upprepade gånger](#do-build-the-app-iteratively)|[Lägg till många exempel utterances i samma format som ignorerar andra format](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Lägg till fras listor och mönster i senare iterationer](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Blanda definitionen av intents och enheter](#dont-mix-the-definition-of-intents-and-entities)|
|[Lägg till exempel utterances ingen avsiktshantering](#do-add-example-utterances-to-none-intent)|[Skapa frasen listor med alla möjliga värden](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Utnyttja funktionen förslag för aktiva learning](#do-leverage-the-suggest-feature-for-active-learning)|[Lägga till så många mönster](#dont-add-many-patterns)|
|[Övervaka prestanda för din app](#do-monitor-the-performance-of-your-app)|[Träna och publicera med varje enskild exempel utterance som lagts till](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definiera distinkta avsikter
Kontrollera att ordlistan för varje avsikt är bara för att avsikten och inte överlappande med ett annat syfte. Om du vill ha en app som hanterar resa åtgärder, till exempel flygbolag flygplan och hotell välja du till exempel att dessa som separata avsikter eller samma avsikten med entiteter för specifika data i utterance.

Om ordförråd mellan två avsikter är samma, kombinera avsikten och entiteter. 

Överväg följande exempel utterances:

```
Book a flight
Book a hotel
```

”Boka en svarta” och ”bok hotell” använder samma transformering ”book en”. Detta överlappande så att det ska vara samma avsikten med andra ord svarta och hotell extraherade entiteter. 

## <a name="do-find-sweet-spot-for-intents"></a>Hitta av platsen för avsikter
Använd förutsägelse data från THOMAS för att avgöra om dina avsikter överlappar. Överlappande avsikter confuses THOMAS. Resultatet är att upp bedömningen avsikt för Stäng för att en annan avsikt. Eftersom THOMAS inte använder exakt samma sökväg-informationen för att träna varje gång har överlappande syftet möjlighet att första eller andra i utbildning. Vill du den utterance poängen för varje avsikt att vara längre ifrån varandra så att det inte inträffar. Bra skillnad för avsikter bör resultera i förväntade översta avsikten varje gång. 
 
## <a name="do-build-the-app-iteratively"></a>Skapar då appen upprepade gånger
Behåll en separat *hemlig* testa som inte används av [exempel utterances](luis-concept-utterance.md) eller utterances slutpunkt. Behåll förbättra app för test-uppsättningen. Anpassa test konfigurera användare utterances. Använda hemlig testet ange att utvärdera varje iteration. 

Utvecklare ska ha tre datauppsättningar. Först är exempel utterances för att skapa modellen. Andra är för att testa modellen vid slutpunkten. Tredje är blind testa data som används i [batch testa](luis-how-to-batch-test.md). Den här sista uppsättningen är inte används i utbildning för programmet eller skickas på slutpunkten.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Lägg till fras listor och mönster i senare iterationer
[Fras listor](luis-concept-feature.md) kan du definiera ordlistor för ord som är relaterade till din app-domän. Seed din frasen listan med några ord och Använd sedan funktionen förslag så THOMAS vet om flera ord i ordlistan. Lägg inte till varje ord till ordlistan eftersom den frasen inte är en exakt matchning. 

Verklig användare utterances från slutpunkten, liknar varandra, kan du få word val och placering. Den [mönster](luis-concept-patterns.md) funktionen tar den word val och placeringen tillsammans med reguljära uttryck för att förbättra prognosens noggrannhet. Ett reguljärt uttryck i mönstret möjliggör ord och skiljetecken som du vill ignorera när fortfarande matchar mönstret. 

Använd mönstret valfria syntax för skiljetecken så skiljetecken kan ignoreras.

Använd inte dessa metoder innan din app har tagit emot endpoint begäranden eftersom som skeva förtroende.  

## <a name="do-add-example-utterances-to-none-intent"></a>Lägg till exempel utterances ingen avsiktshantering
Det här är reserven avsikt, anges allt utanför tillämpningsprogrammet. Lägg till en exempel-utterance avsiktshantering för varje 10 exempel utterances i resten av appen THOMAS NONE.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Utnyttja funktionen förslag för aktiva learning
Använd [active learning](label-suggested-utterances.md)'s **granska endpoint utterances** regelbundet, i stället för att lägga till fler exempel utterances avsikter. Eftersom appen emot ständigt endpoint utterances, växande listan och ändra.

## <a name="do-monitor-the-performance-of-your-app"></a>Övervaka prestandan för din app
Övervaka förutsägelsefunktionen med hjälp av ett test. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Lägg inte till många exempel utterances till avsikter
När appen publiceras bara lägga till utterances från aktiva learning iterativ pågår. Om utterances liknar för, kan du lägga till ett mönster. 

## <a name="dont-use-luis-as-a-training-platform"></a>Använd inte THOMAS som en plattform för utbildning
THOMAS är specifika för en språkmodell domän. Det är inte avsedda att fungera som en allmän utbildning plattform. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Lägg inte till många exempel utterances i samma format som ignorerar andra format
THOMAS förväntar variationer i en avsikt utterances. Utterances kan variera när med samma övergripande innebörd. Variationer kan innehålla utterance längd, word val och word placering. 

|Använd inte samma format|Använd olika format|
|--|--|
|Köp en biljett till Seattle<br>Köp en biljett till Paris<br>Köp en biljett till Orlando|Köpa 1 biljetten Seattle<br>Reservera två platser på det röda ögat att Paris nästa måndag<br>Jag skulle vilja bok 3 biljetter till Orlando för vår break|

Den andra kolumnen använder olika verb (köp, Reservera, bok), olika kvantiteter (1, två, 3), och olika uppställningar av ord, men alla ha samma avsikt köper flygbolag biljetter för. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Blanda inte definitionen av intents och enheter
Skapa din bot tar syftet för alla åtgärder. Använda entiteter som parametrar som möjliggör åtgärden. 

En chatbot som kommer bok flygbolag flygplan, skapa en **BookFlight** avsikt. Skapa inte en avsett för varje flygbolag eller alla målservrar. Använd de delarna av data som [entiteter](luis-concept-entity-types.md) och markera dem i exempel utterances. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Skapa inte frasen listor med alla möjliga värden
Ange några exempel i den [fras listor](luis-concept-feature.md) men inte alla ord. THOMAS generalisering och tar hänsyn till kontexten. 

## <a name="dont-add-many-patterns"></a>Lägg inte till många mönster
Lägg inte till för många [mönster](luis-concept-patterns.md). THOMAS är avsedd att lära dig snabbt med färre exempel. Systemet inte överbelastas i onödan.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Inte träna och publicera med varje enskild exempel utterance
Lägg till 10 eller 15 utterances innan utbildning och publicering. Där du kan visa effekten på prognosens noggrannhet. Att lägga till en enda utterance kan inte ha en synlig inverkan på poängen. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [planera din app](plan-your-app.md) i THOMAS appen.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
