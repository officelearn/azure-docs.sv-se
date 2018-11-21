---
title: Är dina data klara för datavetenskap? -Utvärdering av Azure Machine Learning | Microsoft Docs
description: Fyra villkor som måste uppfylla för att vara klara för datavetenskap. Den här videon har konkreta exempel som hjälper med grundläggande data utvärdering.
keywords: relevanta data, utvärdera data, förbereda data, Datakriterier, data som är redo
services: machine-learning
documentationcenter: na
author: ericlicoding
ms.custom: (previous ms.author=hshapiro, author=heatherbshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.openlocfilehash: d762b0b82364293bb8a946dec575ac099dcd9172
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263258"
---
# <a name="is-your-data-ready-for-data-science"></a>Är dina data klara för datavetenskap?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Datavetenskap för nybörjare-serien
Lär dig att utvärdera dina data så att den uppfyller grundläggande kriterier för att vara klara för datavetenskap.

Titta på alla för att få ut det mesta av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabbintroduktion i datakunskap med fem korta filmer.

* Video 1: [5 frågor och svar om datavetenskap](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek.)*
* Video 2: Är dina data klara för datavetenskap?
* Video 3: [Ställ en fråga som du kan svara på med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Avskriften: Är dina data klara för datavetenskap?
Välkommen till ”är dina data klara för datavetenskap”? andra videon i serien *datavetenskap för nybörjare*.  

Innan datavetenskap kan ge dig de svar som du vill ha så att det vissa högkvalitativa råmaterial att arbeta med. Precis som gör en platt, desto bättre ingredienser du börjar med, desto bättre slutliga produkt. 

## <a name="criteria-for-data"></a>Kriterier för data
Det finns vissa komponenter som måste hämtas tillsammans inklusive i dataforskning:

* Relevanta
* Ansluten
* Korrekt
* Tillräckligt för att arbeta med

## <a name="is-your-data-relevant"></a>Är dina data relevanta?
Så att den första komponenten - du behöver data som är relevanta.

![Relevanta data jämfört med irrelevanta data – utvärdera data](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Till vänster visas i tabell blod alkohol andelen sju personer som testas utanför ett fält i Boston, Red Sox batting medelvärdet i deras senaste spel och pris av i arkivet närmaste bekvämlighet.

Det här är alla perfekt legitima data. Endast fel är att det inte är relevanta. Det finns ingen uppenbar relation mellan dessa siffror. Om någon har gett dig aktuella priset för mjölk och Red Sox batting medelvärdet, finns det inget sätt som du kan gissa blod alkohol innehållet.

Nu vill titta på tabellen till höger. Den här gången varje person brödtext lagringsenheter har mäts samt antalet drycker som de har haft.  Talen i varje rad är nu relevanta till varandra. Om jag gav dig min brödtext lagringsenheter och antalet Margaritas som jag har haft, du kan göra en uppskattning av min blod alkohol innehåll.

## <a name="do-you-have-connected-data"></a>Du har anslutit data?
Nästa ingrediens är anslutna.

![Anslutna data jämfört med frånkopplade data – data villkor, data som är redo](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Här är några relevanta data på kvaliteten på hamburgers: grill temperatur, patty vikt och klassificering i lokala livsmedelskedjan magazine. Men Observera luckor i tabellen till vänster.

De flesta datauppsättningar saknar vissa värden. Det är vanligt att ha hål så här och det finns sätt att arbeta runt dem. Men om det finns för mycket saknas, dina data börjar likna Schweiz ost.

Om du tittar på tabellen till vänster, finns det så mycket data som saknas, det är svårt att få fram alla slags relationen mellan galler temperatur och patty vikt. Det här exemplet visar frånkopplade data.

Tabellen till höger, men är full och slutför – ett exempel på anslutna data.

## <a name="is-your-data-accurate"></a>Är dina data korrekt?
Nästa ingrediens är precision. Här är fyra mål att träffa.

![Korrekta data jämfört med felaktiga data - Datakriterier](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Titta på målet i det övre högra hörnet. Det finns en nära gruppering direkt runt tjurar ögat. Det är naturligtvis korrekt. Oväntat, på språket för datavetenskap prestanda till höger mål under den också anses vara korrekt.

Om du som anges i mitten av dessa pilar, ser du att det ligger mycket nära tjurar ögat. Pilarna sprids ut hela målet, så att de betraktas inexakt, men de är uppbyggd kring tjurar ögat, så att de betraktas som korrekt.

Nu se ut på angivna längst upp till vänster. Pilarna når här mycket nära varandra en nära gruppering. De exakta, men de är felaktigt eftersom mitten är stor tjurar ögat. Pilarna i nedre vänstra målet är felaktiga såväl som inexakt. Den här archer behöver mer praxis.

## <a name="do-you-have-enough-data-to-work-with"></a>Har du tillräckligt med data att fungera med?
Slutligen är ingrediens #4 tillräckligt med data.

![Har du tillräckligt med data för analys? Utvärdering av data](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Tänk på varje datapunkt i tabellen som ett penseldrag i en tavla. Om du har några av dem kan de målningen avslutas kan vara fuzzy – det är svårt att se vad det är.

Om du lägger till lite mer penseldrag startar din tavla frågesporten lite.

När du har oändliga möjligheter som finns tillräckligt med linjer, du kan bara se tillräckligt för att göra vissa breda beslut. Är en plats kanske du vill gå till? Det verkar ljus, som ser ut som rent vatten – Ja, som är där jag på semester.

När du lägger till mer data bilden blir tydligare och du kan fatta mer detaljerad. Nu kan du titta på de tre hotell på den vänstra banken. Du kan se funktionerna i förgrunden. Du kan även välja att stanna kvar på den tredje våningen på grund av vyn.

Med data som är relevant, ansluten, korrekt och tillräckligt, du har alla komponenter krävs för att göra vissa datavetenskap med hög kvalitet.

Se till att Kolla in andra fyra videor i *datavetenskap för nybörjare* från Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Nästa steg
* [Prova en första dataexperiment med Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](what-is-machine-learning.md)
