---
title: Använd provdatauppsättningar i Machine Learning Studio | Microsoft Docs
description: Beskrivningar av de datauppsättningar som används i Exempelmodeller som ingår i Machine Learning Studio. Du kan använda dessa provdatauppsättningar för dina experiment.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 03a0b844-e8a7-4896-996f-d3c7a0db7a50
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.openlocfilehash: 5e225a16baef53c12a5d0a719c36a2042d391c7f
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959110"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Använd exemplen på datauppsättningar i Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

När du skapar en ny arbetsyta i Azure Machine Learning med ett antal provdatauppsättningar och experiment som standard. Många av dessa provdatauppsättningar som används av Exempelmodeller i den [Azure AI-galleriet](http://gallery.cortanaintelligence.com/). Andra ingår som exempel på olika typer av data som vanligtvis används i machine learning.

Vissa av dessa data är tillgängliga i Azure Blob storage. Följande tabell innehåller en direktlänk för dessa datamängder. Du kan använda dessa data i dina experiment med hjälp av den [importera Data] [ import-data] modul.

Resten av dessa provdatauppsättningar är tillgängliga i arbetsytan under **sparade datauppsättningar**. Du hittar du på modulpaletten till vänster om arbetsytan för experimentet i Machine Learning Studio.
Du kan använda någon av dessa data i dina egna experiment genom att dra den till din experimentet.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th>Namn på datauppsättning</th>
  <th>DataSet-beskrivning</th>
</tr>

<tr>
  <td>Vuxna inventering intäkter binär klassificering dataset</td>
  <td>
En delmängd av 1994 inventering databasen, med fungerande vuxna än 16 med ett index som justerade intäkter > 100.
<p></p>
<b>Användning:</b> klassificera personer som använder demografi för att förutsäga om en person får än 50 kB per år.
<p></p>
<b>För forskning:</b> Kohavi R., Becker B., (1996). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap </td>
</tr>

<tr>
  <td>Flygplats koder Dataset</td>
  <td>
USA flygplats koder.
<p></p>
Den här datauppsättningen innehåller en rad för varje USA flygplats, vilket ger en flygplats-ID och namn och plats orten och staten.
  </td>
</tr>

<tr>
  <td>Bil price data (Raw)</td>
  <td>
Information om bilar av märke och modell, inklusive pris, funktioner, till exempel antalet cylindrar och MPG samt en insurance riskpoäng.
<p></p>
Riskpoäng associeras ursprungligen med automatisk pris. Den justeras sedan för faktiska risk i en process som är kända för aktuarier som symboling. + 3 värdet anger att automatisk är riskfyllda och ett värde av-3 som den är förmodligen.
<p></p>
<b>Användning:</b> förutsäga riskpoäng av funktioner som använder regression eller multivariate klassificering. 
<p></p>
<b>För forskning:</b> Schlimmer, J.C. (1987). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap </td>
</tr>

<tr>
  <td>Cykel uthyrning UCI dataset</td>
  <td>
UCI cykel uthyrning dataset som bygger på verkliga data från kapital Bikeshare företag som upprätthåller en cykel uthyrning nätverk i Washington DC.
<p></p>
Dataset innehåller en rad för varje timme av varje dag under 2011 och 2012, med totalt 17,379 rader. Varje timme cykel hyra intervallet är från 1 till 977.

  </td>
</tr>

<tr>
  <td>Faktura Gates RGB-bild</td>
  <td>
Offentligt tillgängliga avbildningsfil konverteras till CSV-data.
<p></p>
Koden för att konvertera avbildningen har angetts i den <strong>färg kvantifieringsfel via K-Means kluster</strong> modellen detaljsida.
  </td>
</tr>

<tr>
  <td>Blod bidrag data</td>
  <td>
En delmängd av data från vinstsyfte Service Center Hsin Chu ort, Taiwan blod bidragsgivare-databas.
<p></p>
Bidragsgivare data innehåller månader sedan senaste bidrag), och frekvens, eller det totala antalet bidrag, tid sedan senaste bidrag och mängden blod donerat.
<p></p>
<b>Användning:</b> målet är att förutsäga via klassificering om bidragsgivare donerat blod i mars 2007, där 1 anger en bidragsgivare under perioden mål och 0 icke-bidragsgivare. 
<p></p>
<b>För forskning:</b> Yeh I.C., (2008). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap
<p></p>
Yeh, jag-Cheng, Yang, bestämmer Jang och Ting, Tao-Ming ”Knowledge identifiering i läget Nedsatt funktionalitet modellen med Bernoulli-sekvens,” Expert system med program, 2008 <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Bröstet bröstcancerdiagnoser data</td>
  <td>
