---
title: Migrera analyser från Excel
titleSuffix: ML Studio (classic) - Azure
description: En jämförelse av linjära Regressions modeller i Excel och i Azure Machine Learning Studio (klassisk)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 6976f0bb671b94f71b71287483c2ab88d0959899
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152779"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio-classic"></a>Migrera analyser från Excel till Azure Machine Learning Studio (klassisk)

> *Kate Baroni* och *ben Båtman* är Enterprise Solution Architects i Microsofts Expert Center för data insikter. I den här artikeln beskriver de deras erfarenhet av att migrera en befintlig Regressions analys till en molnbaserad lösning med hjälp av Azure Machine Learning Studio (klassisk).

## <a name="goal"></a>Mål

Vårt projekt startade med två mål i åtanke: 

1. Använd förutsägelse analys för att förbättra noggrannheten i vår organisations månatliga intäkts prognoser 
2. Använd Azure Machine Learning Studio (klassisk) för att bekräfta, optimera, öka hastigheten och skala för våra resultat. 

Precis som många företag går vår organisation igenom en prognos för månatlig intäkt. Vårt små team med affärsanalytiker har åtgärd ATS med att använda Azure Machine Learning Studio (klassisk) för att stödja processen och förbättra prognos noggrannheten. Teamet ägnade flera månader åt att samla in data från flera källor och köra dataattribut via statistisk analys som är relevanta för försäljnings prognoser för tjänster. Nästa steg var att börja prototyper av statistiska Regressions modeller för data i Excel. Inom några veckor hade vi en Excel-Regressions modell som utförde den aktuella fält-och finansierings prognos processen. Detta blev resultatet av bas linje förutsägelsen. 

Sedan vidtog vi nästa steg för att flytta vår förutsägelse analys till Studio (klassisk) för att ta reda på hur Studio (klassisk) kan förbättra förutsägande prestanda.

## <a name="achieving-predictive-performance-parity"></a>Uppnå förutsägbar prestanda paritet
Vår första prioritet var att uppnå paritet mellan Studio (klassiska) och Regressions modeller för Excel. Med samma data, och samma delning för utbildning och test av data, ville vi uppnå förutsägande prestanda paritet mellan Excel och Studio (klassisk). Det gick inte att börja med. Den klassiska Studio-modellen (Excel-modellen). Detta misslyckades på grund av bristande förståelse för bas verktygs inställningen i Studio (klassisk). Efter en synkronisering med produkt teamet för Studio (klassisk) fick vi en bättre förståelse för den grundläggande inställningen som krävs för våra data uppsättningar och uppnått paritet mellan de två modellerna. 

### <a name="create-regression-model-in-excel"></a>Skapa Regressions modell i Excel
Vår Excel-regression använde den standard linjära Regressions modell som finns i Excel Analysis ToolPak. 

Vi beräknade *betyder absolut% fel* och använde det som prestanda mått för modellen. Det tog 3 månader att komma till en fungerande modell med hjälp av Excel. Vi har gått mycket av inlärningen till Studio (klassiskt) experiment, vilket i slut ändan är att förstå kraven.

### <a name="create-comparable-experiment-in-studio-classic"></a>Skapa jämförbart experiment i Studio (klassisk)
Vi har följt de här stegen för att skapa vårt experiment i Studio (klassiskt): 

1. Överför data uppsättningen som en CSV-fil till Studio (klassisk) (mycket liten fil)
2. Skapat ett nytt experiment och använde modulen [Välj kolumner i data uppsättning][select-columns] för att välja samma data funktioner som används i Excel 
3. Använde modulen [dela data][split] (med *relativt uttrycks* läge) för att dela upp data i samma utbildnings data uppsättningar som har utförts i Excel 
4. Experimentat med modulen [linjär regression][linear-regression] (endast standard alternativ), dokumenterad och jämför resultaten med vår Excel regression-modell

### <a name="review-initial-results"></a>Granska inledande resultat
I början utförde Excel-modellen klart modellen Studio (klassisk): 

