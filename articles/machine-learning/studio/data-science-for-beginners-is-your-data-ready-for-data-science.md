---
title: "Är dina data klara för datavetenskap? -Utvärdering av Azure Machine Learning | Microsoft Docs"
description: "Fyra villkor som måste uppfylla för att vara klar för datavetenskap. Den här videon har konkreta exempel för att hjälpa dig med grundläggande data utvärdering."
keywords: "relevanta data utvärdera data, förbereder data, Datakriterier, data som är redo"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: d502062c-da70-4b21-9054-0bfd9902612e
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2018
ms.author: cgronlun
ms.openlocfilehash: 4ab9462c4cc4573717450ce48028807960cecee9
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2018
---
# <a name="is-your-data-ready-for-data-science"></a>Är dina data klara för datavetenskap?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Datavetenskap för nybörjare serien
Lär dig att utvärdera dina data att kontrollera att den uppfyller grundläggande kriterier för att vara klar för datavetenskap.

Titta på alla för att få ut mesta möjliga av serien. [Gå till listan över videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Datavetenskap för nybörjare* är en snabb introduktion till datavetenskap i fem kort video.

* Video 1: [5 frågor datavetenskap svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min. 14 sek)*
* Video 2: Är redo för datavetenskap dina data?
* Video 3: [Ställ en fråga som du kan svara med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min. 42 sek)*
* Video 5: [kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Betyg: Är redo för datavetenskap dina data?
Välkommen till ”är dina data redo för datavetenskap”? andra videon i serien *datavetenskap för nybörjare*.  

Innan datavetenskap kan ge dig svaren som du vill ha så att det vissa hög kvalitet raw material att arbeta med. Precis som upprättar en pizza, desto bättre beståndsdelarna du börjar med bättre färdiga produkten. 

## <a name="criteria-for-data"></a>Kriterier för data
Så om datavetenskap finns det vissa komponenter som behövs för att ihop.

Vi behöver data som är:

* Relevanta
* Ansluten
* Korrekt
* Tillräckligt för att arbeta med

## <a name="is-your-data-relevant"></a>Gäller dina data?
Den första komponenten - måste data som är relevanta.

![Relevanta data kontra irrelevanta data - utvärdera data](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Titta på tabellen till vänster. Vi uppfyllda sju personer utanför Boston staplar, mätt blod alkohol nivån, Red Sox batting medelvärdet för de senaste spelet och pris av i arkivet närmaste bekvämlighet.

Det här är alla perfekt legitima data. Endast fel är att den inte är relevanta. Det finns ingen uppenbara relation mellan dessa siffror. Om jag gav mjölk och rött Sox batting medelvärdet aktuella pris, går det inte kunde du tror min blod alkoholhalt.

Nu vill titta på tabellen till höger. Den här tiden mäts vi varje person body massa och räknas antalet drycker som de har haft.  Talen i varje rad är nu relevanta till varandra. Om jag gav min brödtext massa och antalet Margaritas som jag har haft du göra en uppskattning av min blod alkohol innehåll.

## <a name="do-you-have-connected-data"></a>Du har anslutit data?
Nästa komponenten är anslutna data.

![Ansluten data kontra frånkopplade data - Datakriterier data är klar](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Här är några relevanta data för hamburgers kvalitet: grill temperatur, patty vikt och betyg i den lokala mat magazine. Men hittar luckor i tabellen till vänster.

De flesta datauppsättningar saknas vissa värden. Det är vanligt att ha hål så här och det finns olika sätt att komma runt dem. Men om det finns för mycket saknas, dina data börjar likna ost och Schweiz.

Om du tittar på tabellen till vänster, finns det så mycket data som saknas, är det svårt att få fram alla typer av relationen mellan galler temperatur- och patty vikt. Detta är ett exempel på frånkopplade data.

I högra tabellen men är full och slutföra – ett exempel på anslutna data.

## <a name="is-your-data-accurate"></a>Stämmer din data?
Nästa komponenten vi behöver är korrekta. Här är fyra mål som vi vill att träffa med pilarna.

![Korrekta data kontra felaktiga data - data villkor](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Titta på målet i det övre högra hörnet. Vi har en nära gruppering höger runt tjurar ögat. Naturligtvis är korrekt. Oväntat, datavetenskap språk, vår prestanda till målet höger under det anses också vara korrekt.

Om du har att kartlägga mittpunkt pilarna visas att det är mycket nära tjurar öga. Pilarna sprids ut alla runt mål, så att de ska anses vara inexakt, men de är uppbyggd kring ögat tjurar så att de ska anses vara korrekt.

Nu titta på det övre vänstra målet. Vår pilarna nådde här mycket nära varandra en nära gruppering. De är exakt, men de är felaktigt eftersom mitt går ut tjurar öga. Och naturligtvis pilarna i det nedre vänstra målet är både stämmer och inexakt. Den här archer måste flera praxis.

## <a name="do-you-have-enough-data-to-work-with"></a>Har du tillräckligt med data att arbeta med?
Slutligen beståndsdel #4 – vi måste ha tillräckligt med data.

![Har du tillräckligt med data för analys? Utvärdering av](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Tänk på att varje datapunkt i tabellen som en pensellinje i en återgivningen. Om du har några av dem återgivningen kan vara ganska fuzzy - det är svårt att se vad det är.

Om du lägger till vissa mer penseldrag startar din återgivnings lite frågesporten.

När du har knappt tillräckligt med linjer, ser du att se vissa bred beslut. Är det någon plats kanske du vill besöka? Det ser ut klara, som ser ut som ren vattenstämplar – Ja, som är där vi på semester.

När du lägger till mer data bilden blir tydligare och du kan göra mer detaljerad beslut. Nu kan jag tittar på tre hotell på den vänstra banken. Du vet jag gillar funktionerna arkitekturen för den i förgrunden. Jag kommer förblir det, på den tredje våningen.

Data som är relevanta, ansluten, korrekt, och inte tillräckligt har alla komponenter som vi behöver göra vissa högkvalitativ datavetenskap.

Se till att checka ut de fyra videor som i *datavetenskap för nybörjare* från Microsoft Azure Machine Learning.

## <a name="next-steps"></a>Nästa steg
* [Försök med ett första datavetenskap experiment i Machine Learning Studio](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](what-is-machine-learning.md)