En av tre bröstcancerdiagnoser-relaterade datamängder som tillhandahålls av Oncology Institute som förekommer ofta i machine learning dokumentation. Kombinerar diagnostikinformation med funktioner från laboratorieanalys av ungefär 300 vävnadsprover.
<p></p>
<b>Användning:</b> klassificera typ av bröstcancerdiagnoser, baserat på 9 attribut, vilket är linjär och vissa är kategoriska. 
<p></p>
<b>För forskning:</b> Wohlberg, W.H., gata, W.N. och Mangasarian, O.L. (1995). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap </td>
</tr>

<tr>
  <td>Bröstet Bröstcancerdiagnoser funktioner <td>
Dataset innehåller information om 102K misstänkta områden (kandidater) med röntgen bilder, beskrivs av 117 funktioner. Funktionerna är tillverkarspecifika och deras innebörd avslöjas inte av dataset skapare (Siemens hälsovård). 
  </td>
</tr>

<tr>
  <td>Bröstet Bröstcancerdiagnoser Info</td>
  <td>
Dataset innehåller ytterligare information för varje misstänkt region röntgenbilden. Varje exempel innehåller information (till exempel etikett, patient ID, koordinaterna för korrigering i förhållande till att hela bilden) om motsvarande radnumret i datauppsättningen bröstet Bröstcancerdiagnoser funktioner. Varje tålamod har ett antal exempel. Några exempel är positivt för patienter som har en bröstcancerdiagnoser, och vissa är negativt. Patienter som inte har en bröstcancerdiagnoser är alla exempel negativt. Datamängden har 102K exempel. Dataset är prioriterar 0,6% av pekar är positivt, resten är negativt. Datamängden har gjorts tillgängliga av Siemens hälsovård.
  </td>
</tr>

<tr>
  <td>CRM Appetency etiketter delade</td>
  <td>
Etiketterna från KDD Cup 2009 customer relationship förutsägelse challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM omsättning etiketter delade</td>
  <td>
Etiketterna från KDD Cup 2009 customer relationship förutsägelse challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM datamängden delas</td>
  <td>