|  | Excel | Studio (klassisk) |
| --- |:---:|:---:|
| Prestanda | | |
| <ul style="list-style-type: none;"><li>Justerad R-kvadrat</li></ul> |0.96 |Saknas |
| <ul style="list-style-type: none;"><li>Koefficienten för <br />Bestämning</li></ul> |Saknas |0.78<br />(låg exakthet) |
| Medelvärde för absolut fel |$9,5 m |$19.4 m |
| Medelvärde för absolut fel (%) |6.03% |12.2% |

När vi körde vår process och sina resultat av utvecklare och data experter på Machine Learning-teamet, har de snabbt fått några användbara tips. 

* När du använder modulen [linjär regression][linear-regression] i Studio (klassisk) tillhandahålls två metoder:
  * Brantaste för online-toning: kan vara lämpligare för större skalnings problem
  * Vanliga minsta kvadratmetoden: det här är den metod som de flesta tycker om när de hör linjär regression. För små data uppsättningar kan vanliga minsta kvadratmetoden vara ett optimalt alternativ.
* Överväg att justera parametern för L2-Regulariseringshastigheten för att förbättra prestanda. Den ställs in på 0,001 som standard, men för vår små data uppsättning ställer vi in den på 0,005 för att förbättra prestanda. 

### <a name="mystery-solved"></a>Mysteriet löst!
När vi använde rekommendationerna har vi uppnått samma bas linje prestanda i Studio (klassisk) som med Excel: 

|  | Excel | Studio (klassisk) (initial) | Studio (klassiskt) med minsta kvadratmetoden |
| --- |:---:|:---:|:---:|
| Namngett värde |Faktiska värden (numeriska) |det |det |
| Learner |Excel-> data analys-> regression |Linjär regression. |Linjär regression |
| Alternativ för att lära |Saknas |Appar |vanliga minsta kvadratmetoden<br />L2 = 0,005 |
| Datauppsättning |26 rader, 3 funktioner, 1 etikett. Alla numeriska. |det |det |
| Dela: träna |Excel tränade på de första 18 raderna och testats på de sista 8 raderna. |det |det |
| Dela: testa |Excel Regressions formel som tillämpas på de sista 8 raderna |det |det |
| **Prestanda** | | | |
| Justerad R-kvadrat |0.96 |Saknas | |
| Effektiv bestämning |Saknas |0.78 |0.952049 |
| Medelvärde för absolut fel |$9,5 m |$19.4 m |$9,5 m |
| Medelvärde för absolut fel (%) |<span style="background-color: 00FF00;">6,03%</span> |12.2% |<span style="background-color: 00FF00;">6,03%</span> |

Dessutom jämförs Excel-koefficienterna bra med funktions vikterna i den tränade Azure-modellen:

|  | Excel-koefficienter | Funktions vikt för Azure |
| --- |:---:|:---:|
| Snapp/bias |19470209.88 |19328500 |
| Funktion A |0.832653063 |0.834156 |
| Funktion B |11071967.08 |11007300 |
| Funktion C |25383318.09 |25140800 |

## <a name="next-steps"></a>Nästa steg
Vi ville använda den Machine Learning webb tjänsten i Excel. Våra affärsanalytiker förlitar sig på Excel och vi behövde ett sätt att anropa Machine Learning-webbtjänsten med en rad Excel-data och returnera det förväntade värdet till Excel. 

Vi ville också optimera vår modell med hjälp av de alternativ och algoritmer som finns i Studio (klassisk).

### <a name="integration-with-excel"></a>Integrering med Excel
Vår lösning var att operationaliseraa vår Machine Learning Regressions modell genom att skapa en webb tjänst från den tränade modellen. Inom några minuter skapades webb tjänsten och vi kan anropa den direkt från Excel för att returnera ett förväntat intäkts värde. 

Avsnittet *instrument panel för webb tjänster* innehåller en nedladdnings bar Excel-arbetsbok. Arbets boken är i förväg formaterad med webb tjänstens API och schema information Embedded. När du klickar på *Hämta Excel-arbetsbok*öppnas arbets boken och du kan spara den på den lokala datorn. 

