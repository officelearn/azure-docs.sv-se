---
title: Använd exemplen på datauppsättningar i Machine Learning Studio | Microsoft Docs
description: Beskrivningar av de datauppsättningar som används i exemplet modeller som ingår i Machine Learning Studio. Du kan använda dessa exempel på datauppsättningar för dina experiment.
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
ms.openlocfilehash: 725b7e1e37ffca38cf439cc62b053f13dfd6397d
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903038"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Använd exemplen på datauppsättningar i Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

När du skapar en ny arbetsyta i Azure Machine Learning, med ett antal provdatauppsättningar och experiment som standard. Många av dessa exempel på datauppsättningar som används av exemplet modeller i den [Azure AI-galleriet](http://gallery.cortanaintelligence.com/). Andra finns med exempel på olika typer av data som vanligtvis används i machine learning.

Vissa av dessa datauppsättningar är tillgängliga i Azure Blob storage. Följande tabell innehåller en direktlänk för dessa datauppsättningar. Du kan använda dessa datauppsättningar i dina experiment med hjälp av den [importdata] [ import-data] modulen.

Resten av dessa exempel på datauppsättningar som är tillgängliga i din arbetsyta under **sparade datauppsättningar**. Du hittar du på modulpaletten till vänster om arbetsytan för experimentet i Machine Learning Studio.
Du kan använda någon av de här datauppsättningarna i dina egna experiment genom att dra den till experimentets arbetsyta.


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

<table>

<tr>
  <th>Namn på datauppsättning</th>
  <th>Beskrivning av datauppsättning</th>
</tr>

<tr>
  <td>Vuxet insamlade binära Intäktsklassificering datauppsättning</td>
  <td>
En delmängd av 1994 insamlade databasen, med fungerande vuxna än 16 med ett index som justerad inkomst > 100.
<p></p>
<b>Användning:</b> klassificera personer som använder demografi för att förutsäga om en person grundats över 50 K per år.
<p></p>
<b>Relaterade Research:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap </td>
</tr>

<tr>
  <td>Flygplats koder datauppsättning</td>
  <td>
USA flygplats-koder.
<p></p>
Den här datauppsättningen innehåller en rad för varje USA flygplats, att tillhandahålla en flygplats-ID och namn tillsammans med den plats ort och delstat.
  </td>
</tr>

<tr>
  <td>Bil price data (Raw)</td>
  <td>
Information om bilar av märke och modell, inklusive priser, funktioner, till exempel antalet cylindrar och MPG, samt en försäkring riskpoäng.
<p></p>
Riskpoängen är inledningsvis associerad med automatisk pris. Den justeras sedan för faktiska risk i en process som är kända för aktuarier som symboling. Värdet + 3 anger att automatiskt är riskfyllda och ett värde för-3 att den är förmodligen säkert.
<p></p>
<b>Användning:</b> förutsäga riskpoäng med funktioner som använder regression eller multivariate klassificering. 
<p></p>
<b>Relaterade Research:</b> Schlimmer, J.C. (1987). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap </td>
</tr>

<tr>
  <td>Cykel hyra UCI datauppsättning</td>
  <td>
UCI cykel hyra datauppsättning som är baserad på verkliga data från kapital Bikeshare företag som underhåller ett cykel hyra nätverk i Washington DC.
<p></p>
Datauppsättningen har en rad för varje timme varje dag under 2011 och 2012, totalt 17,379 rader. Cykeluthyrning per timme intervallet är från 1 till 977.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB-bild</td>
  <td>
Offentligt tillgängliga bildfil konverteras till CSV-data.
<p></p>
Koden för att konvertera avbildningen finns i den <strong>färg kvantifieringsfel med K-Means klustring</strong> detaljsidan för modellen.
  </td>
</tr>

<tr>
  <td>Blod donation data</td>
  <td>
En delmängd av data från vinstsyfte Service Center Hsin Chu stad, Taiwan blod bidragsgivare-databas.
<p></p>
Bidragsgivare data inklusive månader sedan senaste donation), och frekvens, eller det totala antalet donationer, tid sedan senaste donation och mängden blod donerat.
<p></p>
<b>Användning:</b> målet är att förutsäga via klassificering om bidragsgivare donerat blod i mars 2007, där 1 anger en bidragsgivare under perioden som mål och 0 icke-bidragsgivare. 
<p></p>
<b>Relaterade Research:</b> Yeh, I.C., (2008). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap
<p></p>
Yeh, jag-Cheng, Yang, King Jang och cering, tagg-mobilnät, ”Knowledge discovery på NF modellen med Bernoulli sekvens,” Expert-system med program, 2008 <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Bröstet cancer data</td>
  <td>