Den här informationen kommer från KDD Cup 2009 customer relationship förutsägelse challenge (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Dataset innehåller 50K kunder från Franska Telecom företaget Orange. Varje kund har 230 anonymiserade funktioner, 190 som är numeriska och 40 är kategoriska. Funktionerna är mycket sparse.
  </td>
</tr>

<tr>
  <td>CRM Upselling etiketter delade</td>
  <td>
Etiketterna från KDD Cup 2009 customer relationship förutsägelse challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Energieffektivitet Regression data</td>
  <td>
En samling av simulerade energi profiler, baserat på 12 skapande av olika former. Byggnader särskiljs med hjälp av åtta funktioner. Detta inkluderar rutor område, rutor området distribution och orientering.
<p></p>
<b>Användning:</b> använda regression eller klassificering för att förutsäga den energieffektivitet klassificeringen baserat som en av två riktigt värdefulla svar. Flera klassen klassificering, är i avrunda variabeln svar till närmaste heltal. 
<p></p>
<b>För forskning:</b> Xifara A. & Tsanas A. (2012). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap </td>
</tr>

<tr>
  <td>Svarta fördröjningar Data</td>
  <td>
Passagerare som rör sig i tid prestandadata från TranStats datainsamling för USA Avdelning transport (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">i tid</a>).
<p></p>
Dataset innehåller tidsperiod April oktober 2013. Innan du skickar till Azure Machine Learning Studio bearbetades datamängden på följande sätt:
<ul>
  <li>Dataset filtrerades så att den täcker endast de 70 mest använda flygplatserna i kontinentala USA</li>
  <li>Avbrutna flygplan har märkt med skjutas upp med mer än 15 minuter</li>
  <li>Distribueras flygplan har filtrerats ut</li>
  <li>Följande kolumner har valts: år, månad, dag i månaden, DayOfWeek, operatör, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, avbruten</li>
</ul>
</td>
</tr>

<tr>
  <td>Rör sig i tid prestanda (Raw)</td>
  <td>
Registrerar flygplan svarta mottagna och avvikelser i USA från oktober 2011.
<p></p>
<b>Användning:</b> förutsäga svarta fördröjningar. 
<p></p>
<b>För forskning:</b> från USA Avd. transporten <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Data om skogsbränder</td>
  <td>
Innehåller väder data, till exempel temperatur- och fuktighetskonsekvens index och vindhastigheten. Data hämtas från ett område i nordöstra Portugal, tillsammans med uppgifter om skogsbränder.
<p></p>
<b>Användning:</b> detta är en svårt regression uppgift om syftet är att förutsäga området brända skogsbränder. 
<p></p>
<b>För forskning:</b> Cortez P., & Morais A. (2008). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap
<p></p>
[Cortez och Morais 2007] S. Cortez och A. Morais. En Data Mining metod för att förutsäga skogsbränder med meteorologiska Data. I J. Neves, m-F. Santos och J. Machado Eds., nya trender i styrs av datorn, förfaranden 13 EPIA 2007 - portugisiska konferens om styrs av datorn, December, 523-Guimarães Portugal sidor 512, 2007. APPIA ISBN 13 978-989-95618-0-9. Finns på: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Tyska kreditkort UCI dataset</td>
  <td>
UCI Statlog (tyska kreditkort) datauppsättningen (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + tyska + kredit + Data</a>), med hjälp av filen german.data.
<p></p>
Dataset klassificerar personer beskrivs av en uppsättning attribut som låg eller hög kreditrisker. Varje exempel representerar en person. Det finns 20 funktioner, både numeriska och kategoriska, och en binär etikett (kredit risk värdet). Hög kredit risk poster har etikett = 2, låg kredit risk poster har etikett = 1. Kostnaden för misclassifying ett exempel på låg risk som hög är 1, kostnaden för misclassifying en hög risk exempel så låga är 5.
  </td>
</tr>

<tr>
  <td>IMDB text</td>
  <td>
Dataset innehåller information om filmer som har klassificerat i Twitter tweets: IMDB film ID, film namn, genre och produktionsår. Det finns 17 kB filmer i datauppsättningen. Dataset introducerades i dokumentet ”S. Dooms, T. Tyskland Pessemier och L. Martens. MovieTweetings: en film klassificeringen Dataset som samlas in från Twitter. Workshop på gemensamt skapade och mänsklig beräkning för rekommenderare CrowdRec vid RecSys 2013 ”.
  </td>
</tr>

<tr>
  <td>Iris två klassdata</td>
  <td>
Detta är kanske bästa kända databasen ska finnas i mönstret recognition dokumentation. Dataset är relativt små som innehåller 50 exempel varje på bladet mått från tre iris sorter.
<p></p>
<b>Användning:</b> förutsäga iris typen från mätningarna.  
<p></p>
<b>För forskning:</b> Fisher R.A. (1988). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap </td>
</tr>

<tr>
  <td>Film Tweets</td>
  <td>
Dataset är en utökad version av film Tweetings dataset. Datamängden har 170K klassificering för filmer, extraheras från väl strukturerade tweets på Twitter. Varje instans representerar en tweet och är en tuppel: användar-ID, IMDB film ID, klassificering, timestamp, antalet för den här tweet och antalet retweets för den här tweet. Datamängden har gjorts tillgängliga av A. SA, S. Dooms, B. Loni och D. Tikk för rekommenderare system Challenge 2014.
  </td>
</tr>

<tr>
  <td>MPG-data för olika bilar</td>
  <td>
Den här datauppsättningen utgör en lite annorlunda version av den dataset som tillhandahålls av StatLib bibliotek med Carnegie Mellon University. Dataset användes i 1983 American statistiska Association tillverkning.
<p></p>
Data visar bränsleförbrukningen för olika bilar i miles per gallon. Den innehåller också information som antalet cylindrar, motorn Förskjutning, hästkrafter, total vikt och acceleration.
<p></p>
<b>Användning:</b> förutsäga bränsleekonomi baserat på tre separata flervärdesattribut och fem kontinuerliga attribut. 
<p></p>
<b>För forskning:</b> StatLib Carnegie Mellon University (1993). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap </td>
</tr>

<tr>
  <td>Pima människor Diabetes binär klassificering dataset</td>
  <td>
En delmängd av data från National Institute Diabetes och mag och lever sjukdomar databasen. Dataset filtrerades kan fokusera på hondjur patienter av Pima indiska arv. Informationen omfattar medicinska data, till exempel glukos och inulinsirap nivåer samt lifestyle faktorer.
<p></p>
<b>Användning:</b> förutsäga om ämnet har diabetes (binär klassificering). 
<p></p>
<b>För forskning:</b> Sigillito, V. (1990). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml”</a>. Irvine, CA: University of California, skola Information och datorvetenskap </td>
</tr>

<tr>
  <td>Restaurang kundinformation</td>
  <td>
En uppsättning metadata om kunder, inklusive demografisk information och inställningar.
<p></p>
<b>Användning:</b> använder den här datauppsättningen i kombination med de andra två restaurang datauppsättningar, att träna och testa ett rekommenderare system. 
<p></p>
<b>För forskning:</b> Bache K. och Lichman M. (2013). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap.
  </td>
</tr>

<tr>
  <td>Restaurang funktionsdata</td>
  <td>
En uppsättning metadata om hotell och deras funktioner, till exempel mat typ, äta middag format och plats.
<p></p>
<b>Användning:</b> använder den här datauppsättningen i kombination med de andra två restaurang datauppsättningar, att träna och testa ett rekommenderare system. 
<p></p>
<b>För forskning:</b> Bache K. och Lichman M. (2013). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap.
  </td>
</tr>

<tr>
  <td>Restaurang klassificeringar</td>
  <td>
Innehåller klassificeringar som angetts av användare till hotell på en skala från 0 till 2.
<p></p>
<b>Användning:</b> använder den här datauppsättningen i kombination med de andra två restaurang datauppsättningar, att träna och testa ett rekommenderare system. 
<p></p>
<b>För forskning:</b> Bache K. och Lichman M. (2013). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap.
  </td>
</tr>

<tr>
  <td>Stål Annealing flera klassen dataset</td>
  <td>
Den här datauppsättningen innehåller en serie av poster från stål glödgning försök. Den innehåller fysiska attribut (bredd, tjocklek, typ (slutet, blad, etc.) av den resulterande stål typer.
<p></p>
<b>Användning:</b> förutsäga något av två numeriska klassattribut; hårdhet eller styrka. Du kan också analysera visar sambandet mellan attribut.
<p></p>
Stålsorter följer en set-standard definieras av SAE och andra organisationer. Du letar efter en specifik 'klass' (Klassvariabeln) och vill förstå de värden som behövs. 
<p></p>
<b>För forskning:</b> pund, D. & Buntine, W. (NA). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information och datorvetenskap
<p></p>
En användbar guide till stål betyg finns här: <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleskopet data</td>
  <td>
Post med hög energi gamma partikel belastning tillsammans med bakgrundsljud både simulerade med hjälp av en Monte Carlo process.
<p></p>
Syftet med simuleringen var att förbättra grunden-baserade luften Cherenkov gamma teleskop. Detta görs med hjälp av statistiska metoder för att skilja mellan den önskade signalen (Cherenkov strålning duschar) och bakgrundsljud (hadronic duschar initieras av cosmic strålar i övre luften).
<p></p>
Data har bearbetats för att skapa ett avlångt kluster med långt axeln är inriktad på kamera center. Egenskaperna för den här ellips (kallas ofta Hillas parametrar) är bland parametrarna avbildning som kan användas för diskriminering.
<p></p>
<b>Användning:</b> förutsäga om representerar bild av en signal eller bakgrunden brus.
<p></p>
<b>Kommentarer:</b> enkel klassificering noggrannhet är inte relevant för dessa data sedan klassificera en händelse i bakgrunden som signalen är lägre än klassificera en signalhändelse som bakgrund. Jämförelse av olika klassificerare ska ROC-diagram användas. Sannolikheten för att ta emot en händelse i bakgrunden som signal måste vara under följande tröskelvärden: 0,01 0,02, 0,05, 0,1 eller 0,2.
<p></p>
Observera också att antalet händelser som bakgrund (för hadronic duschar h) underskattar. Klassen h eller brus representerar merparten av händelser i verkliga mått. 
<p></p>
<b>För forskning:</b> Bock, R.K. (1995). UCI Machine Learning databasen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, skola Information </td>
</tr>

<tr>
  <td>Väder Dataset</td>
  <td>
Varje timme mark-baserade väder observationer från NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">samman data från 201304 till 201310</a>).
<p></p>
Väder data omfattar observationer från flygplats väder stationer, som omfattar tidsperioden April oktober 2013. Innan du skickar till Azure Machine Learning Studio bearbetades datamängden på följande sätt:
<ul>
  <li>Väder station ID mappades till motsvarande flygplats ID: N</li>
  <li>Väder stationer som inte är associerade med de mest använda 70 flygplatserna har filtrerats ut</li>
  <li>Kolumnen är uppdelad i separata år, månad och dag kolumner</li>
  <li>Följande kolumner har valts: AirportID, år, månad, dag, tid, tidszon, SkyCondition, synlighet, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, Vindhastigheten, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
Data hämtas från Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) baserat på artiklar för varje företag S & P 500 lagras som XML-data.
<p></p>
Innan du skickar till Azure Machine Learning Studio bearbetades datamängden på följande sätt:
<ul>
  <li>Extrahera textinnehåll för varje företag</li>
  <li>Ta bort wiki formatering</li>
  <li>Ta bort icke-alfanumeriska tecken</li>
  <li>Omvandla alla text till gemener</li>
  <li>Kända företagets kategorier har lagts till</li>
</ul>
<p></p>
Observera att för vissa företag en artikel kunde inte hittas så att antalet poster som är mindre än 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Dataset innehåller kunddata och uppgifter om deras svar på en direkt e kampanj. Varje rad representerar en kund. Dataset innehåller nio funktioner om användaren demografi och tidigare beteende och tre etiketten kolumner (Besök konvertering och ägna).  Besök är en binary-kolumn som anger att en kund besökt efter marknadsföringskampanjen. Konvertering anger en kund köpt något. Ägna är den som har använt.  Datamängden har gjorts tillgängliga av Kevin Hillstrom för MineThatData e-Analytics och Data Mining utmaning.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funktioner i test exemplen i RCV1 V2 Reuters nyheter dataset. Datamängden har 781K nyhetsartiklar tillsammans med deras ID. (första kolumnen i datauppsättningen). Varje artikel tokeniserad stopworded, och berodde. Datamängden har gjorts tillgängliga av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funktioner i utbildning exemplen i RCV1 V2 Reuters nyheter dataset. Datamängden har 23K nyhetsartiklar tillsammans med deras ID. (första kolumnen i datauppsättningen). Varje artikel tokeniserad stopworded, och berodde. Datamängden har gjorts tillgängliga av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
DataSet från KDD Cup 1999 Knowledge identifiering och datautvinning verktyg konkurrens (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Datamängden har hämtats och lagrats i Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) och innehåller både träning och testning datauppsättningar. Utbildning datamängden har cirka 126K rader och 43 kolumner, inklusive etiketter. Tre kolumner är en del av etikettinformationen om och 40 kolumner, som består av strängen kategoriska och numeriska funktioner är tillgängliga för att träna modellen. Testdata har cirka 22,5 K testa exempel med samma 43 kolumner som utbildning-data.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Avsnittet om porttilldelningar för nyhetsartiklar i RCV1 V2 Reuters nyheter dataset. En nyhetsartikel i kan tilldelas till flera avsnitt. Formatet på varje rad är ”&lt;avsnittsnamn&gt; &lt;dokument-id&gt; 1”. Dataset innehåller 2.6M avsnittet tilldelningar. Datamängden har gjorts tillgängliga av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Den här informationen kommer från KDD Cup 2010 Student prestanda utvärdering challenge (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">student prestandautvärdering</a>). De data som används är träningsmängden Algebra_2008_2009 (Stamper, J., Niculescu-Mizil A. Ritter, S. Gordon, G.J. och Koedinger K.R. (2010). Algebra jag 2008-2009. Challenge dataset från KDD Cup 2010 utbildningssyfte Data Mining utmaning. Hitta den på <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Datamängden har hämtats och lagrats i Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) och innehåller loggfilerna från en student tutoring system. De angivna funktionerna inkluderar problem-ID och dess kort beskrivning, student ID, tidsstämpel och hur många försök student innan lösa problemet på rätt sätt. Den ursprungliga datauppsättningen har 8.9M poster. den här datauppsättningen har provtagning ned till de första 100K raderna. Datamängden har 23 tabbavgränsade kolumner med olika typer: numeriska, kategoriska, och tidsstämpel.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
