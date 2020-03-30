---
title: Migrerar analyser från Excel
titleSuffix: ML Studio (classic) - Azure
description: En jämförelse av linjära regressionsmodeller i Excel och i Azure Machine Learning Studio (klassisk)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 85bae9bfc10460b51935c6eb1e14e3a3dd816a8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217804"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrera analyser från Excel till Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> *Kate Baroni* och *Ben Boatman* är företagslösningsarkitekter i Microsofts Data Insights Center of Excellence. I den här artikeln beskriver de sin upplevelse att migrera en befintlig regressionsanalyssvit till en molnbaserad lösning med Azure Machine Learning Studio (klassisk).

## <a name="goal"></a>Mål

Vårt projekt inleddes med två mål i åtanke: 

1. Använd prediktiva analyser för att förbättra noggrannheten i vår organisations månatliga intäktsprognoser 
2. Använd Azure Machine Learning Studio (klassisk) för att bekräfta, optimera, öka hastigheten och omfattningen av våra resultat. 

Liksom många företag går vår organisation igenom en månatlig intäktsprognosprocess. Vårt lilla team av affärsanalytiker fick i uppdrag att använda Azure Machine Learning Studio (klassiskt) för att stödja processen och förbättra prognosnoggrannheten. Teamet tillbringade flera månader med att samla in data från flera källor och köra dataattributen genom statistisk analys som identifierar viktiga attribut som är relevanta för försäljningsprognoser för tjänster. Nästa steg var att börja prototyper statistiska regressionsmodeller på data i Excel. Inom några veckor hade vi en Excel-regressionsmodell som överträffade de aktuella fält- och ekonomiprognosprocesserna. Detta blev baslinje förutsägelse resultatet. 

Vi tog sedan nästa steg för att flytta vår prediktiva analys över till Studio (klassisk) för att ta reda på hur Studio (klassisk) kan förbättra prediktiva prestanda.

## <a name="achieving-predictive-performance-parity"></a>Uppnå prediktiv prestandaparitet
Vår första prioritet var att uppnå paritet mellan Studio (klassiska) och Excel regression modeller. Med tanke på samma data, och samma uppdelning för tränings- och testdata, ville vi uppnå prediktiv prestandaparitet mellan Excel och Studio (klassisk). Inledningsvis misslyckades vi. Excel-modellen överträffade Studio (klassisk) modell. Felet berodde på en brist på förståelse för basverktygsinställningen i Studio (klassisk). Efter en synkronisering med Studio (klassiska) produktteam, fick vi en bättre förståelse för den basinställning som krävs för våra datauppsättningar, och uppnått paritet mellan de två modellerna. 

### <a name="create-regression-model-in-excel"></a>Skapa regressionsmodell i Excel
Vår Excel-regression använde den linjära standardregressionsmodellen som finns i Excel Analysis ToolPak. 

Vi beräknade *Medel absolut % fel* och använde det som prestandamått för modellen. Det tog 3 månader att komma fram till en arbetsmodell med Excel. Vi tog mycket av lärandet i Studio (klassiska) experiment som i slutändan var fördelaktigt att förstå krav.

### <a name="create-comparable-experiment-in-studio-classic"></a>Skapa jämförbara experiment i Studio (klassiskt)
Vi följde dessa steg för att skapa vårt experiment i Studio (klassisk): 

1. Laddade upp datauppsättningen som en CSV-fil till Studio (klassisk) (mycket liten fil)
2. Skapade ett nytt experiment och använde modulen [Välj kolumner i datauppsättning][select-columns] för att välja samma datafunktioner som används i Excel 
3. Använde modulen [Dela data][split] (med *relativt uttryck)* för att dela upp data i samma träningsdatauppsättningar som hade gjorts i Excel 
4. Experimenterade med den [linjära regressionsmodulen][linear-regression] (endast standardalternativ), dokumenterade och jämförde resultaten med vår Excel-regressionsmodell

### <a name="review-initial-results"></a>Granska de första resultaten
Till en början överträffade Excel-modellen tydligt Studio-modellen (klassisk) : 

