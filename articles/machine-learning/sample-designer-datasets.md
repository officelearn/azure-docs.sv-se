---
title: Använda exempeldatauppsättningar i Azure Machine Learning-designer
titleSuffix: Azure Machine Learning
description: Läs mer om exempeldatauppsättningar som ingår i Azure Machine Learning-designern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037309"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Använda exempeldatauppsättningarna i Azure Machine Learning designer (förhandsversion)

När du skapar en ny pipeline i Azure Machine Learning designer (förhandsversion) inkluderas ett antal exempeldatauppsättningar som standard. Dessa exempeldatauppsättningar används av exempelpipelarna på designerns startsida. 

Exempeldatauppsättningarna är tillgängliga under kategorin **Dataset**-**Samples.** Du hittar detta i modulpaletten till vänster om duken i designern. Du kan använda någon av dessa datauppsättningar i din egen pipeline genom att dra den till arbetsytan.

## <a name="datasets"></a>Datauppsättningar


| Namn på&nbsp;Datauppsättning&nbsp;&nbsp;&nbsp;&nbsp;| Beskrivning av datauppsättning |
|-------------|:--------------------|
| Vuxenräkning inkomst binär klassificering dataset | En delmängd av databasen folkräkning från 1994, med arbetande vuxna över 16 år med ett justerat inkomstindex på > 100.<br/>**Användning**: Klassificera personer som använder demografi för att förutsäga om en person tjänar över 50K per år.<br/> **Släkt forskning**: Kohavi, R., Becker B., (1996). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, KALIFORNIEN: University of California, School of Information and Computer Science|
|Bil prisdata (Raw)|Information om bilar efter märke och modell, inklusive priset, funktioner som antalet cylindrar och MPG, samt en försäkring riskpoäng.<br/> Riskpoängen associeras inledningsvis med automatiskt pris. Det justeras sedan för faktisk risk i en process som är känd för aktuarier som symbol. Värdet +3 anger att auton är riskabelt och värdet -3 att det förmodligen är säkert.<br/>**Användning**</b> : Förutsäg riskpoängen efter funktioner, med hjälp av regression eller multivariatklassificering.<br/>**Relaterad forskning:**</b> Schlimmer, J.C. (1987). [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml). Irvine, KALIFORNIEN: University of California, School of Information and Computer Science. |
| CRM-appetiketter delas |Etiketter från KDD Cup 2009 kundrelation förutsägelse utmaning ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|CRM-omsättningsetiketter delade|Etiketter från KDD Cup 2009 kundrelation förutsägelse utmaning ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|CRM-datauppsättningen delad | Dessa data kommer från KDD Cup 2009 kundrelation förutsägelse utmaning ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Datauppsättningen innehåller 50K kunder från det franska telekomföretaget Orange. Varje kund har 230 anonymiserade funktioner, varav 190 är numeriska och 40 är kategoriska. Funktionerna är mycket glesa. |
|CRM merförsäljning etiketter delas|Etiketter från KDD Cup 2009 kundrelation förutsägelse utmaning ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Data om flygförseningar|Prestandadata för passagerarflygning i tid hämtade från TranStats datainsamling av us Department of Transportation ([On-Time](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>Datauppsättningen omfattar tidsperioden april-oktober 2013. Innan du laddar upp till designern bearbetades datauppsättningen på följande sätt: <br/>- Datauppsättningen filtrerades för att endast täcka de 70 mest trafikerade flygplatserna i kontinentala USA <br/>- Inställda flyg var märkta som försenade med mer än 15 minuter <br/>- Omdirigerade flygningar filtrerades bort <br/>- Följande kolumner har valts: År, Månad, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Avbruten|
|Tyska kreditkort UCI dataset|UCI Statlog (tyska kreditkort) dataset ([Statlog + tyska + Credit + Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))), med hjälp av german.data filen.<br/>Datauppsättningen klassificerar personer, som beskrivs av en uppsättning attribut, som låga eller höga kreditrisker. Varje exempel representerar en person. Det finns 20 funktioner, både numeriska och kategoriska, och en binär etikett (kreditriskvärdet). Hög kreditrisk poster har etikett = 2, låg kreditrisk poster har etikett = 1. Kostnaden för felklassificering av ett lågriskexempel som hög är 1, medan kostnaden för att felklassificera ett högriskexempel som lågt är 5.|
|Imdb-filmtitlar|Datauppsättningen innehåller information om filmer som har betygsatts i Twitter tweets: IMDB film-ID, filmnamn, genre och produktionsår. Det finns 17K-filmer i datauppsättningen. Datauppsättningen introducerades i uppsatsen "S. Dooms, T. De Pessemier och L. Martens. MovieTweetings: en film rating datauppsättning samlas in från Twitter. Workshop om Crowdsourcing och human beräkning för Recommender Systems, CrowdRec på RecSys 2013."|
|Film betyg|Datauppsättningen är en utökad version av datauppsättningen Movie Tweetings. Datauppsättningen har 170K betyg för filmer, utvinns ur välstrukturerade tweets på Twitter. Varje instans representerar en tweet och är en tuple: användar-ID, IMDB film-ID, betyg, tidsstämpel, antal favoriter för denna tweet, och antal retweets av denna tweet. Datauppsättningen gjordes tillgänglig av A. Said, S. Dooms, B. Loni och D. Tikk för Recommender Systems Challenge 2014.|
|Väder Dataset|Landbaserade väderobservationer per timme från NOAA ([sammanslagna data från 201304 till 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Väderdata omfattar observationer gjorda från flygplatsens väderstationer, som omfattar tidsperioden april-oktober 2013. Innan du laddar upp till designern bearbetades datauppsättningen på följande sätt:    <br/> - Väderstations-ID har mappats till motsvarande flygplats-ID    <br/> - Väderstationer som inte är associerade med de 70 mest trafikerade flygplatserna filtrerades bort    <br/> - Kolumnen Datum delades upp i separata kolumner för år, månad och dag    <br/> - Följande kolumner har valts: AirportID, År, Månad, Dag, Tid, Tidszon, SkyCondition, Synlighet, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, Timprecip, Altimeter|
|Wikipedia SP 500 Datauppsättning|Data härleds frånhttps://www.wikipedia.org/) Wikipedia ( baserat på artiklar i varje S&P 500 företag, lagras som XML-data.    <br/>Innan du laddar upp till designern bearbetades datauppsättningen på följande sätt:    <br/> - Extrahera textinnehåll för varje specifikt företag    <br/> - Ta bort wiki-formatering    <br/> - Ta bort icke-alfanumeriska tecken    <br/> - Konvertera all text till gemener    <br/> - Kända företagskategorier har lagts till    <br/>Observera att det inte gick att hitta en artikel för vissa företag, så antalet poster är mindre än 500.|

## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna i prediktiv analys och maskininlärning med [handledning: Förutsäg bilpris med designern](tutorial-designer-automobile-price-train-score.md)

* Lär dig hur du ändrar befintliga [designerexempel](samples-designer.md) för att anpassa dem till dina behov.
