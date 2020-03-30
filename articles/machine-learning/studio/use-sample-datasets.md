---
title: Använda exempeldatauppsättningarna
titleSuffix: ML Studio (classic) - Azure
description: Beskrivningar av de datauppsättningar som används i exempelmodeller som ingår i Machine Learning Studio (klassisk). Du kan använda dessa exempeldatauppsättningar för dina experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: b7b8606f7f15f8d6fdd66681a1c7ade60ff506f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217790"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Använda exempeldatauppsättningarna i Azure Machine Learning Studio (klassisk)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

[top]: #machine-learning-sample-datasets

När du skapar en ny arbetsyta i Azure Machine Learning Studio (klassisk) inkluderas ett antal exempeldatauppsättningar och experiment som standard. Många av dessa exempeldatauppsättningar används av exempelmodellerna i [Azure AI Gallery](https://gallery.azure.ai/). Andra ingår som exempel på olika typer av data som vanligtvis används i maskininlärning.

Vissa av dessa datauppsättningar är tillgängliga i Azure Blob-lagring. För dessa datauppsättningar innehåller följande tabell en direkt länk. Du kan använda dessa datauppsättningar i experimenten med hjälp av modulen [Importera data.][import-data]

Resten av dessa exempeldatauppsättningar är tillgängliga på arbetsytan under **Sparade datauppsättningar**. Du hittar detta i modulpaletten till vänster om experimentduken i Machine Learning Studio (klassisk).
Du kan använda någon av dessa datauppsättningar i ditt eget experiment genom att dra den till experimentarbetsytan.

## <a name="datasets"></a>Datauppsättningar

<table>

<tr>
  <th>Namn på datauppsättning</th>
  <th>Beskrivning av datauppsättning</th>
</tr>

<tr>
  <td>Vuxenräkning inkomst binär klassificering dataset</td>
  <td>
En delmängd av databasen folkräkning från 1994, med arbetande vuxna över 16 år med ett justerat inkomstindex på > 100.
<p></p>
<b>Användning:</b> Klassificera personer som använder demografi för att förutsäga om en person tjänar över 50K per år.
<p></p>
<b>Relaterad forskning:</b> Kohavi, R., Becker, B., (1996). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Flygplatskoder Datauppsättning</td>
  <td>
Amerikanska flygplatskoder.
<p></p>
Den här datauppsättningen innehåller en rad för varje amerikansk flygplats, vilket ger flygplats-ID-nummer och namn tillsammans med platsens stad och delstat.
  </td>
</tr>

<tr>
  <td>Bil prisdata (Raw)</td>
  <td>
Information om bilar efter märke och modell, inklusive priset, funktioner som antalet cylindrar och MPG, samt en försäkring riskpoäng.
<p></p>
Riskpoängen associeras inledningsvis med automatiskt pris. Det justeras sedan för faktisk risk i en process som är känd för aktuarier som symbol. Värdet +3 anger att auton är riskabelt och värdet -3 att det förmodligen är säkert.
<p></p>
<b>Användning:</b> Förutsäga riskpoängen med funktioner med hjälp av regressions- eller multivariatklassificering. 
<p></p>
<b>Relaterad forskning:</b> Schlimmer, J.C. (1987). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Cykeluthyrning UCI datauppsättning</td>
  <td>
UCI Bike Rental datauppsättning som är baserad på verkliga data från Capital Bikeshare företag som upprätthåller en cykel hyra nätverk i Washington DC.
<p></p>
Datauppsättningen har en rad för varje timme varje dag 2011 och 2012, för totalt 17 379 rader. Utbudet av cykeluthyrning per timme är från 1 till 977.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB Bild</td>
  <td>
Offentligt tillgänglig bildfil konverteras till CSV-data.
<p></p>
Koden för konvertering av avbildningen finns på sidan <strong>Färgkvantisering med hjälp av detaljsidan för K-Means-klustermodell.</strong>
  </td>
</tr>

<tr>
  <td>Uppgifter om blodgivning</td>
  <td>
En delmängd data från blodgivardatabasen vid Blood Transfusion Service Center i Hsin-Chu City, Taiwan.
<p></p>
Givardata inkluderar månaderna efter den sist donationen), och frekvens, eller det sammanlagda numrerar av donationer, tid efter sist donation och belopp av blod donerat.
<p></p>
<b>Användning:</b> Målet är att genom klassificering förutsäga om givaren donerade blod i mars 2007, där 1 anger en givare under målperioden, och 0 en icke-givare. 
<p></p>
<b>Relaterad forskning:</b> Yeh, I.C., (2008). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science
<p></p>
Yeh, I-Cheng, Yang, King-Jang, och Ting, Tao-Ming, "Kunskap upptäckt på RFM modell med Bernoulli sekvens, "Expert System med applikationer, 2008,<a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Bröstcancer data</td>
  <td>
