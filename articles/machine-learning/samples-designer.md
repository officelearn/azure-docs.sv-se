---
title: Pipelines i exempel designern & data uppsättningar
titleSuffix: Azure Machine Learning
description: Använd exempel i Azure Machine Learning designer för att komma igång med dina pipelines i Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: 60f8f2593ef9c05fa905e9d3d6f6ba0901a14243
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983354"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Exempel på pipelines & data uppsättningar för Azure Machine Learning designer

Använd de inbyggda exemplen i Azure Machine Learning designer för att snabbt komma igång med att skapa dina egna maskin inlärnings pipeliner. Azure Machine Learning designer [GitHub-lagringsplatsen](https://github.com/Azure/MachineLearningDesigner) innehåller detaljerad dokumentation som hjälper dig att förstå några vanliga scenarier för maskin inlärning.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).
* En Azure Machine Learning-arbetsyta med Enterprise SKU.

## <a name="use-sample-pipelines"></a>Använd exempel pipelines

Designern sparar en kopia av exempel pipelines till din Studio-arbetsyta. Du kan redigera pipelinen för att anpassa den efter dina behov och spara den som din egen. Använd dem som start punkt för att rivstart med dina projekt.

Så här använder du ett designer-exempel:

1. Logga in på <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>och välj den arbets yta som du vill arbeta med.

1. Välj **Designer**.

1. Välj en exempel-pipeline under det **nya pipeline** -avsnittet.

    Välj **Visa fler exempel** för en fullständig lista över exempel.

1. Om du vill köra en pipeline måste du först ställa in ett standard beräknings mål för att köra pipelinen på.

   1. I fönstret **Inställningar** till höger om arbets ytan väljer du **Välj Compute Target (Välj Compute Target**).

   1. I dialog rutan som visas väljer du ett befintligt beräknings mål eller skapar ett nytt. Välj **Spara**.

   1. Välj **Skicka** överst på arbets ytan för att skicka en pipeline-körning.

   Beroende på pipeline-och beräknings inställningarna i exemplet kan det ta en stund att slutföra körningen. Standard beräknings inställningarna har en minsta Node-storlek på 0, vilket innebär att Designer måste allokera resurser efter inaktivitet. Upprepade pipelines körningar tar mindre tid eftersom beräknings resurserna redan har allokerats. Dessutom använder designern cachelagrade resultat för varje modul för att ytterligare förbättra effektiviteten.


1. När pipelinen har körts klart kan du granska pipelinen och visa utdata för varje modul för att lära dig mer. Använd följande steg för att visa utdata från modulen:

   1. Välj en modul på arbets ytan.

   1. I informations fönstret för moduler till höger om arbets ytan väljer du **utdata + loggar**. Välj ikonen ![](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) för diagrammets visuella ikon för att se resultatet av varje modul. 

   Använd exemplen som start punkter för några av de vanligaste scenarierna för maskin inlärning.

## <a name="regression"></a>Regression

Utforska de här inbyggda Regressions exemplen.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 1: regression – pris förutsägelse för bilar (grundläggande)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Förutsäga bil priser med linjär regression. |
| [Exempel 2: regression – pris förutsägelse för bilar (avancerat)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Förutsäga bil priser med hjälp av besluts skog och utökat besluts träd regressorer. Jämför modeller för att hitta den bästa algoritmen.

## <a name="classification"></a>Klassificering

Utforska de här inbyggda klassificerings exemplen. Du kan lära dig mer om exemplen utan dokumentations länkar genom att öppna exemplen och Visa kommentarer i modulen i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 3: binära klassificering med funktions val – inkomst förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Förutsäga inkomst som hög eller låg genom att använda ett besluts träd med två klasser. Använd Pearson-korrelation för att välja funktioner.
| [Exempel 4: binära klassificering med anpassat Python-skript – kredit risk förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klassificera kredit program som hög eller låg risk. Använd modulen kör Python-skript för att vikta dina data.
| [Exempel 5: binära klassificering – kund Relations förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Förutsäga kund omsättningen med hjälp av två klass förstärknings besluts träd. Använd SMOTE för att sampla förprioriterade data.
| [Exempel 7: text klassificering-Wikipedia SP 500-datauppsättning](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klassificera företags typer från Wikipedia-artiklar med multiklass logistik regression. |
| Exempel 12: klassificerings igenkänning av multiklasser – brev igenkänning | Skapa en ensemble med binära klassificerare för att klassificera skrivna bokstäver. |

## <a name="recommender"></a>Rekommenderare

Utforska de här inbyggda rekommendations exemplen. Du kan lära dig mer om exemplen utan dokumentations länkar genom att öppna exemplen och Visa kommentarer i modulen i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| Exempel 10: rekommendation-film klassificering tweets | Bygg en Movie rekommenderar-motor från film titlar och klassificering. |

## <a name="utility"></a>Verktyg

Lär dig mer om de exempel som demonstrerar verktyg och funktioner för maskin inlärning. Du kan lära dig mer om exemplen utan dokumentations länkar genom att öppna exemplen och Visa kommentarer i modulen i stället.

| Exempelrubrik | Beskrivning | 
| --- | --- |
| [Exempel 6: Använd anpassat R-skript – flyg fördröjnings förutsägelse](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Exempel 8: kors validering för binär klassificering – vuxen inkomst förutsägelse | Använd kors validering för att bygga en binär klassificerare för vuxen inkomster.
| Exempel 9: permutations funktions prioritet | Använd permutation-funktionen för att beräkna prioritets resultat för test data uppsättningen. 
| Exempel 11: finjustera parametrar för binär klassificering – naturinkomst förutsägelse | Använd finjustera modellens egenskaper för att hitta optimala disponeringsparametrarna för att bygga en binär klassificerare. |

## <a name="datasets"></a>Datauppsättningar

När du skapar en ny pipeline i Azure Machine Learning designer ingår ett antal exempel data uppsättningar som standard. Dessa exempel data uppsättningar används av exempel pipelinen på design sidan för designer. 

Exempel data uppsättningarna finns i kategorin **data uppsättnings**-**exempel** . Du kan hitta detta i modulen modul till vänster om arbets ytan i designern. Du kan använda någon av dessa data uppsättningar i din egen pipeline genom att dra den till arbets ytan.

| Data&nbsp;uppsättnings namn&nbsp;&nbsp;&nbsp;&nbsp;| Beskrivning av data uppsättning |
|-------------|:--------------------|
| Data uppsättning för binära sammanräknings inkomst | En delmängd av databasen för 1994-inventering, med hjälp av att arbeta vuxna under 16 med ett justerat inkomst index på > 100.<br/>**Användning**: klassificera personer som använder demografiska till att förutsäga om en person får över 50 000 per år.<br/> **Relaterad forskning**: Kohavi, R., Becker, B., (1996). [Machine Learning-lagringsplats för](https://archive.ics.uci.edu/ml). Irvine, CA: University of Kalifornien, skol information och dator vetenskap|
|Bil pris data (RAW)|Information om mobila enheter med märke och modeller, inklusive pris, funktioner, till exempel antalet cylindrar och MPG, samt en försäkrings risk poäng.<br/> Risk poängen är inlednings vis kopplad till automatiskt pris. Den justeras sedan för faktisk risk i en process som är känd för aktuarier som symbol. Värdet + 3 anger att det automatiska är riskabelt och att värdet-3 förmodligen är säkert.<br/>**Användning**:</b> förutsäga risk poängen med hjälp av funktioner, med regression eller multivarierad klassificering.<br/>**Relaterad forskning**:</b> Schlimmer, J.C. (1987). [Machine Learning-lagringsplats för](https://archive.ics.uci.edu/ml). Irvine, CA: University of Kalifornien, skol information och dator vetenskap. |
| CRM begär-etiketter delade |Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|CRM omsättnings etiketter delade|Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|CRM-datauppsättning delad | Dessa data kommer från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse ([orange_small_train. data. zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Data uppsättningen innehåller 50 000-kunder från det franska telekom-företaget orange. Varje kund har 230 anonymiserats-funktioner, men 190 av som är numeriska och 40 är kategoriska. Funktionerna är mycket glesa. |
|CRM försäljar etiketter delade|Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse ([orange_large_train_upselling. labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Data för flyg fördröjning|Prestanda data för passagerar flygning i tid som tagits från TranStats-datainsamlingen för USA: s transport ([i tid](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>Data uppsättningen täcker den tids period som april – oktober 2013. Innan du överför till designern bearbetades data uppsättningen enligt följande: <br/>– Data uppsättningen har filtrerats för att endast avse 70 vanligaste-flyg platserna i kontinental USA <br/>-Avbrutna flygningar har märkts som fördröjda med mer än 15 minuter <br/>-Avlästa flygningar filtrerades bort <br/>– Följande kolumner valdes: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, avbruten|
|German-datauppsättning för tyska kredit kort|Den Statlog (tyska kredit kort) data uppsättningen ([Statlog + tyska + kredit + data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))), med hjälp av den tyska. data filen.<br/>Data uppsättningen klassificerar personer, som beskrivs av en uppsättning attribut, som små eller stora kredit risker. Varje exempel representerar en person. Det finns 20 funktioner, både numeriska och kategoriska och en binär etikett (kredit risk värde). Stora kredit risk poster har etikett = 2, låg kredit risk poster har etikett = 1. Kostnaden för att inte klassificera ett litet risk exempel som hög är 1, medan kostnaden för att klassificera ett högt risk exempel som låg är 5.|
|Film titlar för IMDB|Data uppsättningen innehåller information om filmer som har klassificerats i Twitter-tweets: IMDB Movie ID, Movie Name, genre och Product Year. Det finns 17K filmer i data uppsättningen. Data uppsättningen introducerades i papperet "S. Dooms, T. ex. de Pessemier och L. Martens. MovieTweetings: en film betygs uppsättning som samlats in från Twitter. Workshop om gemensamt skapade och mänsklig beräkning för rekommenderade system, CrowdRec på RecSys 2013. "|
|Film klassificeringar|Data uppsättningen är en utökad version av data uppsättningen för film tweeter. Data uppsättningen har 170K klassificeringar för filmer som extraheras från välstrukturerade tweets på Twitter. Varje instans representerar en tweet och är en tupel: användar-ID, IMDB-film-ID, klassificering, tidsstämpel, antal favoriter för den här tweeten och antalet retweetar för den här tweeten. Data uppsättningen gjordes tillgänglig av A. S. Dooms, B. Loni och D. Tikk för rekommenderade system Challenge 2014.|
|Data uppsättning väder|Varje timmes markbaserade väderleks observationer från NOAA ([sammanfogade data från 201304 till 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Väder data täcker observationer som gjorts från flyg platsens väderleks stationer, som omfattar den period som april – 2013 oktober. Innan du överför till designern bearbetades data uppsättningen enligt följande:    <br/> -Väderleks Stations-ID mappas till motsvarande flyg plats-ID    <br/> – Väder stationer som inte är kopplade till 70 vanligaste-flyg platserna filtrerades bort    <br/> – Datum kolumnen delades upp i separata kolumner för år, månad och dag    <br/> – Följande kolumner valdes: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altimeter,,,,|
|Wikipedia SP 500-datauppsättning|Data härleds från Wikipedia (https://www.wikipedia.org/) baserat på artiklarna i varje S&P 500-företag, lagrade som XML-data.    <br/>Innan du överför till designern bearbetades data uppsättningen enligt följande:    <br/> – Extrahera text innehåll för varje enskilt företag    <br/> – Ta bort wiki-format    <br/> -Ta bort icke-alfanumeriska tecken    <br/> -Konvertera all text till gemener    <br/> -Kända företags kategorier har lagts till    <br/>Observera att för vissa företag gick det inte att hitta en artikel, så antalet poster är mindre än 500.|


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig grunderna för förutsägelse analys och maskin inlärning med [Självstudier: förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md)