En av tre cancer-relaterade datauppsättningar som tillhandahålls av Oncology Institute som visas ofta i machine learning-dokumentation. Kombinerar diagnostikinformation med funktioner från laboratorieanalys av ungefär 300 vävnadsprover.
<p></p>
<b>Användning:</b> klassificera datatypen för cancer, baserat på 9 attribut, vilket är linjär och vissa är kategoriska. 
<p></p>
<b>Relaterade Research:</b> Wohlberg, W.H., gata, W.N. och Mangasarian, O.L. (1995). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap </td>
</tr>

<tr>
  <td>Bröstet Cancer funktioner <td>
Datauppsättningen innehåller information för 102K misstänkta regioner (kandidater) X-ray avbildningar, beskrivs 117 för funktionerna. Funktionerna är tillverkarspecifika och deras innebörd visas inte datauppsättning skapare (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Bröstet Cancer Info</td>
  <td>
Datauppsättningen innehåller ytterligare information för varje misstänkt region av X-ray avbildning. Varje exempel innehåller information (till exempel etikett, patientens ID, koordinaterna för patch i förhållande till hela bilden) om motsvarande radnumret i datauppsättningen bröstet Cancer funktioner. Varje patients har ett antal exempel. Några exempel är positivt för patienter som har en cancer, och vissa är negativt. För patienter som inte har en cancer, är alla exempel negativt. Datauppsättningen har 102K exempel. Datauppsättningen är prioriterar 0,6% av punkterna är positivt, resten är negativt. Datauppsättningen har gjorts tillgängliga av Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>CRM begär etiketter delas</td>
  <td>
Etiketter från KDD Cup 2009 kunden relation förutsägelse utmaningen (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM omsättning etiketter delas</td>
  <td>
Etiketter från KDD Cup 2009 kunden relation förutsägelse utmaningen (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-datauppsättning som delas</td>
  <td>
Den här informationen kommer från KDD Cup 2009 kunden relation förutsägelse utmaningen (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Datauppsättningen innehåller 50K kunder från Franska Kostnadsuppföljning av företaget Orange. Varje kund har 230 maskerade funktioner, 190 som är numeriska och 40 är kategoriska. Funktionerna är mycket begränsad.
  </td>
</tr>

<tr>
  <td>CRM Upselling etiketter delas</td>
  <td>
Etiketter från KDD Cup 2009 kunden relation förutsägelse utmaningen (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Energieffektivitet Regression data</td>
  <td>
En samling av simulerade energi profiler, baserat på 12 annan byggnad former. Byggnaderna åtskiljs av åtta funktioner. Detta inkluderar glas området, rutor området distribution och orientering.
<p></p>
<b>Användning:</b> använda regression eller klassificering för att förutsäga den energieffektivitet omdöme baserat som en av två riktig värdefull svar. Flera klassificering, är i avrunda variabeln svar till närmaste heltal. 
<p></p>
<b>Relaterade Research:</b> Xifara A. & Tsanas A. (2012). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap </td>
</tr>

<tr>
  <td>Flight fördröjer Data</td>
  <td>
Passagerartrafik flygning i tid prestandadata som kommer från TranStats datainsamling i USA Department of Transportation (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">i tid</a>).
<p></p>
Datauppsättningen tar upp hur lång tid April – oktober 2013. Innan du skickar till Azure Machine Learning Studio, bearbetades datamängden på följande sätt:
<ul>
  <li>Datauppsättningen har filtrerats för att täcka endast de 70 mest använda flygplatserna i kontinentala USA</li>
  <li>Avbrutna flyg märkta som försenade på grund av mer än 15 minuter</li>
  <li>Och distribueras flyg filtrerades bort</li>
  <li>Följande kolumner har valts: år, månad, dag i månaden, DayOfWeek, operatör, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, avbruten</li>
</ul>
</td>
</tr>

<tr>
  <td>Flight punktlighetsinformation (Raw)</td>
  <td>
Poster flygplan flygning tillförsel och leveranser inom USA från oktober 2011.
<p></p>
<b>Användning:</b> förutsäga flygförseningar. 
<p></p>
<b>Relaterade Research:</b> från USA Avd. Transportmyndighet <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Data om skogsbränder</td>
  <td>
Innehåller weather-data, till exempel temperatur och fuktighet index och vindhastighet. Data hämtas från ett område i nordöstra Portugal, i kombination med poster i skogsbränder.
<p></p>
<b>Användning:</b> det här är en svårt regression uppgift om syftet är att förutsäga området brända skogsbränder. 
<p></p>
<b>Relaterade Research:</b> Cortez, P., & Morais A. (2008). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap
<p></p>
[Cortez och Morais 2007] S. Cortez och A. Morais. En Data Mining metod för att förutsäga skogsbränder med hjälp av Meteorological Data. I J. Neves, M. F. Santos och J. Machado Eds., nya trender i artificiell intelligens, handlingar från 13 EPIA 2007 - portugisiska konferens på artificiell intelligens, December, Guimarães, Portugal, sidor 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Tillgängligt på: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Tyska kreditkort UCI datauppsättning</td>
  <td>
UCI Statlog (tyska kreditkort) datauppsättningen (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + tyska + kredit + Data</a>), med hjälp av german.data-filen.
<p></p>
Datauppsättningen klassificerar personer, som beskrivs av en uppsättning attribut som låg eller hög kreditrisker. Varje exempel representerar en person. Det finns 20 funktioner, både numeriska och kategoriska, och en binär etikett (kredit risk värde). Hög kredit risk poster har etikett = 2, låg kredit risk poster har etikett = 1. Kostnaden för misclassifying ett lågrisk-exempel som hög är 1, kostnaden för misclassifying en hög risk exempel så låga är 5.
  </td>
</tr>

<tr>
  <td>IMDB text</td>
  <td>
Datauppsättningen innehåller information om filmer som har klassificerat i Twitter tweets: IMDB film-ID, filmens, genre och produktionsår. Det finns 17K filmer i datauppsättningen. Datauppsättningen introducerades i dokumentet ”S. Dooms, T. De Pessemier och L. Martens. MovieTweetings: en film omdöme datamängden som samlas in från Twitter. Workshop om gemensamt skapade och mänsklig beräkning för rekommenderare system, CrowdRec vid RecSys 2013 ”.
  </td>
</tr>

<tr>
  <td>Iris två klass-data</td>
  <td>
Detta är kanske mest känd databasen ska finnas i mönstret för dokumentation. Datauppsättningen är relativt liten, som innehåller 50 exempel varje på petal mått från tre iris sorterna.
<p></p>
<b>Användning:</b> förutsäga iris-typen från mätningar.  
<p></p>
<b>Relaterade Research:</b> Fisher R.A. (1988). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap </td>
</tr>

<tr>
  <td>Film Tweets</td>
  <td>
Datauppsättningen är en utökad version av film Tweetings datauppsättningen. Datauppsättningen har 170K klassificeringar för filmer, extraheras från välstrukturerade tweets på Twitter. Varje instans som representerar en tweet och är en tuppel: användar-ID, IMDB film-ID, klassificering, tidsstämpel, antalet Favoriter för den här tweet och antal retweets av den här tweet. Datauppsättningen har gjorts tillgängliga av A. SA, S. Dooms, B. Loni och D. Tikk för rekommenderare system utmaning 2014.
  </td>
</tr>

<tr>
  <td>MPG-data för olika bilar</td>
  <td>
Den här datauppsättningen är en något justerad version av datauppsättningen som tillhandahålls av StatLib bibliotek med Carnegie Mellon University. Datauppsättningen senast användes i 1983 American statistiska Association tillverkning.
<p></p>
Data visas i olika bilar i miles per gallon samband med bränsle. Den innehåller också information som antalet cylindrar, motorn Förskjutning, hästkrafter, total vikt och acceleration.
<p></p>
<b>Användning:</b> förutsäga bränsleekonomin baserat på tre diskreta flervärdesattribut och fem kontinuerliga attribut. 
<p></p>
<b>Relaterade Research:</b> StatLib Carnegie Mellon University (1993). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap </td>
</tr>

<tr>
  <td>Pima människor Diabetes binär klassificering datauppsättning</td>
  <td>
En delmängd av data från National Institute av Diabetes och mag och lever sjukdomar. Datauppsättningen har filtrerats för att fokusera på kvinnliga patienter av Pima indiska arv. Innehåller information som medicinska data, till exempel glukos och inulinsirap nivåer samt livsstil faktorer.
<p></p>
<b>Användning:</b> förutsäga om ämnet har diabetes (binär klassificering). 
<p></p>
<b>Relaterade Research:</b> Sigillito, V. (1990). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml”</a>. Irvine, CA: University of California, av skolinformation och datavetenskap </td>
</tr>

<tr>
  <td>Restaurang kunddata</td>
  <td>
En uppsättning metadata om kunder, inklusive demografi och inställningar.
<p></p>
<b>Användning:</b> använder den här datauppsättningen i kombination med de andra två restaurang datauppsättningar, träna och testa ett rekommenderare system. 
<p></p>
<b>Relaterade Research:</b> Bache, K. och Lichman, M. (2013). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap.
  </td>
</tr>

<tr>
  <td>Restaurang funktionsdata</td>
  <td>
En uppsättning metadata om restauranger och deras funktioner, till exempel mat typ, äta middag format och plats.
<p></p>
<b>Användning:</b> använder den här datauppsättningen i kombination med de andra två restaurang datauppsättningar, träna och testa ett rekommenderare system. 
<p></p>
<b>Relaterade Research:</b> Bache, K. och Lichman, M. (2013). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap.
  </td>
</tr>

<tr>
  <td>Restaurang betyg</td>
  <td>
Innehåller klassificeringar som anges av användarna restauranger på en skala från 0 till 2.
<p></p>
<b>Användning:</b> använder den här datauppsättningen i kombination med de andra två restaurang datauppsättningar, träna och testa ett rekommenderare system. 
<p></p>
<b>Relaterade Research:</b> Bache, K. och Lichman, M. (2013). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap.
  </td>
</tr>

<tr>
  <td>Stål Annealing flera datauppsättning</td>
  <td>
Den här datauppsättningen innehåller en serie poster från stål glödgning utvärderingsversioner. Den innehåller de fysiska egenskaperna (bredd, tjocklek, typ (slutet, blad osv.) i den nya stål typer.
<p></p>
<b>Användning:</b> förutsäga något av två numeriska klassattribut; hårdhet eller styrka. Du kan också analysera korrelationer mellan attribut.
<p></p>
Stål betyg följer en set-standard definieras av SAE och andra organisationer. Du letar efter en specifik ”i företagsklass” (Klassvariabeln) och vill förstå de värden som behövs. 
<p></p>
<b>Relaterade Research:</b> Sterling, D. & Buntine, W. (NA). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation och datavetenskap
<p></p>
En användbar guide till stål betyg finns här: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Teleskopet data</td>
  <td>
Post med hög energi gamma partikeln toppar tillsammans med bakgrundsljud både simulerade Använd Monte Carlo-process.
<p></p>
Syftet med simuleringen var att förbättra marken atmospheric Cherenkov gamma teleskop. Detta görs med hjälp av statistiska metoder för att skilja mellan den önskade signalen (Cherenkov strålning duschar) och bakgrundsljud (hadronic duschar initieras av cosmic strålar i luften på övre).
<p></p>
Data har bearbetats för att skapa ett avlångt kluster med långt axel är inriktad på kamera center. Egenskaperna för den här ellips (kallas ofta Hillas parametrar) är bland de bildparametrar som kan användas för diskriminering.
<p></p>
<b>Användning:</b> förutsäga om avbildning av en representerar signal eller bakgrund bruset.
<p></p>
<b>Obs:</b> enkel klassificering är inte meningsfullt för dessa data sedan klassificera en händelse i bakgrunden eftersom signalen är sämre än klassificera en signalhändelse som bakgrund. Jämförelse av olika klassificerare ska ROC-diagrammet användas. Sannolikheten för att acceptera en händelse i bakgrunden som signalen måste vara under följande tröskelvärden: 0,01, 0,02, 0,05, 0,1 eller 0,2.
<p></p>
Observera också att antalet händelser som bakgrund (för hadronic duschar h) är överskattade. I verkliga mått representerar klassen h eller brus merparten av händelser. 
<p></p>
<b>Relaterade Research:</b> Bock, R.K. (1995). UCI Machine Learning-lagringsplatsen <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: University of California, av skolinformation </td>
</tr>

<tr>
  <td>Väder-datauppsättning</td>
  <td>
Per timme mark-baserade väder observationer från amerikanska NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">samman data från 201304 till 201310</a>).
<p></p>
Väderdata täcker observationer som görs från en flygplats väder stationer, som omfattar tidsperioden April – oktober 2013. Innan du skickar till Azure Machine Learning Studio, bearbetades datamängden på följande sätt:
<ul>
  <li>-Väderstation, ID: N har mappats till motsvarande flygplats ID: N</li>
  <li>Väder stationer som inte är associerade med 70 mest använda flygplatser filtrerades bort</li>
  <li>Datumkolumnen är uppdelad i separata kolumner för år, månad och dag</li>
  <li>Följande kolumner har valts: AirportID, år, månad, dag, tid, tidszon, SkyCondition, synlighet, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, Vindhastigheten, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 datauppsättning</td>
  <td>
Data hämtas från Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) baserat på artiklar på varje S & P 500 företag, lagras som XML-data.
<p></p>
Innan du skickar till Azure Machine Learning Studio, bearbetades datamängden på följande sätt:
<ul>
  <li>Extrahera textinnehåll för varje företag</li>
  <li>Ta bort wiki formatering</li>
  <li>Ta bort icke-alfanumeriska tecken</li>
  <li>Omvandla alla text till gemener</li>
  <li>Kända företagets kategorier har lagts till</li>
</ul>
<p></p>
Observera att för vissa företag som en artikel kunde inte hittas så att antalet poster som är mindre än 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Datauppsättningen innehåller kunddata och uppgifter om deras svar på en direkt e kampanj. Varje rad representerar en kund. Datauppsättningen innehåller nio funktioner om demografi för användaren och de senaste beteende och tre märka kolumner (besöker, konvertering och spendera).  Gå till är en binary-kolumn som anger att en kund besökt efter marknadsföringskampanjen. Konverteringen anger en kund köpt något. Spendera är den som användes för att.  Datauppsättningen har gjorts tillgängliga av Kevin Hillstrom för MineThatData e-Analytics och Data Mining utmaning.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funktioner i testet exemplen i RCV1 V2 Reuters nyheter datauppsättningen. Datauppsättningen har 781K nyhetsartiklar tillsammans med deras ID (första kolumnen i datauppsättningen). Varje artikel är tokeniserad stopworded, och berodde. Datauppsättningen har gjorts tillgängliga av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funktioner i utbildning exemplen i RCV1 V2 Reuters nyheter datauppsättningen. Datauppsättningen har 23K nyhetsartiklar tillsammans med deras ID (första kolumnen i datauppsättningen). Varje artikel är tokeniserad stopworded, och berodde. Datauppsättningen har gjorts tillgängliga av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Datauppsättning från KDD Cup 1999 Knowledge Discovery and Data-utvinning Tools konkurrens (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Datauppsättningen har hämtats och lagrats i Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) och innehåller både för träning och testning datauppsättningar. Datauppsättning för träning har cirka 126K rader och 43 kolumner, inklusive etiketter. Tre kolumner är en del av etikettinformationen om och 40 kolumner, som består av strängen/kategoriska och numeriska funktioner är tillgängliga för att träna modellen. Test har cirka 22,5 K testa exempel med samma 43 kolumner som i träningsdata.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Avsnittet tilldelningar efter nyhetsartiklar i RCV1 V2 Reuters nyheter datauppsättningen. En nyhetsartikel kan tilldelas till flera avsnitt. Formatet för varje rad är ”&lt;ämnesnamn&gt; &lt;dokument-id&gt; 1”. Datauppsättningen innehåller 2,6 miljoner avsnittet tilldelningar. Datauppsättningen har gjorts tillgängliga av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Den här informationen kommer från KDD Cup 2010 Student prestanda utvärdering utmaningen (<a href="http://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">student prestandautvärdering</a>). De data som används är träningsmängden Algebra_2008_2009 (Stamper, J., Niculescu-Mizil A., Ritter, S. Gordon, G.J. och Koedinger K.R. (2010). Algebra jag 2008-2009. Utmana datauppsättning från KDD Cup 2010 Educational Data Mining utmaning. Hitta den på <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Datauppsättningen har hämtats och lagrats i Azure Blob storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) och innehåller loggfiler från student tutoring system. De angivna funktionerna inkluderar problem-ID och en kort beskrivning, student-ID, tidsstämpel och hur många försök elevens gjort innan du lösa problemet på rätt sätt. Den ursprungliga datauppsättningen innehåller poster för 8.9M; den här datauppsättningen har varit samplas ned till de första 100K raderna. Datauppsättningen har 23 tabbavgränsade kolumner med olika typer: numeriska, kategoriska, och tidsstämpel.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
