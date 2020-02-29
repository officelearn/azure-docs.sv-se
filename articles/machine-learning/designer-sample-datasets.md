---
title: Använd exempel data uppsättningarna
titleSuffix: Azure Machine Learning
description: Beskrivningar av de data uppsättningar som används i exempel modeller som ingår i Machine Learning designer. Du kan använda dessa exempel data uppsättningar för dina pipeliner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165067"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Använd exempel data uppsättningarna i Azure Machine Learning designer (förhands granskning)

När du skapar en ny pipeline i Azure Machine Learning designer (för hands version) ingår ett antal exempel data uppsättningar som standard. Många av dessa exempel data uppsättningar används av exempel modellerna på design sidan för designern. Andra inkluderas som exempel på olika typer av data som vanligt vis används i Machine Learning.

Några av dessa data uppsättningar är tillgängliga i Azure Blob Storage. I följande tabell finns en direkt länk för de här data uppsättningarna. Du kan använda dessa data uppsättningar i pipelinen med hjälp av modulen [Importera data](./algorithm-module-reference/import-data.md) .

Resten av dessa exempel data uppsättningar är tillgängliga under **data uppsättningar**-**exempel** kategori. Du kan hitta detta i modulen modul till vänster om arbets ytan i designern. Du kan använda någon av dessa data uppsättningar i din egen pipeline genom att dra den till arbets ytan.

## <a name="datasets"></a>Datauppsättningar

<table>

<tr>
  <th>Namn på datauppsättning</th>
  <th>Beskrivning av data uppsättning</th>
</tr>

<tr>
  <td>Data uppsättning för binära sammanräknings inkomst</td>
  <td>
En delmängd av databasen för 1994-inventering, med hjälp av att arbeta vuxna under 16 med ett justerat inkomst index på > 100.
<p></p>
<b>Användning:</b> Klassificera personer som använder demografiska till att förutsäga om en person får över 50 000 per år.
<p></p>
<b>Relaterad forskning:</b> Kohavi, R., Becker, B., (1996). Machine Learning lagrings <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>plats för. Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Bil pris data (RAW)</td>
  <td>
Information om mobila enheter med märke och modeller, inklusive pris, funktioner, till exempel antalet cylindrar och MPG, samt en försäkrings risk poäng.
<p></p>
Risk poängen är inlednings vis kopplad till automatiskt pris. Den justeras sedan för faktisk risk i en process som är känd för aktuarier som symbol. Värdet + 3 anger att det automatiska är riskabelt och att värdet-3 förmodligen är säkert.
<p></p>
<b>Användning:</b> Förutsäg risk poängen med hjälp av funktioner, regression eller multivarierad klassificering. 
<p></p>
<b>Relaterad forskning:</b> Schlimmer, J.C. (1987). Machine Learning lagrings <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>plats för. Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>


<tr>
  <td>CRM begär-etiketter delade</td>
  <td>
Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>).
  </td>
</tr>

<tr>
  <td>CRM omsättnings etiketter delade</td>
  <td>
Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-datauppsättning delad</td>
  <td>
Dessa data kommer från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train. data. zip</a>).
<p></p>
Data uppsättningen innehåller 50 000-kunder från det franska telekom-företaget orange. Varje kund har 230 anonymiserats-funktioner, men 190 av som är numeriska och 40 är kategoriska. Funktionerna är mycket glesa.
  </td>
</tr>

<tr>
  <td>CRM försäljar etiketter delade</td>
  <td>
Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>).
  </td>
</tr>

<tr>
  <td>Data för flyg fördröjning</td>
  <td>
