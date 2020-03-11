---
title: Använd exempel data uppsättningar i Azure Machine Learning designer
titleSuffix: Azure Machine Learning
description: Lär dig mer om de exempel data uppsättningar som ingår i Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037309"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Använd exempel data uppsättningarna i Azure Machine Learning designer (förhands granskning)

När du skapar en ny pipeline i Azure Machine Learning designer (för hands version) ingår ett antal exempel data uppsättningar som standard. Dessa exempel data uppsättningar används av exempel pipelinen på design sidan för designer. 

Exempel data uppsättningarna är tillgängliga under **data uppsättningar**-**exempel** kategori. Du kan hitta detta i modulen modul till vänster om arbets ytan i designern. Du kan använda någon av dessa data uppsättningar i din egen pipeline genom att dra den till arbets ytan.

## <a name="datasets"></a>Datauppsättningar


| Data uppsättningens&nbsp;namn&nbsp;&nbsp;&nbsp;&nbsp;| Beskrivning av data uppsättning |
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
|Data uppsättning väder|Varje timmes markbaserade väderleks observationer från NOAA ([sammanfogade data från 201304 till 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Väder data täcker observationer som gjorts från flyg platsens väderleks stationer, som omfattar den period som april – 2013 oktober. Innan du överför till designern bearbetades data uppsättningen enligt följande:    <br/> -Väderleks Stations-ID mappas till motsvarande flyg plats-ID    <br/> – Väder stationer som inte är kopplade till 70 vanligaste-flyg platserna filtrerades bort    <br/> – Datum kolumnen delades upp i separata kolumner för år, månad och dag    <br/> – Följande kolumner valdes: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altimeter|
|Wikipedia SP 500-datauppsättning|Data härleds från Wikipedia (https://www.wikipedia.org/) baserat på artiklar i varje S & P 500-företag, lagrade som XML-data.    <br/>Innan du överför till designern bearbetades data uppsättningen enligt följande:    <br/> – Extrahera text innehåll för varje enskilt företag    <br/> – Ta bort wiki-format    <br/> -Ta bort icke-alfanumeriska tecken    <br/> -Konvertera all text till gemener    <br/> -Kända företags kategorier har lagts till    <br/>Observera att för vissa företag gick det inte att hitta en artikel, så antalet poster är mindre än 500.|

## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna för förutsägelse analys och maskin inlärning med [Självstudier: förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md)

* Lär dig hur du ändrar befintliga [Designer-exempel](samples-designer.md) för att anpassa dem efter dina behov.
