---
title: Utvärdering av uppgifter
titleSuffix: ML Studio (classic) - Azure
description: Fyra kriterier som dina data måste uppfylla för att vara redo för datavetenskap. Denna video har konkreta exempel för att hjälpa till med grundläggande datautvärdering.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ccc422dfb3105fd1e12569a84a4ebfd22182b225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837807"
---
# <a name="is-your-data-ready-for-data-science"></a>Är dina data klara för datavetenskap?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: Data Science för nybörjare serien
Lär dig hur du utvärderar dina data för att se till att de uppfyller grundläggande kriterier för att vara redo för datavetenskap.

För att få ut det mesta av serien, titta på dem alla. [Gå till listan med videoklipp](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i denna serie
*Data Science for Beginners* är en snabb introduktion till datavetenskap i fem korta videor.

* Video 1: [De 5 frågorna datavetenskap svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 sek)*
* Video 2: Är dina data redo för datavetenskap?
* Video 3: [Ställ en fråga som du kan svara på med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 sek)*
* Video 4: [Förutsäg ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 sek)*
* Video 5: [Kopiera andras arbete för att göra datavetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 sek)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Avskrift: Är dina data redo för datavetenskap?
Välkommen till "Är dina data redo för datavetenskap?" den andra videon i serien *Data Science för nybörjare*.  

Innan datavetenskap kan ge dig de svar du vill ha, måste du ge det några högkvalitativa råvaror att arbeta med. Precis som att göra en pizza, desto bättre ingredienser du börjar med, desto bättre slutprodukt. 

## <a name="criteria-for-data"></a>Kriterier för data
Inom datavetenskap finns det vissa ingredienser som måste dras samman, inklusive:

* Relevanta
* Ansluten
* Korrekt
* Tillräckligt för att arbeta med

## <a name="is-your-data-relevant"></a>Är dina uppgifter relevanta?
Så den första ingrediensen - du behöver data som är relevanta.

![Relevanta data kontra irrelevanta data - utvärdera data](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Till vänster, presenterar bordet alkoholhalten i blodet på sju personer testas utanför en Boston bar, Red Sox vadd genomsnitt i deras senaste spel, och priset på mjölk i närmaste närbutik.

Allt detta är helt legitima uppgifter. Det är enda felet är att det inte är relevant. Det finns inget uppenbart samband mellan dessa siffror. Om någon gav dig det aktuella priset på mjölk och Red Sox vadd genomsnitt, det finns inget sätt du kan gissa deras alkoholhalt i blodet.

Titta nu på bordet till höger. Den här gången varje persons kroppsmassa mättes liksom antalet drycker de har haft.  Siffrorna i varje rad är nu relevanta för varandra. Om jag gav dig min kroppsmassa och antalet Margaritas jag har haft, kan du gissa på min alkoholhalt i blodet.

## <a name="do-you-have-connected-data"></a>Har du anslutna data?
Nästa ingrediens är anslutna data.

![Anslutna data kontra frånkopplade data - datakriterier, data redo](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Här är några relevanta uppgifter om kvaliteten på hamburgare: grill temperatur, patty vikt, och betyg i den lokala mattidningen. Men lägg märke till luckorna i tabellen till vänster.

De flesta datauppsättningar saknar vissa värden. Det är vanligt att ha hål som detta och det finns sätt att komma runt dem. Men om det saknas för mycket, börjar dina data att se ut som schweizerost.

Om man tittar på bordet till vänster, det finns så mycket saknade data, det är svårt att komma med någon form av relation mellan grill temperatur och patty vikt. I det här exemplet visas frånkopplade data.

Tabellen till höger är dock full och fullständig - ett exempel på anslutna data.

## <a name="is-your-data-accurate"></a>Är dina uppgifter korrekta?
Nästa ingrediens är noggrannhet. Här är fyra mål att träffa.

![Korrekta data kontra felaktiga data - datakriterier](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Titta på målet längst upp till höger. Det finns en tät gruppering runt tjurar ögat. Det är naturligtvis korrekt. Märkligt nog, på språket i datavetenskap, prestanda på målet precis under det anses också korrekt.

Om du kartlagt mitten av dessa pilar, skulle du se att det är mycket nära tjurar ögat. Pilarna är utspridda runt målet, så de anses oprecisa, men de är centrerade runt tjurar ögat, så de anses vara korrekta.

Titta nu på det övre vänstra målet. Här pilarna slog mycket nära varandra, en tät gruppering. De är exakta, men de är felaktiga eftersom centrum är långt borta från tjurar ögat. Pilarna i det nedre vänstra målet är både felaktiga och oprecisa. Bågskytten behöver mer övning.

## <a name="do-you-have-enough-data-to-work-with"></a>Har du tillräckligt med data att arbeta med?
Slutligen är ingrediens #4 tillräckligt med data.

![Har du tillräckligt med data för analys? Utvärdering av uppgifter](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Tänk på varje datapunkt i tabellen som ett penseldrag i en målning. Om du bara har några av dem, kan målningen vara suddig - det är svårt att säga vad det är.

Om du lägger till några fler penseldrag, då din målning börjar bli lite skarpare.

När du har knappt tillräckligt med slag, ser du bara tillräckligt för att göra några breda beslut. Är det någonstans jag kanske vill besöka? Det ser ljust ut, det ser ut som rent vatten - ja, det är där jag ska på semester.

När du lägger till mer data blir bilden tydligare och du kan fatta mer detaljerade beslut. Nu kan du titta på de tre hotellen på den vänstra stranden. Du kan märka de arkitektoniska egenskaperna hos den i förgrunden. Du kan även välja att stanna på tredje våningen på grund av utsikten.

Med data som är relevanta, anslutna, korrekta och tillräckligt har du alla ingredienser som behövs för att göra en del högkvalitativ datavetenskap.

Var noga med att kolla in de andra fyra videorna i *Data Science for Beginners* från Microsoft Azure Machine Learning Studio (klassisk).

## <a name="next-steps"></a>Nästa steg
* [Prova ett första datavetenskapsexperiment med Machine Learning Studio (klassiskt)](create-experiment.md)
* [Få en introduktion till Maskininlärning på Microsoft Azure](/azure/machine-learning/preview/overview-what-is-azure-ml)
