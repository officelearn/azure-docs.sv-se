---
title: Använda linjär Regression i Machine Learning | Microsoft Docs
description: En jämförelse av linjära regressionsmodeller i Excel och Azure Machine Learning Studio
metakeywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: 417ae6ab-de4f-4bdd-957a-d96133234656
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 0e7004cca1d64270a2b48ea1e41c74b3e7555317
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823783"
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Använda linjär regression i Azure Machine Learning
> *Kate Baroni* och *Ben Boatman* är enterprise lösningsarkitekter i Microsoft Data Insights Center sökfunktion. I den här artikeln beskrivs de upplevelsen migrera en befintlig regression analysis suite till en molnbaserad lösning som använder Azure Machine Learning. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Mål
Vår projektet startades med två mål i åtanke: 

1. Använda förutsägande analys för att förbättra vår organisation månatliga intäkter projektioner 
2. Använd Azure Machine Learning för att bekräfta, optimera, öka hastighet, och skala för våra resultat. 

Vår organisation går igenom en månatlig intäkter prognoser processen som många företag. Vårt små team med affärsanalytiker har har behörighet att använda Azure Machine Learning för att stödja processen och förbättra prognosens noggrannhet. Teamet har använt några månader samla in data från flera källor och som kör dataattribut via statistiska analyser, identifiera viktiga punkter som är relevanta för försäljningsprognoser för tjänster. Nästa steg var att börja skapa prototyper statistiska regressionsmodeller på data i Excel. Vi hade en Excel-regressionsmodell som vinner den aktuella fält och ekonomi prognoser processer inom ett par veckor. Det blev baslinje förutsägelse resultatet. 

Sedan tog vi nästa steg för att flytta vår förutsägande analys till Azure Machine Learning för att ta reda på hur Maskininlärning kan förbättra på förutsägande prestanda.

## <a name="achieving-predictive-performance-parity"></a>Uppnå förutsägande prestanda paritet
Vårt första prioritet var att uppnå paritet mellan regressionsmodeller Machine Learning och Excel. Baserat på samma data och samma delning för träning och testning av data, som vi ville få förutsägande prestanda paritet mellan Excel och Machine Learning. Inledningsvis misslyckades vi. Excel-datamodellen – bättre än Machine Learning-modell. Felet uppstod på grund av brist på förståelse för grundläggande verktygsinställningen i Machine Learning. Efter en synkronisering med Machine Learning-produktteamet vi fick en bättre förståelse för de grundläggande inställning krävs för våra data, och vi uppnådde paritet mellan de två modellerna. 

### <a name="create-regression-model-in-excel"></a>Skapa regression-modell i Excel
Vår Excel-Regression används standard linjär regressionsmodell hittades i Excel-analysverktygens. 

Vi beräknas *Mean absolut % Error* och använda den som prestandamått för modellen. Det tog tre månader att komma till en aktiv-modell i Excel. Vi tog mycket av utbildning i Machine Learning Studio-experiment som slutligen var fördelaktigt om kraven.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Skapa jämförbara experiment i Azure Machine Learning
Vi har följt stegen nedan för att skapa vårt experiment i Machine Learning Studio: 

1. Överfört datauppsättningen som en csv-fil till Machine Learning Studio (liten fil)
2. Skapa ett nytt experiment och används den [Välj kolumner i datauppsättning] [ select-columns] modul för att välja samma datafunktioner som används i Excel 
3. Används den [dela Data] [ split] modulen (med *relativa uttryck* läge) att dela upp data i samma datauppsättningar för utbildning som hade gjorts i Excel 
4. Redan experimenterat lite med den [linjär Regression] [ linear-regression] modulen (endast standardalternativ), dokumenterade och jämfört med resultatet till vår regressionsmodell för Excel

### <a name="review-initial-results"></a>Granska resultatet från den inledande
Först Excel-datamodellen tydligt – bättre än Machine Learning Studio-modell: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Prestanda | | |
| <ul style="list-style-type: none;"><li>Justera R-ruta</li></ul> |0.96 |Gäller inte |
| <ul style="list-style-type: none;"><li>Koefficienten för <br />Har gjorts</li></ul> |Gäller inte |0.78<br />(låg precision) |
| Medelabsolutfel |$9. 5M |$ 19.4 M |
| Medelabsolutfel (%) |6.03% |12.2% |

När vi körde våra processen och resultat av utvecklare och datavetare på Machine Learning-teamet tillhandahålla de snabbt några användbara tips. 

* När du använder den [linjär Regression] [ linear-regression] modul i Machine Learning Studio finns två metoder:
  * Online toning Lutningsmetoden: Kanske passar bättre för större skala problem
  * Vanlig minsta kvadrat: Det här är den metod som de flesta tänker på när de hör linjär regression. Vanlig minsta kvadrat kan vara mer föredra för små datauppsättningar.
* Överväg att justera parametern L2 Regularisering vikt för att förbättra prestanda. Den är inställd på 0,001 som standard, men för vårt liten datamängd vi ställa in det på 0,005 att förbättra prestanda. 

### <a name="mystery-solved"></a>Ta mysteriet löst!
När vi tillämpade rekommendationerna uppnått vi samma baslinje-prestanda i Machine Learning Studio som med Excel: 