Prestanda data för passagerar flygning i tid som tagits från TranStats-datainsamlingen för USA: s transport (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">i tid</a>).
<p></p>
Data uppsättningen täcker den tids period som april – oktober 2013. Innan du överför till designern bearbetades data uppsättningen enligt följande:
<ul>
  <li>Data uppsättningen har filtrerats för att endast avse 70 vanligaste-flyg platserna i kontinental USA</li>
  <li>Annullerade flygningar har märkts som fördröjda med mer än 15 minuter</li>
  <li>Avlästa flygningar filtrerades bort</li>
  <li>Följande kolumner valdes: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, avbruten</li>
</ul>
</td>
</tr>

<tr>
  <td>German-datauppsättning för tyska kredit kort</td>
  <td>
Den Statlog (tyska kredit kort) data uppsättningen (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + tyska + kredit + data</a>), med hjälp av den tyska. data filen.
<p></p>
Data uppsättningen klassificerar personer, som beskrivs av en uppsättning attribut, som små eller stora kredit risker. Varje exempel representerar en person. Det finns 20 funktioner, både numeriska och kategoriska och en binär etikett (kredit risk värde). Stora kredit risk poster har etikett = 2, låg kredit risk poster har etikett = 1. Kostnaden för att inte klassificera ett litet risk exempel som hög är 1, medan kostnaden för att klassificera ett högt risk exempel som låg är 5.
  </td>
</tr>

<tr>
  <td>Film titlar för IMDB</td>
  <td>
Data uppsättningen innehåller information om filmer som har klassificerats i Twitter-tweets: IMDB Movie ID, Movie Name, genre och Product Year. Det finns 17K filmer i data uppsättningen. Data uppsättningen introducerades i papperet "S. Dooms, T. ex. de Pessemier och L. Martens. MovieTweetings: en film betygs uppsättning som samlats in från Twitter. Workshop om gemensamt skapade och mänsklig beräkning för rekommenderade system, CrowdRec på RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Film klassificeringar</td>
  <td>
Data uppsättningen är en utökad version av data uppsättningen för film tweeter. Data uppsättningen har 170K klassificeringar för filmer som extraheras från välstrukturerade tweets på Twitter. Varje instans representerar en tweet och är en tupel: användar-ID, IMDB-film-ID, klassificering, tidsstämpel, antal favoriter för den här tweeten och antalet retweetar för den här tweeten. Data uppsättningen gjordes tillgänglig av A. S. Dooms, B. Loni och D. Tikk för rekommenderade system Challenge 2014.
  </td>
</tr>


<tr>
  <td>Data uppsättning väder</td>
  <td>
Varje timmes markbaserade väderleks observationer från NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">sammanfogade data från 201304 till 201310</a>).
<p></p>
Väder data täcker observationer som gjorts från flyg platsens väderleks stationer, som omfattar den period som april – 2013 oktober. Innan du överför till designern bearbetades data uppsättningen enligt följande:
<ul>
  <li>Väder Stations-ID: n mappades till motsvarande flyg plats-ID</li>
  <li>Väder stationer som inte är kopplade till 70 vanligaste-flyg platserna filtrerades bort</li>
  <li>Datum kolumnen delades upp i separata kolumner för år, månad och dag</li>
  <li>Följande kolumner valdes: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500-datauppsättning</td>
  <td>
Data härleds från Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) baserat på artiklar i varje S & P 500-företag, lagrade som XML-data.
<p></p>
Innan du överför till designern bearbetades data uppsättningen enligt följande:
<ul>
  <li>Extrahera text innehåll för varje enskilt företag</li>
  <li>Ta bort wiki-formatering</li>
  <li>Ta bort icke-alfanumeriska tecken</li>
  <li>Konvertera all text till gemener</li>
  <li>Kända företags kategorier har lagts till</li>
</ul>
<p></p>
Observera att för vissa företag gick det inte att hitta en artikel, så antalet poster är mindre än 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Nästa steg

* Lär dig grunderna för förutsägelse analys och maskin inlärning med [Självstudier: förutsäga bil pris med designern](tutorial-designer-automobile-price-train-score.md)

* Använd modulen [Importera data](./algorithm-module-reference/import-data.md) för att importera exempel data uppsättningar