En av tre cancerrelaterade dataset från Oncology Institute som förekommer ofta i maskininlärningslitteratur. Kombinerar diagnostisk information med funktioner från laboratorieanalys av cirka 300 vävnadsprover.
<p></p>
<b>Användning:</b> Klassificera vilken typ av cancer, baserat på 9 attribut, varav några är linjära och vissa är kategoriska. 
<p></p>
<b>Relaterad forskning:</b> Wohlberg, W.H., Gata, W.N., & Mangasarian, O.L. (1995). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Bröstcancer Funktioner <td>
Datauppsättningen innehåller information för 102K misstänkta regioner (kandidater) av röntgenbilder, var och en beskrivs av 117 funktioner. Funktionerna är proprietära och deras innebörd avslöjas inte av datauppsättningen skapare (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Bröstcancer Info</td>
  <td>
Datauppsättningen innehåller ytterligare information för varje misstänkt område med röntgenbild. Varje exempel innehåller information (till exempel etikett, patient-ID, koordinater för plåstret i förhållande till hela bilden) om motsvarande radnummer i datauppsättningen Bröstcancerfunktioner. Varje patient har ett antal exempel. För patienter som har cancer är vissa exempel positiva och vissa negativa. För patienter som inte har cancer, alla exempel är negativa. Datauppsättningen har 102K exempel. Datauppsättningen är partisk, 0,6% av punkterna är positiva, resten är negativa. Dataset gjordes tillgängligt av Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>CRM-appetiketter delas</td>
  <td>
Etiketter från KDD Cup 2009 kundrelation förutsägelse utmaning (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-omsättningsetiketter delade</td>
  <td>
Etiketter från KDD Cup 2009 kundrelation förutsägelse utmaning (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-datauppsättningen delad</td>
  <td>
Dessa data kommer från KDD Cup 2009 kundrelation förutsägelse utmaning (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Datauppsättningen innehåller 50K kunder från det franska telekomföretaget Orange. Varje kund har 230 anonymiserade funktioner, varav 190 är numeriska och 40 är kategoriska. Funktionerna är mycket glesa.
  </td>
</tr>

<tr>
  <td>CRM merförsäljning etiketter delas</td>
  <td>
Etiketter från KDD Cup 2009 kundrelation förutsägelse utmaning (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Uppgifter om regression av energieffektivitet</td>
  <td>
En samling simulerade energiprofiler, baserade på 12 olika byggnadsformer. Byggnaderna är differentierade med åtta egenskaper. Detta inkluderar glasyta, glasytafördelning och orientering.
<p></p>
<b>Användning:</b> Använd antingen regression eller klassificering för att förutsäga energieffektivitetsklassificeringen baserad som ett av två verkliga värdena. För klassificering i flera klasser avrundas svarsvariabeln till närmaste heltal. 
<p></p>
<b>Relaterad forskning:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Data om flygförseningar</td>
  <td>
Prestandadata för passagerarflygning i tid hämtade från TranStats datainsamling av us Department of Transportation (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).
<p></p>
Datauppsättningen omfattar tidsperioden april-oktober 2013. Innan datauppsättningen laddades upp till Azure Machine Learning Studio (klassisk) bearbetades den enligt följande:
<ul>
  <li>Datauppsättningen filtrerades för att endast täcka de 70 mest trafikerade flygplatserna i kontinentala USA</li>
  <li>Inställda flyg var märkta som försenade med mer än 15 minuter</li>
  <li>Omdirigerade flygningar filtrerades bort</li>
  <li>Följande kolumner har valts: År, Månad, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Avbruten</li>
</ul>
</td>
</tr>

<tr>
  <td>Flyg i tid prestanda (Raw)</td>
  <td>
Register över flygresor och avgångar inom USA från oktober 2011.
<p></p>
<b>Användning:</b> Förutse flygförseningar. 
<p></p>
<b>Relaterad forskning:</b> Från US Dept <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>of Transportation .
  </td>
</tr>

<tr>
  <td>Uppgifter om skogsbränder</td>
  <td>
Innehåller väderdata, till exempel temperatur- och fuktighetsindex och vindhastighet. Uppgifterna är hämtade från ett område i nordöstra Portugal, i kombination med uppgifter om skogsbränder.
<p></p>
<b>Användning:</b> Detta är en svår regressionsuppgift, där syftet är att förutsäga det brända området med skogsbränder. 
<p></p>
<b>Relaterad forskning:</b> Cortez, P., & Morais, A. (2008). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science
<p></p>
[Cortez och Morais, 2007] P. Cortez och A. Morais. En data mining strategi för att förutsäga skogsbränder med hjälp av meteorologiska data. I J. Neves, M. F. Santos och J. Machado Eds., Nya trender i artificiell intelligens, Proceedings of the 13th EPIA 2007 - Portugisisk konferens om artificiell intelligens, december, Guimarães, Portugal, sid. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Finns på: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Tyska kreditkort UCI dataset</td>
  <td>
UCI Statlog (tyska kreditkort) dataset (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + tyska + Credit + Data</a>), med hjälp av german.data filen.
<p></p>
Datauppsättningen klassificerar personer, som beskrivs av en uppsättning attribut, som låga eller höga kreditrisker. Varje exempel representerar en person. Det finns 20 funktioner, både numeriska och kategoriska, och en binär etikett (kreditriskvärdet). Hög kreditrisk poster har etikett = 2, låg kreditrisk poster har etikett = 1. Kostnaden för felklassificering av ett lågriskexempel som hög är 1, medan kostnaden för att felklassificera ett högriskexempel som lågt är 5.
  </td>
</tr>

<tr>
  <td>Imdb-filmtitlar</td>
  <td>
Datauppsättningen innehåller information om filmer som har betygsatts i Twitter tweets: IMDB film-ID, filmnamn, genre och produktionsår. Det finns 17K-filmer i datauppsättningen. Datauppsättningen introducerades i uppsatsen "S. Dooms, T. De Pessemier och L. Martens. MovieTweetings: en film rating datauppsättning samlas in från Twitter. Workshop om Crowdsourcing och human beräkning för Recommender Systems, CrowdRec på RecSys 2013."
  </td>
</tr>

<tr>
  <td>Iris två klassdata</td>
  <td>
Detta är kanske den mest kända databasen som finns i litteraturen om mönsterigenkänning. Datauppsättningen är relativt liten, innehållande 50 exempel vardera av kronbladsmätningar från tre irissorter.
<p></p>
<b>Användning:</b> Förutsäg iristypen från mätningarna.  
<p></p>
<b>Relaterad forskning:</b> Fisher, R.A. (1988). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Film Tweets</td>
  <td>
Datauppsättningen är en utökad version av datauppsättningen Movie Tweetings. Datauppsättningen har 170K betyg för filmer, utvinns ur välstrukturerade tweets på Twitter. Varje instans representerar en tweet och är en tuple: användar-ID, IMDB film-ID, betyg, tidsstämpel, antal favoriter för denna tweet, och antal retweets av denna tweet. Datauppsättningen gjordes tillgänglig av A. Said, S. Dooms, B. Loni och D. Tikk för Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td>MPG-data för olika bilar</td>
  <td>
Denna datauppsättning är en något modifierad version av datauppsättningen från StatLib-biblioteket vid Carnegie Mellon University. Datauppsättningen användes i 1983 American Statistical Association Exposition.
<p></p>
Uppgifterna listar bränsleförbrukning för olika bilar i miles per gallon. Den innehåller också information som antalet cylindrar, motorförskjutning, hästkrafter, totalvikt och acceleration.
<p></p>
<b>Användning:</b> Förutsäga bränsleekonomi baserat på tre flervärdesdisreattribut och fem kontinuerliga attribut. 
<p></p>
<b>Relaterad forskning:</b> StatLib, Carnegie Mellon-universitetet, (1993). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Pima Indians Diabetes binär klassificering dataset</td>
  <td>
En delmängd av data från National Institute of Diabetes och mag-och njursjukdomar databas. Datauppsättningen filtrerades för att fokusera på kvinnliga patienter av Pima indiska arv. Uppgifterna omfattar medicinska data såsom glukos och insulinnivåer, samt livsstilsfaktorer.
<p></p>
<b>Användning:</b> Förutsäg om ämnet har diabetes (binär klassificering). 
<p></p>
<b>Relaterad forskning:</b> Sigillito, V. (1990). UCI Machine <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml</a>Learning Repository " . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Kunddata för restaurang</td>
  <td>
En uppsättning metadata om kunder, inklusive demografi och preferenser.
<p></p>
<b>Användning:</b> Använd den här datauppsättningen, i kombination med de andra två restaurangdatauppsättningarna, för att träna och testa ett rekommendationssystem. 
<p></p>
<b>Relaterad forskning:</b> Bache, K. och Lichman, M. (2013). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Funktionersdata för restaurang</td>
  <td>
En uppsättning metadata om restauranger och deras funktioner, till exempel mattyp, matstil och plats.
<p></p>
<b>Användning:</b> Använd den här datauppsättningen, i kombination med de andra två restaurangdatauppsättningarna, för att träna och testa ett rekommendationssystem. 
<p></p>
<b>Relaterad forskning:</b> Bache, K. och Lichman, M. (2013). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Betyg på restaurang</td>
  <td>
Innehåller klassificeringar som ges av användare till restauranger på en skala från 0 till 2.
<p></p>
<b>Användning:</b> Använd den här datauppsättningen, i kombination med de andra två restaurangdatauppsättningarna, för att träna och testa ett rekommendationssystem. 
<p></p>
<b>Relaterad forskning:</b> Bache, K. och Lichman, M. (2013). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Datauppsättning av flera klasser i stål</td>
  <td>
Denna datauppsättning innehåller en serie poster från stålglödningsförsök. Den innehåller de fysiska attributen (bredd, tjocklek, typ (spole, plåt, etc.) av de resulterande ståltyperna.
<p></p>
<b>Användning:</b> Förutsäga något av två numeriska klassattribut. hårdhet eller styrka. Du kan också analysera korrelationer mellan attribut.
<p></p>
Stålsorter följer en fastställd standard, definierad av SAE och andra organisationer. Du letar efter ett visst betyg (klassvariabeln) och vill förstå de värden som behövs. 
<p></p>
<b>Relaterad forskning:</b> Sterling D. & Buntine, W. (NA). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information and Computer Science
<p></p>
En användbar guide till stålkvaliteter hittar du här:<a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleskopdata</td>
  <td>
Rekord av hög energi gammapartiklar spricker tillsammans med bakgrundsljud, båda simulerade med hjälp av en Monte Carlo-process.
<p></p>
Avsikten med simuleringen var att förbättra noggrannheten i markbaserade atmosfäriska Cherenkov gammateleskop. Detta görs med hjälp av statistiska metoder för att skilja mellan önskad signal (Cherenkov strålning duschar) och bakgrundsljud (hadronic duschar initieras av kosmiska strålar i den övre atmosfären).
<p></p>
Data har förbehandlats för att skapa ett avlångt kluster med den långa axeln är riktad mot kameracentret. Egenskaperna hos denna ellips (ofta kallade Hillas parametrar) är bland de bildparametrar som kan användas för diskriminering.
<p></p>
<b>Användning:</b> Förutsäga om bilden av en dusch representerar signal eller bakgrundsljud.
<p></p>
<b>Anmärkningar:</b> Enkel klassificeringsnoggrannhet är inte meningsfull för dessa data, eftersom det är värre att klassificera en bakgrundshändelse som signal. För jämförelse av olika klassificerare bör ROC-diagrammet användas. Sannolikheten för att acceptera en bakgrundshändelse som signal måste vara under ett av följande tröskelvärden: 0,01, 0,02, 0,05, 0,1 eller 0,2.
<p></p>
Observera också att antalet bakgrundshändelser (h, för hadronic duschar) underskattas. I verkliga mätningar representerar h- eller bullerklassen de flesta händelser. 
<p></p>
<b>Relaterad forskning:</b> Bock, R.K. (1995). UCI Machine <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>Learning Repository . Irvine, KALIFORNIEN: University of California, School of Information </td>
</tr>

<tr>
  <td>Väder Dataset</td>
  <td>
Landbaserade väderobservationer per timme från NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">sammanslagna data från 201304 till 201310</a>).
<p></p>
Väderdata omfattar observationer gjorda från flygplatsens väderstationer, som omfattar tidsperioden april-oktober 2013. Innan datauppsättningen laddades upp till Azure Machine Learning Studio (klassisk) bearbetades den enligt följande:
<ul>
  <li>Väderstations-ID har mappats till motsvarande flygplats-ID</li>
  <li>Väderstationer som inte är associerade med de 70 mest trafikerade flygplatserna filtrerades bort</li>
  <li>Kolumnen Datum delades upp i separata kolumner för år, månad och dag</li>
  <li>Följande kolumner valdes: AirportID, År, Månad, Dag, Tid, TimeZone, SkyCondition, Synlighet, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Höjdmätare</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Datauppsättning</td>
  <td>
Data härleds från<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>Wikipedia ( ) baserat på artiklar i varje S&P 500 företag, lagras som XML-data.
<p></p>
Innan datauppsättningen laddades upp till Azure Machine Learning Studio (klassisk) bearbetades den enligt följande:
<ul>
  <li>Extrahera textinnehåll för varje specifikt företag</li>
  <li>Ta bort wiki-formatering</li>
  <li>Ta bort icke-alfanumeriska tecken</li>
  <li>Konvertera all text till gemener</li>
  <li>Kända företagskategorier har lagts till</li>
</ul>
<p></p>
Observera att det inte gick att hitta en artikel för vissa företag, så antalet poster är mindre än 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Datauppsättningen innehåller kunddata och indikationer på deras svar på en direktutskickskampanj. Varje rad representerar en kund. Datauppsättningen innehåller nio funktioner om användardemografi och tidigare beteende och tre etikettkolumner (besök, konvertering och utgifter).  Besök är en binär kolumn som anger att en kund besökte efter marknadsföringskampanjen. Konverteringen indikerar att en kund har köpt något. Spendera är det belopp som spenderades.  Datauppsättningen gjordes tillgänglig av Kevin Hillstrom för MineThatData E-Mail Analytics and Data Mining Challenge.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funktioner i testexempel i RCV1-V2 Reuters nyhetsdatauppsättning. Datauppsättningen har 781K nyhetsartiklar tillsammans med sina ID (första kolumnen i datauppsättningen). Varje artikel är tokeniserad, stopworded och stemmed. Datauppsättningen gjordes tillgänglig av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funktioner för utbildning exempel i RCV1-V2 Reuters nyhetsdatauppsättning. Datauppsättningen har 23K nyhetsartiklar tillsammans med sina ID (första kolumnen i datauppsättningen). Varje artikel är tokeniserad, stopworded och stemmed. Datauppsättningen gjordes tillgänglig av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Datauppsättning från KDD Cup 1999 Knowledge Discovery and Data Mining Tools Competition (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Datauppsättningen hämtades och lagras i Azure Blob-lagring (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) och innehåller både utbildning och testning av datauppsättningar. Träningsdatauppsättningen har cirka 126K rader och 43 kolumner, inklusive etiketterna. Tre kolumner är en del av etikettinformationen och 40 kolumner, som består av numeriska och sträng/kategoriska funktioner, är tillgängliga för utbildning av modellen. Testdata har cirka 22,5 K testexempel med samma 43 kolumner som i träningsdata.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Ämne uppdrag för nyhetsartiklar i RCV1-V2 Reuters nyhetsdataset. En nyhetsartikel kan tilldelas flera ämnen. Formatet för varje rad&lt;är&gt; &lt;"ämnesnamndokument-ID&gt; 1". Datauppsättningen innehåller 2,6 M ämnestilldelningar. Datauppsättningen gjordes tillgänglig av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Dessa data kommer från KDD Cup 2010 Student prestanda utvärdering utmaning (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">student prestanda utvärdering</a>). De data som används är Algebra_2008_2009 utbildningsset (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Utmaning datauppsättning från KDD Cup 2010 Educational Data Mining Challenge. Hitta den på <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Datauppsättningen hämtades och lagras i Azure Blob-lagring (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) och innehåller loggfiler från ett studenthandledarsystem. De medföljande funktionerna inkluderar problem-ID och dess korta beskrivning, student-ID, tidsstämpel och hur många försök studenten gjorde innan du löser problemet på rätt sätt. Den ursprungliga datauppsättningen har 8.9M poster; Den här datauppsättningen har snurits ned till de första 100 K raderna. Datauppsättningen har 23 flikseparerade kolumner av olika slag: numerisk, kategorisk och tidsstämpel.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kickstarta dina experiment med exempel](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