|  | Excel | Studio (klassisk) |
| --- |:---:|:---:|
| Prestanda | | |
| <ul style="list-style-type: none;"><li>Justerad R-kvadrat</li></ul> |0.96 |Ej tillämpligt |
| <ul style="list-style-type: none;"><li>Koefficienten för <br />Bestämning</li></ul> |Ej tillämpligt |0,78<br />(låg noggrannhet) |
| Genomsnittligt absolut fel |$9.5M $9.5M $9.5M $ |$ 19.4M |
| Genomsnittligt absolut fel (%) |6.03% |12.2% |

När vi körde vår process och resultat av utvecklare och dataforskare i Machine Learning-teamet gav de snabbt några användbara tips. 

* När du använder modulen [Linjär regression][linear-regression] i Studio (klassisk) tillhandahålls två metoder:
  * Online Gradient Descent: Kan vara mer lämpade för större problem
  * Vanliga minsta kvadrater: Detta är den metod som de flesta människor tänker på när de hör linjär regression. För små datamängder kan vanliga minsta kvadrater vara ett mer optimalt val.
* Överväg att justera parametern L2 Regularization Weight för att förbättra prestanda. Den är inställd på 0,001 som standard, men för vår lilla datauppsättning ställer vi in den på 0,005 för att förbättra prestanda. 

### <a name="mystery-solved"></a>Mysteriet löst!
När vi tillämpade rekommendationerna uppnådde vi samma baslinjeprestanda i Studio (klassisk) som med Excel: 

|  | Excel | Studio (klassisk) (Initial) | Studio (klassisk) w / Minsta Rutor |
| --- |:---:|:---:|:---:|
| Märkt värde |Verkliga värden (numeriska) |Samma |Samma |
| Eleven |Excel -> dataanalys -> regression |Linjär regression. |Linjär regression |
| Alternativ för elev |Ej tillämpligt |Standardvärden |vanliga minsta kvadrater<br />L2 = 0,005 |
| Datauppsättning |26 rader, 3 funktioner, 1 etikett. Alla numeriska. |Samma |Samma |
| Split: Tåg |Excel tränas på de första 18 raderna, testade på de senaste 8 raderna. |Samma |Samma |
| Dela: Test |Excel regressionsformel som tillämpas på de senaste 8 raderna |Samma |Samma |
| **Prestanda** | | | |
| Justerad R-kvadrat |0.96 |Ej tillämpligt | |
| Bestämningskoefficient |Ej tillämpligt |0,78 |0.952049 |
| Genomsnittligt absolut fel |$9.5M $9.5M $9.5M $ |$ 19.4M |$9.5M $9.5M $9.5M $ |
| Genomsnittligt absolut fel (%) |<span style="background-color: 00FF00;">6.03%</span> |12.2% |<span style="background-color: 00FF00;">6.03%</span> |

Dessutom jämförde Excel-koefficienterna väl med funktionsvikterna i den Azure-tränade modellen:

|  | Excel-koefficienter | Azure-funktionsvikter |
| --- |:---:|:---:|
| Avlyssna/partiskhet |19470209.88 |19328500 |
| Funktion A |0.832653063 |0.834156 |
| Funktion B |11071967.08 |11007300 |
| Funktion C |25383318.09 |25140800 |

## <a name="next-steps"></a>Efterföljande moment
Vi ville använda machine learning-webbtjänsten i Excel. Våra affärsanalytiker förlitar sig på Excel och vi behövde ett sätt att ringa machine learning-webbtjänsten med en rad Excel-data och få den att returnera det förväntade värdet till Excel. 

Vi ville också optimera vår modell, med hjälp av de alternativ och algoritmer som finns i Studio (klassisk).

### <a name="integration-with-excel"></a>Integrering med Excel
Vår lösning var att operationalisera vår Machine Learning regression modell genom att skapa en webbtjänst från den utbildade modellen. Inom några minuter skapades webbtjänsten och vi kan kalla den direkt från Excel för att returnera ett förväntat intäktsvärde. 

