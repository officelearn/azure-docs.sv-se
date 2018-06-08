---
title: Linjär regression i Machine Learning | Microsoft Docs
description: En jämförelse av linjär regression modeller i Excel och i Azure Machine Learning Studio
metakeywords: ''
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
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
ms.openlocfilehash: 162fc96c44db3c92103e12922de14b543daec9bf
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836233"
---
# <a name="using-linear-regression-in-azure-machine-learning"></a>Använda linjär regression i Azure Machine Learning
> *Kate Baroni* och *Ben Boatman* är enterprise lösningsarkitekter i Microsofts Data insikter Center utmärkt. I den här artikeln beskriver de sina erfarenheter migrera en befintlig regression analys suite till en molnbaserad lösning med hjälp av Azure Machine Learning. 
> 
> 

&nbsp; 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="goal"></a>Mål
Projektet igång med två mål i åtanke: 

1. Använd förutsägelseanalys för att förbättra vår organisation månatliga intäkter projektioner 
2. Använda Azure Machine Learning bekräfta genom att optimera, öka hastighet och skala för våra resultat. 

Vår organisation genomgår en månatlig intäkter prognoser process som många företag. Vår små affärsanalytiker-teamet har gett med hjälp av Azure Machine Learning stöder processen och förbättra prognosens noggrannhet. Teamet har använt flera månader samla in data från flera källor och kör attributen data via statistiska analyser identifiera viktiga egenskaper som är relevanta för tjänster försäljningsprognoser. Nästa steg är att börja prototyper statistiska regression modeller på data i Excel. Inom några veckor hade vi en Excel-regressionsmodell som outperforming aktuella fältet och ekonomi prognoser processer. Detta blev resultatet baslinjen förutsägelse. 

Vi tog sedan nästa steg att flytta våra förutsägelseanalyser över till Azure Machine Learning ta reda på hur Maskininlärning kan förbättra förutsägbar prestanda.

## <a name="achieving-predictive-performance-parity"></a>Uppnå förutsägbar prestanda paritet
Vår högsta prioritet var att uppnå paritet mellan regression Machine Learning och Excel. Baserat på samma data och samma delning för träning och testning av data, som vi ville uppnå förutsägbar prestanda paritet mellan Excel och Maskininlärning. Först kunde inte. Excel-modellen gick bättre än förväntat Machine Learning-modellen. Felet berodde på grund av bristande förståelse för verktygsinställningen grundläggande i Machine Learning. Vi fick en bättre förståelse för inställningen för våra datauppsättningar efter en synkronisering med Produktteamet för Maskininlärning och uppnås paritet mellan de två modellerna. 

### <a name="create-regression-model-in-excel"></a>Skapa regressionsmodell i Excel
Vår Excel Regression används standard linjär regressionsmodell hittades i Excel analysverktygens. 

Det beräknade *medelvärde absolut % fel* och använda den som prestandamåttet för modellen. Det tog tre månader att få fram en fungerande modell i Excel. Vi har utvecklat mycket learning i Machine Learning Studio-experiment som slutligen var bra i Förstå kraven.

### <a name="create-comparable-experiment-in-azure-machine-learning"></a>Skapa jämförbara experiment i Azure Machine Learning
Vi har följt de här stegen för att skapa vårt experiment i Machine Learning Studio: 

1. Överförs datamängden som en csv-fil till Machine Learning Studio (liten fil)
2. Ett nytt experiment skapas och används i [Välj kolumner i datauppsättning] [ select-columns] modulen att välja samma datafunktioner som används i Excel 
3. Används av [dela Data] [ split] modul (med *relativa uttryck* läge) att dela data i samma utbildning datauppsättningar som har gjorts i Excel 
4. Redan experimenterat lite med den [linjär Regression] [ linear-regression] modul (standardalternativen endast), dokumenterade och jämförs resultatet till vår regressionsmodell för Excel

### <a name="review-initial-results"></a>Granska resultatet från första
Först Excel modellen tydligt gick bättre än förväntat Machine Learning Studio modellen: 

|  | Excel | Studio |
| --- |:---:|:---:|
| Prestanda | | |
| <ul style="list-style-type: none;"><li>Justeras R ruta</li></ul> |0.96 |Gäller inte |
| <ul style="list-style-type: none;"><li>Värde för <br />Bestämning</li></ul> |Gäller inte |0.78<br />(låg noggrannhet) |
| Medelabsolutfel |$9. 5M |$ 19.4 M |
| Medelabsolutfel (%) |6.03% |12.2% |

När vi körde våra processen och resultat av datavetare och utvecklare på Machine Learning-teamet angetts de snabbt några användbara tips. 

* När du använder den [linjär Regression] [ linear-regression] modul i Machine Learning Studio finns två metoder:
  * Online toningen härstammar: Kanske passar bättre för större skala problem
  * Vanlig minsta kvadrat: Det här är den metod som de flesta tänka på när de hör linjär regression. Vanlig minsta kvadrat kan vara mer föredra för små datauppsättningar.
* Överväg att modifiera parametern L2 Regularization vikt för att förbättra prestanda. Anges till 0,001 som standard, men för våra liten datamängd vi det att 0,005 att förbättra prestanda. 

### <a name="mystery-solved"></a>Okända löst!
När vi tillämpas rekommendationerna uppnås vi samma baslinje-prestanda i Machine Learning Studio som med Excel: 