|  | Excel | Studio (första) | Studio med minsta kvadrat |
| --- |:---:|:---:|:---:|
| Taggade värde |Faktiska (numeriska) |Samma |Samma |
| Learner |Excel -> Data Analysis -> Regression |Linjär Regression. |Linjär Regression |
| Learner alternativ |Gäller inte |Standardvärden |vanlig minsta kvadrat<br />L2 = 0,005 |
| Datauppsättning |26 raderna, 3 funktioner, 1 etikett. Alla numeriska. |Samma |Samma |
| Dela: träna |Excel tränats på först 18 rader, testas och de senaste 8 raderna. |Samma |Samma |
| Dela: Test |Excel-regressionsformeln som tillämpas på de senaste 8 raderna |Samma |Samma |
| **Prestanda** | | | |
| Justera R-ruta |0.96 |Gäller inte | |
| Bestämningskoefficient |Gäller inte |0.78 |0.952049 |
| Medelabsolutfel |$9. 5M |$ 19.4 M |$9. 5M |
| Medelabsolutfel (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Dessutom Excel koefficienter jämfört med bra funktionen vikter i Azure-tränade modellen:

|  | Excel koefficienter | Funktionen Azure vikterna |
| --- |:---:|:---:|
| Skärningspunkt/Bias |19470209.88 |19328500 |
| Funktion A |0.832653063 |0.834156 |
| Funktion B |11071967.08 |11007300 |
| Funktion C |25383318.09 |25140800 |

## <a name="next-steps"></a>Nästa steg
Vi ville använda Machine Learning-webbtjänst i Excel. Vår affärsanalytiker förlitar sig på Excel och vi behövde ett sätt att anropa Machine Learning-webbtjänst med en rad med Excel-data och den returnerar förutsägelsevärdet till Excel. 

Ville vi även optimera vår modell med hjälp av alternativen och algoritmer som finns i Machine Learning Studio.

### <a name="integration-with-excel"></a>Integrering med Excel
Vår lösning var att operationalisera vår Machine Learning regression-modellen genom att skapa en webbtjänst från den tränade modellen. Webbtjänsten har skapats inom ett par minuter, och vi kan kalla den direkt från Excel för att returnera ett värde för förväntade intäkter. 

Den *Web Services-instrumentpanel* avsnittet innehåller en nedladdningsbar Excel-arbetsbok. Arbetsboken innehåller redan formaterad web API- och schemareferenser information om tjänstens inbäddade. När du klickar på *ladda ned Excel-arbetsbok*, arbetsboken öppnas och du kan spara den till din lokala dator. 

![][1]

Kopiera dina fördefinierade parametrar till blå parameteravsnittet med öppna arbetsboken som visas nedan. När parametrarna har angetts Excel som anropar till Machine Learning-webbtjänsten och förväntade poängsatta etiketter visas i avsnittet grön förutsagda värden. Arbetsboken kommer att fortsätta att skapa förutsägelser för parametrar som baserat på din tränade modellen för alla radobjekt som angetts under parametrar. Mer information om hur du använder den här funktionen finns i [förbrukar en Azure Machine Learning-webbtjänst från Excel](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optimering och ytterligare experiment
Nu när vi har haft en baslinje med vår Excel-datamodellen, flyttas vi vidare att optimera våra linjär regressionsmodell för Maskininlärning. Vi använde modulen [Filter-baserade Funktionsurval] [ filter-based-feature-selection] för att förbättra på vår valet av ursprungliga data element och det hjälpt oss att uppnå en förbättring av prestanda för 4.6% innebär absoluta fel. Vi använder den här funktionen som kunde vi sparar veckor i går igenom dataattribut att hitta rätt uppsättning funktioner för modellering för framtida projekt. 

Sedan vi planerar att inkludera ytterligare algoritmer som [Bayesian] [ bayesian-linear-regression] eller [förstärkta beslutsträd] [ boosted-decision-tree-regression] i vår experiment att jämföra prestanda. 

Om du vill experimentera med regression är en bra datauppsättning för att testa exempeldatauppsättningen energi effektivitet Regression som har många olika numeriska attribut. Datauppsättningen tillhandahålls som en del av exemplen på datauppsättningar i Machine Learning Studio. Du kan använda en mängd olika inlärningsmoduler för att förutse uppvärmning belastning eller kylning belastningen. Diagrammet nedan är en prestanda-jämförelse av olika regression lär sig mot den energieffektivitet datauppsättning att förutsäga för en målvariabel kylning belastning: 

| Modell | Medelabsolutfel | Rot medelvärdet cirkels fel | Relativa absoluta fel | Relativ cirkels fel | Bestämningskoefficient |
| --- | --- | --- | --- | --- | --- |
| Beslutsträd |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Linjär Regression (brantaste Lutningsmetoden) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Neuralt nätverk Regression |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Linjär Regression (vanlig minsta kvadrat) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Viktiga Takeaways
Vi lärt oss mycket av från körs Excel regression och Azure Machine Learning-experiment parallellt. Skapa baslinje i Excel och jämföra dem med modeller med Machine Learning [linjär Regression] [ linear-regression] hjälpte oss Lär dig Azure Machine Learning och vi har hittat möjligheter att förbättra data prestanda för val av och modell. 

Vi hittade också att det är lämpligt att använda [Filter-baserade Funktionsurval] [ filter-based-feature-selection] att påskynda framtida förutsägelse projekt. Genom att använda val av funktioner för dina data, kan du skapa en förbättrad modell i Machine Learning med bättre prestanda. 

Möjlighet att överföra förutsägande analys prognoser från Machine Learning till Excel hela organismen tillåter en kraftig ökning i möjligheten att tillhandahålla har resultaten till en bred företag målgrupp. 

## <a name="resources"></a>Resurser
Här är några resurser som hjälper dig att arbeta med regression: 

* Regression i Excel. Om du aldrig har försökt regression i Excel, gör du enklare med hjälp av den här självstudien: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regression jämfört med prognoser Tyler Chessman skrev en bloggartikel som förklarar hur du time series prognoser i Excel, som innehåller en bra nybörjare beskrivning av linjär regression. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
* Vanlig minst linjär Regression: Fel, problem och fallgropar. En introduktion och en beskrivning av Regression: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