Avsnittet *Instrumentpanel för webbtjänster* innehåller en nedladdningsbar Excel-arbetsbok. Arbetsboken är förformaterad med webbtjänstens API och schemainformation inbäddad. När du klickar på *Hämta Excel-arbetsbok*öppnas arbetsboken och du kan spara den på den lokala datorn. 

![Hämta Excel-arbetsbok från instrumentpanelen för webbtjänster](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

När arbetsboken är öppen kopierar du de fördefinierade parametrarna till det blå parameteravsnittet enligt nedan. När parametrarna har angetts ropar Excel ut till machine learning-webbtjänsten och de förväntade poängsatta etiketterna visas i avsnittet Gröna förutsagda värden. Arbetsboken fortsätter att skapa förutsägelser för parametrar baserat på din tränade modell för alla radobjekt som anges under Parametrar. Mer information om hur du använder den här funktionen finns i [Använda en Azure Machine Learning Web Service från Excel](consuming-from-excel.md). 

![Mall Excel-arbetsbok som ansluter till den distribuerade webbtjänsten](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimering och ytterligare experiment
Nu när vi hade en baslinje med vår Excel-modell gick vi vidare för att optimera vår Machine Learning Linear Regression Model. Vi använde modulen [Filter-Based Feature Selection][filter-based-feature-selection] för att förbättra vårt urval av initiala dataelement och det hjälpte oss att uppnå en prestandaförbättring på 4,6% Mean Absolute Error. För framtida projekt kommer vi att använda den här funktionen som kan spara oss veckor i iterering genom dataattribut för att hitta rätt uppsättning funktioner som ska användas för modellering. 

Därefter planerar vi att inkludera ytterligare algoritmer som [Bayesianska][bayesian-linear-regression] eller [boostade beslutsträd][boosted-decision-tree-regression] i vårt experiment för att jämföra prestanda. 

Om du vill experimentera med regression är en bra datauppsättning för att prova exempeldatauppsättningen För energieffektivitetsregression, som har många numeriska attribut. Datauppsättningen tillhandahålls som en del av exempeldatauppsättningarna i Studio (klassisk). Du kan använda en mängd olika utbildningsmoduler för att förutsäga antingen uppvärmningsbelastning eller kylbelastning. Diagrammet nedan är en prestandajämförelse av olika regression lär sig mot energieffektivitetsdatauppsättningen som förutspår för målvariabeln Kylbelastning: 

| Modell | Genomsnittligt absolut fel | Rotmedelstvadratfel | Relativt absolut fel | Relativt fyrkantigt fel | Bestämningskoefficient |
| --- | --- | --- | --- | --- | --- |
| Förstärkt beslutsträd |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Linjär regression (gradientnedstigning) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regression för Neural Network |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Linjär regression (vanliga minsta kvadrater) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Viktiga takeaways
Vi lärde oss mycket genom att köra Excel regression och Studio (klassiska) experiment parallellt. Att skapa baslinjemodellen i Excel och jämföra den med modeller med Machine Learning [Linear Regression][linear-regression] hjälpte oss att lära oss Studio (klassisk), och vi upptäckte möjligheter att förbättra dataval och modellprestanda. 

Vi fann också att det är lämpligt att använda [filterbaserad funktionsval][filter-based-feature-selection] för att påskynda framtida förutsägelseprojekt. Genom att använda funktionsval på dina data kan du skapa en förbättrad modell i Studio (klassisk) med bättre övergripande prestanda. 

Möjligheten att överföra de prediktiva analytiska prognoserna från Studio (klassisk) till Excel tillåter en betydande ökning av möjligheten att framgångsrikt ge resultat till en bred företagsanvändarpublik. 

## <a name="resources"></a>Resurser
Här är några resurser som hjälper dig att arbeta med regression: 

* Regression i Excel. Om du aldrig har provat regression i Excel gör den här självstudien det enkelt:[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regression kontra prognoser. Tyler Chessman skrev en bloggartikel som förklarar hur man gör tidsserieprognoser i Excel, som innehåller en bra nybörjarbeskrivning av linjär regression. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Vanliga minsta kvadrater Linjär regression: Brister, problem och fallgropar. För en introduktion och diskussion av Regression: [ https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