|  | Excel | Studio (första) | Studio med minsta kvadrat |
| --- |:---:|:---:|:---:|
| Märkt värde |Verkliga (numeriskt) |samma |samma |
| Deltagaren |Excel -> Data Analysis -> Regression |Linjär Regression. |Linjär Regression |
| Deltagaren alternativ |Gäller inte |Standardvärden |vanlig minsta kvadrat<br />L2 = 0,005 |
| Datauppsättning |26 rader, 3 funktioner, 1 etikett. Alla numeriska. |samma |samma |
| Dela: Train |Excel tränats på först 18 rader, testas med de senaste 8 raderna. |samma |samma |
| Dela: Test |Excel-regressionsformeln som tillämpas på de senaste 8 raderna |samma |samma |
| **Prestanda** | | | |
| Justeras R ruta |0.96 |Gäller inte | |
| Bestämningskoefficienten |Gäller inte |0.78 |0.952049 |
| Medelabsolutfel |$9. 5M |$ 19.4 M |$9. 5M |
| Medelabsolutfel (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

Dessutom Excel koefficienter jämfört med bra funktionen vikterna i Azure tränade modellen:

|  | Excel koefficienter | Azure-funktionen vikterna |
| --- |:---:|:---:|
| Skärningspunkt/Bias |19470209.88 |19328500 |
| Funktion A |0.832653063 |0.834156 |
| Funktion B |11071967.08 |11007300 |
| Funktionen C |25383318.09 |25140800 |

## <a name="next-steps"></a>Nästa steg
Vi ville använda webbtjänsten för Machine Learning i Excel. Vår affärsanalytiker förlitar sig på Excel och vi behövs ett sätt att anropa webbtjänsten för Machine Learning med en rad med data i Excel och det förväntade returvärde till Excel. 

Vi ville också optimera vår modell med alternativ och algoritmer som är tillgängliga i Machine Learning Studio.

### <a name="integration-with-excel"></a>Integrering med Excel
Vår lösning var att operationalisera våra Machine Learning regressionsmodell genom att skapa en webbtjänst från den tränade modellen. Webbtjänsten har skapats och vi kan kalla den direkt från Excel för att returnera ett värde för beräknade intäkter inom några minuter. 

Den *Web Services Dashboard* avsnittet innehåller en nedladdningsbar Excel-arbetsbok. Arbetsboken innehåller före formaterad web API och schema tjänstinformation inbäddade. När du klickar på *hämta Excel-arbetsboken*, arbetsboken öppnas och du kan spara den till den lokala datorn. 

![][1]

Kopiera fördefinierade parametrarna i avsnittet blå parametern med öppna arbetsboken som visas nedan. När du har angett parametrarna Excel anropar till Machine Learning-webbtjänst och förväntade poängsatta etiketter visas i avsnittet grön förutsagda värden. Arbetsboken kommer fortsätta att skapa förutsägelser för parametrar utifrån din tränad modell för alla rad-element som anges under parametrar. Mer information om hur du använder den här funktionen finns [förbrukar en Azure Machine Learning-webbtjänst från Excel](consuming-from-excel.md). 

![][2]

### <a name="optimization-and-further-experiments"></a>Optimering och ytterligare experiment
Nu när vi hade en baslinje med vår modell i Excel flytta vi vidare att optimera våra linjär Regression Maskininlärningsmodell. Vi använde modulen [Filter-baserade Funktionsurval] [ filter-based-feature-selection] för att förbättra på vår urval av inledande data element och den hjälp oss att uppnå en prestandaförbättring 4.6% medelvärdet av absoluta fel. Vi använder den här funktionen som kan spara oss veckor i söka igenom dataattribut för att hitta rätt uppsättning funktioner för modellering för framtida projekt. 

Nästa vi planerar att inkludera ytterligare algoritmer som [Bayesian] [ bayesian-linear-regression] eller [Tvåklassförhöjt beslutsträd] [ boosted-decision-tree-regression] i vårt experiment att jämföra prestanda. 

Om du vill experimentera med regression är en bra dataset försöka exempeldatamängd energi effektivitet Regression som har många numeriska attribut. Datamängden har angetts som en del av provdatauppsättningar i Machine Learning Studio. Du kan använda olika learning moduler för att förutsäga uppvärmning belastning eller kylning belastning. Diagrammet nedan är en prestanda jämförelse av olika regression lär sig mot den energieffektivitet dataset förutsäga för målvariabel kylning belastningen: 

| Modell | Medelabsolutfel | Roten medelvärdet kvadrat fel | Relativa absoluta fel | Relativ kvadrat fel | Bestämningskoefficienten |
| --- | --- | --- | --- | --- | --- |
| Tvåklassförhöjda beslutsträdet |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Linjär Regression (toning härstammar) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Neural Network Regression |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Linjär Regression (vanlig minsta kvadrat) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Viktiga Takeaways
Vi lärt dig mycket av körs Excel regression och Azure Machine Learning-experimenten parallellt. Skapa baslinje i Excel och jämföra dem med modeller med hjälp av Machine Learning [linjär Regression] [ linear-regression] hjälpte oss Läs Azure Machine Learning och vi identifierade möjligheter att optimera prestanda för data-markering och modell. 

Det finns också att det är lämpligt att använda [Filter-baserade Funktionsurval] [ filter-based-feature-selection] som påskyndar framtida förutsägelse projekt. Genom att tillämpa val av funktioner på dina data, kan du skapa en förbättrad modell i Machine Learning med bättre prestanda. 

Möjligheten att överföra den förutsägande analytiska prognoser från Machine Learning till Excel hela organismen tillåter mycket större i möjligheten att erbjuda har resultaten till en bred business målgrupp. 

## <a name="resources"></a>Resurser
Här följer några resurser för att hjälpa dig att arbeta med regression: 

* Regression i Excel. Om du aldrig har försökt regression i Excel, gör du enkelt med hjälp av den här kursen: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regression vs prognoser. Tyler Chessman skrev en bloggartikel som förklarar hur du time series Prognosticering i Excel, som innehåller en bra nybörjare beskrivning av linjär regression. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts) 
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