![Hämta Excel-arbetsboken från instrument panelen för webb tjänster](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

När arbets boken är öppen kopierar du de fördefinierade parametrarna till den blå parameter avsnittet som visas nedan. När du har angett parametrarna, anropas Excel till Machine Learning-webbtjänsten och de förväntade Poäng etiketterna visas i avsnittet grönt förväntade värden. Arbets boken kommer fortsätta att skapa förutsägelser för parametrar baserat på din tränade modell för alla rad objekt som anges under parametrar. Mer information om hur du använder den här funktionen finns i använda [en Azure Machine Learning webb tjänst från Excel](consuming-from-excel.md). 

![Mall Excel-arbetsbok som ansluter till den distribuerade webb tjänsten](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>Optimering och ytterligare experiment
Nu när vi hade en bas linje med vår Excel-modell flyttades vi framåt för att optimera vår Machine Learning linjära Regressions modell. Vi använde modulen [filter-baserad funktions val][filter-based-feature-selection] för att förbättra vårt val av första data element och det hjälpte oss att uppnå en prestanda förbättring på 4,6% innebär ett absolut fel. För framtida projekt kommer vi att använda den här funktionen som kan spara veckor i veckan genom att söka igenom dataattribut för att hitta rätt uppsättning funktioner som kan användas för modellering. 

Härnäst planerar vi att inkludera ytterligare algoritmer som [Bayesian][bayesian-linear-regression] eller [förstärknings besluts träd][boosted-decision-tree-regression] i vårt experiment för att jämföra prestanda. 

Om du vill experimentera med regression är en bra data uppsättning att prova är Regressions data uppsättningen i Energy effektivitet, som har många numeriska attribut. Data uppsättningen tillhandahålls som en del av exempel data uppsättningarna i Studio (klassisk). Du kan använda en mängd olika inlärnings moduler för att förutse belastningen på uppvärmning eller kylning. Diagrammet nedan är en prestanda jämförelse av olika regressionar för den energi effektivitets data uppsättning som förutsäger för mål variabelns kyl belastning: 

| Modell | Medelvärde för absolut fel | Rot medelvärde för ett kvadratvärde | Relativt absolut fel | Relativt kvadrat-fel | Effektiv bestämning |
| --- | --- | --- | --- | --- | --- |
| Utökat besluts träd |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| Linjär regression (tonings brantaste) |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| Regression för Neural Network |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| Linjär regression (vanliga minsta kvadratmetoden) |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>Key takeaways
Vi har lärt oss mycket genom att köra Excel regression och Studio (klassiska) experiment parallellt. Skapa bas linje modellen i Excel och jämför den med modeller med Machine Learning [linjär regression][linear-regression] hjälpte oss att lära sig Studio (klassisk) och vi har upptäckt möjligheter att förbättra data urval och modell prestanda. 

Vi har också visat att det är tillrådligt att använda [filtrerings-baserade funktions val][filter-based-feature-selection] för att påskynda framtida förutsägelse projekt. Genom att tillämpa funktions val för dina data kan du skapa en förbättrad modell i Studio (klassisk) med bättre övergripande prestanda. 

Möjligheten att överföra förutsägelse analys prognoser från Studio (klassisk) till Excel-systemically ger en betydande ökning av möjligheten att kunna ge resultat till en bred användare av företags användare. 

## <a name="resources"></a>Resurser
Här är några resurser som hjälper dig att arbeta med regression: 

* Regression i Excel. Om du aldrig har provat regression i Excel gör den här självstudien det enkelt: [https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* Regression vs-Prognosticering. Tyler Schackman skrev en blogg artikel som förklarar hur du utför tids serie prognoser i Excel, som innehåller en bättre nybörjar Beskrivning av linjär regression. [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* Vanlig minsta kvadratmetoden linjär regression: fel, problem och fall GRO par. För en introduktion och diskussion av regression: [https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

