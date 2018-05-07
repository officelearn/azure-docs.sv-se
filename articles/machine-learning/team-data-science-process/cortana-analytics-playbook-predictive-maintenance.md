---
title: Azure AI Playbook för förutsägande Underhåll lösningar | Microsoft Docs
description: En fullständig beskrivning av datavetenskap som används av förutsägande Underhåll lösningar i flera branscher.
services: cortana-analytics
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: fboylu
ms.openlocfilehash: ad06617fb8c14928dca7d9ce18ad86190e8255fe
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="azure-ai-playbook-for-predictive-maintenance-solutions"></a>Azure AI Playbook för förutsägande Underhåll lösningar 
## <a name="executive-summary"></a>Sammanfattning
Förutsägande Underhåll är en av de mest efterfrågade program i förutsägelseanalyser unarguable fördelar, inklusive enorm mängden besparingar. Den här playbook syftar till att tillhandahålla en referens för förutsägande Underhåll lösningar med betoning på viktiga användningsområden.
De är beredda att ge läsaren en förståelse av de vanligaste affärsscenarier av förutsägande Underhåll utmaningar över kvalificerande affärsproblem dessa lösningar kan data som krävs för att lösa dessa problem i verksamheten, tekniker för förutsägelsemodellering till skapa lösningar med exempel lösningsarkitekturer sådana data och bästa praxis.
Beskriver också egenskaperna för förutsägelsemodeller utvecklas, till exempel funktionen tekniker, modell utvärdering för utveckling och prestanda. I princip samlar denna playbook affärs- och analytiska riktlinjer som behövs för en lyckad utveckling och distribution av förutsägande Underhåll lösningar. Dessa riktlinjer förbereds för att skapa en första lösning med hjälp av Cortana Intelligence Suite och specifikt Azure Machine Learning som utgångspunkt i sina långsiktig strategi för förutsägande Underhåll målgruppen. Dokumentation om Cortana Intelligence Suite och Azure Machine Learning finns i [Cortana Analytics](http://www.microsoft.com/server-cloud/cortana-analytics-suite/overview.aspx) och [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) sidor.

> [!TIP]
> En teknisk guide att implementera den här lösningen-mallen finns [teknisk guide till Cortana Intelligence-Lösningsmall för förebyggande underhåll](cortana-analytics-technical-guide-predictive-maintenance.md).
> Du kan hämta ett diagram som ger en översikt över arkitekturen för den här mallen finns [arkitekturen i Cortana Intelligence-Lösningsmall för förebyggande underhåll](cortana-analytics-architecture-predictive-maintenance.md).
> 
> 

## <a name="playbook-overview-and-target-audience"></a>Målgruppen för Playbook översikt och mål
Den här playbook ordnas dra både teknisk och icke-teknisk målgrupp med olika bakgrund och intressen i förutsägande Underhåll utrymme. Playbook täcker båda högnivåaspekterna av de olika typerna av förutsägande Underhåll lösningar och information om hur du implementerar dem. Innehållet är balanserad för att tillgodose både till den målgrupp som bara är intresserad av att förstå området lösning och vilken typ av program som de som du vill implementera lösningarna och därför är intresserad av teknisk information.

Merparten av innehållet i den här playbook ta inte tidigare datavetenskap vetskap eller kunskaper. Men kräver vissa delar av playbook något förtrogenhet med vetenskap begrepp för att kunna följa implementeringsdetaljer. Inledande nivå datavetenskap kunskaper krävs för att utnyttja alla fördelar material i dessa avsnitt.

Den första delen av playbook omfattar en introduktion till förutsägande Underhåll program så för att kvalificera förutsägande Underhåll lösning, en samling av vanliga användningsfall med information om problem i verksamheten data runt dessa användningsfall och företagets fördelar med att implementera lösningarna förutsägande underhåll. Dessa avsnitt behöver inte ha någon teknisk kunskap i domänen förutsägelseanalys.

I den andra hälften av playbook upp vi typerna av tekniker för förutsägelsemodellering för förutsägande Underhåll program och hur du implementerar dessa modeller med exemplen från användningsområden som beskrivs i den första delen av playbook. Detta illustreras med att gå igenom stegen för dataförbearbetning av som data etiketter och funktionen teknik, modell markeringen, utbildning/testning och prestanda utvärdering bästa praxis. Dessa avsnitt är lämpliga för avancerade användare.

## <a name="predictive-maintenance-in-iot"></a>Förutsägande Underhåll i IoT
Effekten av utrustning oplanerade driftstopp kan vara mycket destruktiva för företag. Det är viktigt att behålla fältet utrustning som körs för att maximera användningen och prestanda och minimerar kostsamma felaktig driftstopp. Väntar på att det inte gick att inträffa är helt enkelt inte prisvärt i dagens företag operations scen. För att fortsätta vara konkurrenskraftig företag leta efter nya sätt att maximera tillgången prestanda genom att göra användning av data som samlas in från olika kanaler. Ett viktigt sätt att analysera sådan information är att använda förutsägande analytiska metoder som använder historiska mönster för att förutsäga framtida resultat. En av de mest populära för dessa lösningar kallas förutsägande underhåll som vanligtvis kan vara definierat som men inte begränsat till förutsäga risken för fel på en resurs i den nära framtiden så att resurserna som kan övervakas för att proaktivt identifiera fel och vidta åtgärder innan fel uppstår. Dessa lösningar upptäcka fel mönster för att fastställa tillgångar som är störst risk för fel. Den här tidig identifiering av problem kan distribuera begränsad Underhåll resurser på ett mer kostnadseffektivt sätt och förbättra kvaliteten och ange kedjan processer.

Med ökningen av Sakernas Internet (IoT)-program, förutsägande Underhåll har få ökande kontrolleras i branschen datainsamlingen och bearbetning av tekniker har mognadslagrats tillräckligt för att generera, överföra, lagra och analysera alla typer av data i batchar eller i realtid. Dessa tekniker aktivera enkel utveckling och distribution av lösningar för slutpunkt till slutpunkt med avancerade Analyslösningar med förutsägande Underhåll lösningar tillhandahåller tvekan största fördelen.

Affärsproblem förutsägande Underhåll domän mellan hög operativa risker på grund av oväntade fel och begränsad insyn i den grundläggande orsaken till problem i komplexa företagsmiljöer. Flesta av dessa problem kan kategoriseras sjunker under följande frågor för företag:

* Vad är sannolikheten att utrustning misslyckas i den nära framtiden?
* Vad är utrustningen återstående livslängd?
* Vad är orsaker till fel och vilka underhållsåtgärder ska utföras för att åtgärda dessa problem?

Företag kan använda förutsägande Underhåll för att besvara följande frågor:

* Minska operativa risker och ökar avkastningen på tillgångar genom att upptäcka fel innan de inträffade
* Minska onödiga tidsbaserade underhållsåtgärder och kontrollera kostnaden för underhåll
* Förbättra övergripande bild av varumärke, eliminera felaktiga reklam och resulterande förlorade försäljning från kunden partikelhållfasthet.
* Lägre kostnader för inventering genom att minska inventering nivåer genom att förutsäga beställningspunkten
* Identifiera mönster som är ansluten till olika underhållsproblem med

Förutsägande Underhåll lösningar kan ge företag med nyckeltal, till exempel resultat för hälsotillstånd att övervaka realtid tillgångens skick, en uppskattning av återstående livslängd av tillgångar, rekommendation för förebyggande underhållsaktiviteter och Beräknat datum för ersättning av delar.

## <a name="qualification-criteria-for-predictive-maintenance"></a>Kriterier för förebyggande underhåll
Det är viktigt att betona att inte alla användningsfall eller affärsproblem effektivt kan lösas genom förutsägande underhåll. Viktigt kriterier inkluderar om problemet är förutsägbar karaktär som finns en tydlig sökväg för åtgärden för att förhindra att fel när de identifieras i förväg och viktigast av allt data med tillfredsställande kvalitet för att stödja användningsfallet är tillgänglig. Här kan fokuserar vi på kraven för att skapa en lyckad förutsägande Underhåll lösning.

När du skapar förutsägelsemodeller använder vi historiska data för att träna modellen som kan identifiera dolda mönster och ytterligare identifiera dessa mönster i framtida data. Dessa modeller tränas med exempel som beskrivs av sina funktioner och mål för förutsägelse. Den tränade modellen förväntas göra förutsägelser på måldatorn genom att bara titta på funktioner i nya exemplen. Det är viktigt att modellen avbilda relationen mellan funktioner och mål för förutsägelse. För att träna en effektiv maskininlärning modellen måste träningsdata som innehåller funktioner som faktiskt har förutsägande ström till målet för förutsägelse vilket innebär att data ska vara relevanta för förutsägelse målet kan förvänta sig korrekta förutsägelser.

Om målet är att förutsäga fel i train hjul, innehåller av att träningsinformationen exempelvis hjul-relaterade funktioner (t.ex. telemetri som avspeglar hälsostatus hjul, hittills utfört, car belastning, etc.). Men om målet är att förutsäga train motorn fel, behöver vi förmodligen en ny uppsättning utbildningsdata som har motorn-relaterade funktioner. Innan du skapar förutsägelsemodeller planerar vi för verksamheten expert att förstå kravet relevans och ange den kunskap som behövs för att välja relevanta delmängder av data för analys.

Det finns tre grundläggande datakällor vi letar efter när kvalificerande affärsproblem vara lämpliga för en lösning med förutsägande Underhåll:

1. Fel historik: I förutsägande Underhåll program felhändelser normalt sällsynta. När du skapar förutsägelsemodeller som förutsäga fel måste algoritmen Läs mönstret normal drift samt fel mönstret genom utbildning-processen. Det är därför viktigt att utbildning data innehåller tillräckligt många exemplen i båda kategorier för att lära dig dessa två olika mönster. Därför kräver att data har tillräckligt många misslyckade händelser. Felhändelser finns i Underhåll poster och delar ersättning historik eller avvikelser i utbildning data kan också användas som kodfel som identifieras av domän-experter.
2. Underhåll och reparera historik: En grundläggande datakälla för förutsägande Underhåll lösningar är detaljerad Underhåll historiken för den tillgång som innehåller information om komponenter som ersätts, förebyggande underhåll aktiverar utförs, etc. Det är mycket viktigt att samla in dessa händelser som dessa påverkar försämring mönster och frånvaro av den här informationen orsakar missvisande resultat.
3. Datorn villkor: För att förutsäga hur många dagar (timmar, miles, transaktioner osv.) en dator varar innan det misslyckas, förutsätter vi att datorns hälsostatus försämras med tiden under dess drift. Därför kan räknar vi med data som ska innehålla tid olika funktioner som samlar in det här mönstret ålder och eventuella avvikelser som leder till försämras. Telemetridata från olika sensorer representerar ett bra exempel i IoT-appar. För att förutsäga om en dator ska misslyckas under en tidsperiod, ska helst data avbilda förnedrande trend under denna tidsperiod före händelsen verkligt fel.

Dessutom kan kräver vi att data som är direkt relaterade till driftsförhållanden tillgångens mål för förutsägelse. Beslutet mål baserat på tillgänglighet av både företag behov och data. Tar train hjul fel förutsägelser som exempel kan vi förutsäga ”om ratten ska ha ett fel” eller ”om hela tåget kommer har ett fel”. Den första som riktar sig till en mer specifik komponent, medan den andra som riktar sig mot fel på tåget. Den andra är en mer allmän fråga som kräver mycket mer spridda dataelement än den första, vilket gör det svårare att skapa en modell. Omvänt kan kanske försöka förutsäga hjul fel genom att titta på hög nivå train villkorsdata inte möjligt eftersom den inte innehåller information på komponentnivå. I allmänhet är det mer sensible att förutsäga specifika felhändelser än mer allmän som.

En vanlig fråga som vanligtvis tillfrågas om fel historikdata är ”hur många misslyckade händelser krävs för att träna en modell och hur många betraktas som” det finns tillräckligt med ”? Det finns inga Rensa svar på frågan som många förutsägelseanalyser, är det vanligtvis kvaliteten på de data som avgör vad som är acceptabel. Om datamängden inte innehåller funktioner som är relevanta för felförutsägelse sedan kanske även om det finns många felhändelser bygger en bra modell inte möjligt. Men är tumregeln att de mer av felhändelser bättre modellen och en grov uppskattning av hur många fel exempel krävs är en mycket kontext och data beroende mått. Det här problemet beskrivs i avsnittet för att hantera imbalanced datauppsättningar där vi föreslår metoder för att hantera problemet med inte har tillräckligt med fel.

## <a name="sample-use-cases"></a>Exempel användningsområden
Det här avsnittet fokuserar på en samling med förutsägande Underhåll användningsområden från flera branscher som Aerospace, verktyg och transport. Varje avsnitt går till de användningsområden som samlas in från dessa områden och beskriver affärsproblem data runt problem i verksamheten och fördelarna med en lösning för förutsägande underhåll.

### <a name="aerospace"></a>Aerospace
#### <a name="use-case-1-flight-delay-and-cancellations"></a>Användningsfall 1: Svarta fördröjning och avbryta
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
En av de största företag problem som flygbolagen yta är betydande kostnader som är associerade med flygplan är försenad på grund av problem med mekaniskt. Om mekaniskt fel inte kan repareras avbrytas även flygplan. Detta är mycket kostsam fördröjningar skapa problem i schemaläggning och åtgärder, orsaker felaktiga rykte och kunden klagomål tillsammans med andra problem. Flygbolag är särskilt intresserade förutsäga sådana mekaniskt fel i förväg så att de kan minska svarta fördröjningar eller avbryta. Målet med förutsägande Underhåll lösning för dessa fall är att förutsäga sannolikheten för ett flygplan som fördröjd eller avbrutits, baserat på relevanta datakällor, till exempel information om Underhåll historik och svarta flöde. Två viktiga datakällor för den här användningsfall är svarta ben och sidan loggar. Svarta ben data innehåller information om flödet flyginformation, till exempel datum och tid för utgångspunkten och ankomst, utgångspunkten och ankomst flygplatser och så vidare. Sidan loggdata innehåller ett antal fel och underhåll koder som registreras av underhållspersonal.

##### <a name="business-value-of-the-predictive-model"></a>*Värdet av förutsägelsemodellen*
Med hjälp av tillgängliga historiska data, har en förutsägelsemodell skapats med en algoritm för flera klassificering för att förutsäga typ av mekaniskt problem, vilket resulterar i en fördröjning eller annullering av en rör sig inom det närmaste dygnet. Genom att göra den här förutsägelse vidtas nödvändiga underhållsåtgärder för att minska risken medan ett flygplan genomgår underhåll och därmed förhindra eventuella fördröjningar eller avbryta. Med hjälp av Azure Machine Learning-webbtjänsten, kan förutsägelsemodeller sömlöst och enkelt integreras i flygbolagen befintliga operativsystem. 

#### <a name="use-case-2-aircraft-component-failure"></a>Användningsfall 2: Flygplan komponentfel
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
Flygplan motorer är mycket känslig och dyra delar av utrustning och motorn del ersättningar är bland de mest vanliga underhållsuppgifter i branschen flygbolag. Underhåll lösningar för flygbolag kräver noggrann hantering av lager komponenttillgänglighet, leverans och planera. Att kunna samla in underrättelser om komponenten tillförlitlighet leder till betydande minskning på investeringar. Större datakällan för den här användningsfall är telemetridata som samlas in från ett antal sensorer i flygplan med information om flygplanet. Underhåll poster som också användes för att identifiera när fel uppstod och ersättningar har gjorts.

##### <a name="business-value-of-the-predictive-model"></a>Värdet av förutsägelsemodellen
En modell för flera klassen klassificering byggdes som beräknar sannolikheten för en misslyckades på grund av en viss komponent under nästa månad. Genom att använda dessa lösningar kan flygbolagen minska kostnaderna för reparation av komponenten, förbättra lager komponenttillgänglighet, sänka inventering av relaterade resurser och förbättra Underhåll planering.

### <a name="utilities"></a>Samhällsservice
#### <a name="use-case-1-atm-cash-dispense-failure"></a>Användningsfall 1: Avstå ATM kontanter fel
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
Chefer i tillgångsinformation beräkningsintensiva branscher ofta tillstånd att primära operativa risk för sina företag är oväntade fel av deras tillgångar. Exempelvis är fel på maskiner som bl.a i bankwebbplatser branschen ett mycket vanligt problem som uppstår ofta. Sådana problem Se förutsägande Underhåll lösningar användbar för operatörer av maskinerna. I den här användningsfall är förutsägelse problemet att beräkna sannolikheten att en ATM kontanter återkallelse transaktion hämtar avbryts på grund av ett fel i kontanter utgivaren, till exempel pappersstopp eller en del. Viktiga datakällor för det här fallet är sensoravläsningar som samlar in mätningar medan anteckningar pengar fördelas och underhåll poster insamlade över tid. Sensordata med sensoravläsningar per varje transaktionen har slutförts och sensoravläsningar per varje anteckning vara. Temperatursensor avläsningar tillhandahålls mätningar som glapp mellan anteckningar, tjocklek Observera ankomst avståndet osv. Underhåll data med felkoder och reparationsinformation. Dessa som användes för att identifiera fel fall.

##### <a name="business-value-of-the-predictive-model"></a>*Värdet av förutsägelsemodellen*
Två förutsägelsemodeller har skapats för att förutsäga fel i kontanter återkallelse transaktioner och fel i enskilda anteckningar vara under en transaktion. Genom att förutsäga transaktion fel i förväg, betjänas bl.a proaktivt för att förhindra fel. Dessutom med Obs fel förutsägelser om en transaktion är antagligen misslyckas innan den är klar på grund av en anteckning överflödiga misslyckas, kan det vara bäst att stoppa processen och varna kunden för ofullständiga transaktion i stället för väntan på Underhåll tjänsten till När felet uppstår som kan leda till större kunden klagomål.

#### <a name="use-case-2-wind-turbine-failures"></a>Användningsfall 2: Vind turbinen fel
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
Med rera av medvetenheten vind turbiner har blivit en av de huvudsakliga källorna av energi och de kostnad vanligtvis miljontals kronor. En av huvudkomponenterna i vind turbiner är generator-motor som är försedd med många sensorer som hjälper dig för att övervaka turbinen villkor och status. Sensoravläsningar innehåller värdefull information som kan användas för att skapa en förutsägelsemodell för att förutsäga kritiska nyckel (nyckeltal), till exempel tiden fel för komponenter av vind turbinen. Data för den här användningsfall kommer från flera vind turbiner som finns i tre olika platser. Mått från nära hundra sensorer från varje turbinen registrerades var 10: e sekund för ett år. Dessa värden inkluderar mått som temperatur, generator hastighet, turbinen power och generator upplösning.

##### <a name="business-value-of-the-predictive-model"></a>*Värdet av förutsägelsemodellen*
Förutsägelsemodeller har skapats för att beräkna återstående livslängd för generatorer och temperatursensorer. Underhåll tekniker kan fokusera på misstänkt turbiner som sannolikt kommer att misslyckas snart att komplettera tidsbaserade Underhåll systemen genom att förutsäga sannolikheten för fel.
Förutsägelsemodeller får dessutom insight andelen bidrag för olika faktorer som sannolikheten för fel som hjälper företag att ha en bättre förståelse av orsaken till problemen.

#### <a name="use-case-3-circuit-breaker-failures"></a>Användningsfall 3: strömbrytare fel
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
El- och åtgärder som innefattar produktion, distribution eller försäljning av el kräver mycket underhåll för att säkerställa power rader fungerar vid alla tidpunkter för att garantera leverans av energi till hushåll. Fel i dessa åtgärder är viktig när nästan alla enheter sker med power problem i områden som de sker. Utlösts är kritiska för dessa åtgärder som de är en typ av utrustning som Klipp ut elektrisk ström vid problem och kort kretsar för att förhindra skador till power rader. Problem i verksamheten för den här användningsfall är att förutsäga strömbrytare fel Underhåll loggar, kommandohistoriken och tekniska specifikationer.

Tre viktiga datakällor för det här fallet är underhåll av loggar med korrigerande, förebyggande och systematiskt åtgärder, användningsdata med automatisk och manuell kommandon som skickas till utlösts som öppna och Stäng åtgärder och technical specifikationen data om egenskaperna för varje strömbrytare, till exempel år som gjorts, plats, modell, osv.

##### <a name="business-value-of-the-predictive-model"></a>*Värdet av förutsägelsemodellen*
Förutsägande Underhåll lösningar att minska kostnaderna för reparation och öka livscykeln för utrustning som utlösts. Dessa modeller också förbättra kvaliteten på nätverket power eftersom modeller ger varningar i förväg som minskar oväntade fel som leder till färre avbrott i tjänsten.

#### <a name="use-case-4-elevator-door-failures"></a>Användningsfall 4: Snabba dörren fel
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
De flesta stora snabba företag har vanligtvis miljontals hissar kör runtom i världen. För att få en konkurrensfördel, fokusera de på tillförlitlighet som är det viktigaste till sina kunder. Ritning på potentiella Internet saker, genom att ansluta sina hissar till molnet och samla in data från snabba sensorer och system, kan de Transformera data till värdefull affärsinformation vilket frågeprestanda avsevärt förbättrar åtgärder genom att erbjuda förutsägbar och föregripande underhåll som inte är något som är tillgängliga för konkurrenterna ännu. Affärsbehoven för det här fallet är att tillhandahålla ett kunskapsbas förutsägande program som beräknar risken orsakar dörren fel. Data som krävs för den här implementeringen består av tre delar som är snabba statiska funktioner (t.ex. identifierare, minimera frekvens för underhåll, skapande av typen, etc.), information om användning (t.ex. antalet dörren cykler, genomsnittlig dörren Stäng tid, etc.) och fel historik (d.v.s. historiska fel poster och deras orsaker).

En multiklass-baserad logistic regressionsmodell har skapats med Azure Machine Learning till problemet förutsägelse, med inbyggda funktioner för statiska och användningsdata som funktioner och orsakerna till historiska fel poster som klass etiketter. Den här förutsägelsemodell används av en app på en mobil enhet som används av fältet tekniker för att förbättra effektiviteten fungerar. När en tekniker på plats för att reparera en snabba, kan han eller hon kan referera till den här appen för rekommenderade orsaker och bästa kurser underhållsåtgärder att åtgärda de snabba dörrarna så snabbt som möjligt.

### <a name="transportation-and-logistics"></a>Transport och logistik
#### <a name="use-case-1-brake-disc-failures"></a>Användningsfall 1: Bromsar skiva fel
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
Vanliga underhållsprinciper för fordon omfattar korrigerande och förebyggande underhåll. Åtgärda Underhåll innebär att vehicle har reparerats när ett fel har uppstått som kan orsaka ett allvarligt problem som uppstår till drivrutinen på grund av ett oväntat fel och tid som gått förlorat på ett besök dvs. De flesta fordon regleras också av en princip för förebyggande underhållsåtgärder som kräver att utföra vissa kontroller på ett schema som inte tar hänsyn till det faktiska villkoret bil delsystem. Ingen av dessa metoder är att helt eliminera problem. Det specifika användningsfallet här är bromsar skiva felförutsägelse baserat på data som samlas in via sensorer som är installerade i systemet däck på en bil som håller reda på historiska intresseväckande mönster och andra villkor som exponeras för bilen. De viktigaste datakällan för det här fallet är sensordata som till exempel mäter acceleration, bromsar mönster, körning avstånd, hastighet, osv. Den här informationen, tillsammans med andra statisk information, till exempel bil funktioner hjälp bygga en bra uppsättning predictors som kan användas i en förutsägelsemodell. En annan är viktig information fel data som härledas från en del ordning databasen (används för att hålla extra ordning datumen för och antalet som bilar behandlas i de ensamåterförsäljare förekommer).

##### <a name="business-value-of-the-predictive-model"></a>*Värdet av förutsägelsemodellen*
Värdet för en förutsägbar tillvägagångssättet är stor. Ett system med förutsägande Underhåll kan schemalägga ett besök på din återförsäljare baserat på en förutsägelsemodell. Modellen kan baseras på sensoriska information som som representerar bilen och intresseväckande tidigare aktuella tillstånd. Den här metoden kan du minimera risken för oväntade fel som kan även uppstå innan nästa regelbundet underhåll.
Det kan också minska onödiga förebyggande underhåll. Drivrutinen kan proaktivt informeras om att en ändring av delar kan vara nödvändigt i några veckor och ange din återförsäljare med denna information. Din återförsäljare kunde sedan förbereda ett paket för enskilda Underhåll för drivrutinen i förväg.

#### <a name="use-case-2-subway-train-door-failures"></a>Användningsfall 2: Språng train dörren fel
##### <a name="business-problem-and-data-sources"></a>*Företag problem och datakällor*
En av huvudsakliga skälen förseningar och problem på språng operations är dörren fel train bilar. Förutsäga om en bil tåget kan ha ett dörren fel eller att förutsäga antalet dagar till nästa door felet är mycket viktigt förutseende. Det ger möjlighet att optimera train dörren underhåll och minska tåget nertid.

#### <a name="data-sources"></a>Datakällor
Tre datakällorna i den här användningsfall är 

* **Träna händelsedata**, som är tidigare poster train-händelser 
* **Underhåll data** , till exempel underhållstyper, arbete ordning typer och prioritetskoder  
* **registrerar fel**.

##### <a name="business-value-of-the-predictive-model"></a>*Värdet av förutsägelsemodellen*
Två modeller har byggts för att förutsäga nästa dag fel sannolikhet med hjälp av binär klassificering och dagar till felet med hjälp av regression. Precis som tidigare fall, modeller skapa fantastiska möjligheter att förbättra kvaliteten på tjänsten och öka nöjda komplement till de regelbundna Underhåll systemen.

## <a name="data-preparation"></a>Förberedelse av data
### <a name="data-sources"></a>Datakällor
Gemensamma uppgifter för problem med förutsägande Underhåll kan sammanfattas på följande sätt:

* Fel historik: fel historiken för en dator eller en komponent i datorn.
* Underhållshistorik: reparera historiken för en dator, t.ex. felkoder, tidigare underhållsaktiviteter eller komponenten ersättningar.
* Villkor och användning: driftsförhållanden för en dator som t.ex. data som samlas in från sensorer.
* Datorn funktioner: funktioner för en dator, t.ex. motorn storlek, märke och modell, plats.
* Operatorn funktioner: funktioner för operatorn, t.ex. för kön, tidigare erfarenheter.

Det är möjligt och vanligtvis fallet att fel Historik finns i Underhållshistorik som i form av särskilda felkoder eller datum för ledig delar. I dessa fall kan fel extraheras från Underhåll data. Olika domäner kan dessutom ha en mängd andra datakällor som påverkar fel mönster som inte listas här omfattande. Dessa bör identifieras med hjälp av domän-experter när du skapar förutsägelsemodeller.

Det är några exempel på ovan dataelement från användningsområden:

Fel historik: Flight fördröjning datum, flygplan komponenten fel datum och typer, ATM kontanter återkallelse transaktion fel, train/snabba dörren fel, bromsar disk ersättning datum, vind turbinen fel datum och strömbrytare kommandot fel.

Underhållshistorik: svarta felloggar, ATM för fel transaktionsloggar träna Underhåll poster, inklusive underhållstyp, kort beskrivning etc. och strömbrytare Underhåll poster.

Villkor och användning: svarta dirigerar och tider, sensordata som samlas in från flygplan motorer, sensoravläsningar från ATM-transaktioner, träna händelser data, sensoravläsningar från vind turbiner, hissar och anslutna bilar.

Datorn funktioner: strömbrytare tekniska specifikationer som spänning nivåer, geolokalisering eller bil funktioner, till exempel märke, modell, motorn storleken däck typer produktion och osv.

Två huvudsakliga datatyperna vi observerar i förutsägande underhållsdomän är anges ovan datakällorna temporal data och statiska data.
Fel-historik, datorn villkor reparera historik användningshistorik nästan alltid komma med tidsstämplar som anger när mängden för varje typ av data. Funktioner på datorer och operatorn funktioner är i allmänhet statiska eftersom de vanligtvis beskriver de tekniska specifikationerna av datorer eller aktörens egenskaper. Det är möjligt att dessa funktioner ska ändras med tiden och om så är de ska behandlas som datakällor för tidsstämpel.

### <a name="merging-data-sources"></a>Sammanslagning av datakällor
Vi måste först förbereda våra data i formuläret som krävs för att skapa funktioner från innan du hämtar till en typ av funktionen tekniker eller märkning processen. Målet är att generera en post för varje gång för varje tillgång till dess funktioner och etiketter skall matas in i maskininlärningsalgoritmen. För att förbereda för att rensa slutliga datauppsättning, ska vissa före bearbetning vidta åtgärder. Första steget är att dela varaktigheten för datainsamlingen i tidsenheter där varje post tillhör en tidsenhet för en tillgång. Insamling av data kan också delas in i andra enheter, till exempel åtgärder, men för enkelhetens skull använder vi tidsenheter för resten av förklaringarna.

Måttenheten för gången kan vara i sekunder, minuter, timmar, dagar, månader, cykler, miles eller transaktioner beroende på effektiviteten förberedelse av data och ändringarna som observerats under villkoren i tillgångsinformation från en enhet eller andra faktorer specifika till domänen. Med andra ord behöver tidsenheten inte vara samma som frekvensen för insamling av data i många fall data inte kanske visar skillnader från en enhet till en annan. Till exempel om temperatur värden som samlades var 10: e sekund, välja en tidsenhet 10 sekunder för hela analys blåses upp antal exempel utan att ange ytterligare information. Bättre strategi är att använda medelvärde över en timme som exempel.

Exempel allmänna data scheman för möjliga datakällorna som beskrivs ovan är:

Underhåll poster: dessa är poster med underhållsåtgärder som utförs. Rådata Underhåll data kommer vanligtvis med ett Tillgångsnummer och tidsstämpel med information om vilka underhållsaktiviteter har utförts vid den tiden. Vid sådan rådata behöver underhållsaktiviteter översättas till kategoriska kolumner med varje kategori som motsvarar en åtgärdstyp för underhåll. Grundläggande dataschemat för Underhåll poster skulle innehåller kolumner för tillgångsinformation ID, tid och underhåll åtgärd.

Fel poster: dessa är de poster som hör till målet för förutsägelse som vi kallar fel eller orsaken till felet. Dessa kan vara specifika felkoder eller händelser för fel som definieras av specifika villkor. I vissa fall kan innehåller data flera felkoder vilket motsvarar fel av intresse. Alla fel är målet för förutsägelse så att andra fel används vanligtvis för att skapa funktioner som kan ha att göra med fel. Grundläggande dataschemat för fel poster omfattar Tillgångsnummer, tid och problem eller fel orsak kolumner om orsak finns.

Datorn villkor: dessa är helst realtidsövervakning data om driftsförhållanden av data. Till exempel för dörren fel dörren öppna och stänga tider är bra indikatorer om dörrar aktuella tillstånd. Grundläggande dataschemat för datorn villkor skulle innehåller kolumner för tillgångsinformation ID, tid och villkoret värde.

Dator-och operator: datorn och operatorn data kan kopplas till ett schema för att identifiera vilka tillgången har drivs av vilken operator tillsammans med egenskaperna tillgången och operatorn. Till exempel ägs en bil vanligtvis av en drivrutin med attribut, till exempel ålder, körning upplevelse osv. Om dessa data ändras med tiden kan dessa data bör också innehålla en time-kolumn och ska behandlas som tid olika data för generering av funktionen. Grundläggande dataschemat för datorn villkor omfattar Tillgångsnummer, tillgångsinformation funktioner, operator-ID och operatorn funktioner.

Den slutliga tabellen innan etiketter och funktionen generation kan genereras av vänster koppla datorn villkor tabell med fel poster för tillgångsinformation ID och tidsfälten. Den här tabellen kan sedan kopplas till Underhåll poster på Tillgångsnummer och tid fält och slutligen till datorn och operatorn funktioner på tillgången-ID. Den första vänstra kopplingen lämnar null-värden för fel när datorn är normal drift, dessa imputeras genom en indikatorvärdet för normal drift. Den här kolumnen fel används för att skapa etiketter för förutsägelsemodellen.

### <a name="feature-engineering"></a>Funktionstekniker
Det första steget i modellering är funktionen tekniker. Uppfattning om funktionen generation är begreppsmässigt beskriva och abstrakt hälsostatus för en dator vid en given tidpunkt med hjälp av historiska data som samlats in upp till som tidpunkt. I nästa avsnitt ger vi en översikt över typ av tekniker som kan användas för förutsägande underhåll och hur de etiketter är klar för varje teknik. Den exakta teknik som ska användas beror på problemet och affärsprogram. Funktionen engineering metoderna som beskrivs nedan kan dock användas som baslinjen för att skapa funktioner. Nedan diskuterar vi fördröjning funktioner som ska konstrueras från datakällor som medföljer tidsstämplar och statiska funktioner som har skapats från statisk datakällor och ge exempel från användas.

#### <a name="lag-features"></a>Lag funktioner
Som nämnts tidigare i förutsägande Underhåll finns vanligtvis historiska data med tidsstämplar som anger när mängden för varje typ av data. Det finns många sätt för att skapa funktioner från data som medföljer tidsstämplad data. I det här avsnittet diskuterar vi några av dessa metoder som används för förebyggande underhåll. Vi är inte begränsade av dessa metoder som enbart. Eftersom funktionen tekniker anses vara en av de mest kreativa områdena för förutsägelsemodellering, kan det finnas många andra sätt att skapa funktioner. Här kan ge vi några allmänna metoder.

##### <a name="rolling-aggregates"></a>*Rullande mängder*
För varje post för en tillgång Välj vi ett rullande fönster storlek ”W” vilket är det antal enheter som vi vill att beräkna historiska mängder för. Vi för att beräkna rullande sammanställd funktioner med W punkter före datumet för den posten. Vissa exempel rullande mängder kan löpande antal, medel, standardavvikelser, baserat på standardavvikelser avvikare, CUSUMS åtgärder, lägsta och högsta värden för fönster. En annan intressant metod är att samla in trend ändringar, toppar och ändras med hjälp av algoritmer som identifierar avvikelser i data med hjälp av algoritmer för avvikelseidentifiering identifiering.

Exempel, finns i bild 1 där vi representerar sensorn värden för varje tidsenhet med blå rader som registrerats för en tillgång och markera rullande genomsnittlig funktionen-beräkningen för W = 3 för poster vid t<sub>1</sub> och t<sub>2</sub> som anges med orange och grön grupperingar respektive.

![Bild 1. Rullande sammanställd funktioner](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

Bild 1. Rullande sammanställd funktioner

Som exempel, som för flygplan komponentfel sensor värden från föregående vecka, senaste tre dagarna och sista dag användes för att skapa rullande innebär, standardavvikelse och summan funktioner. För ATM-fel, både rådata sensor värden och återställa innebär median, intervall, standardavvikelser användes på samma sätt kan antalet avvikare utöver tre standardavvikelser övre och nedre CUMSUM funktioner.

Räknar av fel koder från föregående vecka som användes för att skapa funktioner för svarta fördröjning förutsägelse. För tåget dörren fel användes antal händelser för den sista dagen, antalet händelser under de föregående två veckorna och variansen för antal händelser för de senaste 15 dagarna fördröjning funktioner. Samma räknat användes för underhåll-relaterade händelser.

Dessutom är genom att välja ett W som mycket stora (t.ex. år), är det möjligt att visa hela historiken för en tillgång till exempel räknar alla Underhåll poster fel osv. fram till tidpunkten för posten. Den här metoden används för att kunna räkna strömbrytare fel under de senaste tre år. Alla underhållshändelser har också räknas för att skapa en funktion för att avbilda långsiktiga Underhåll effekter för train-fel.

##### <a name="tumbling-aggregates"></a>*Rullande mängder*
För varje etikett post för en tillgång, vi väljer ett fönster med storleken ”W -<sub>k</sub>” där n är det antal eller windows storlek ”W” som vi vill skapa fördröjning funktioner för. ”k” kan registreras som ett stort antal att avbilda långsiktiga försämring mönster eller ett litet antal att avbilda kortsiktig effekter. Vi använder sedan k rullande windows W -<sub>k</sub> , W -<sub>(k-1)</sub>,..., W -<sub>2</sub> , W -<sub>1</sub> skapa sammanställd funktioner för punkter före post och tid (se bild 2). Dessa också rullande windows på nivån post för en enhet som inte har avbildats på bild 2 men tanken är samma som i bild 1 om t<sub>2</sub> används också för att demonstrera rullande effekten.

![Figur 2. Rullande sammanställd funktioner](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

Figur 2. Rullande sammanställd funktioner

Exempel för vind turbiner, B = 1 och k = 3 månader som användes för att skapa fördröjning funktioner för var och en av de senaste 3 månaderna med övre och nedre avvikare.

#### <a name="static-features"></a>Statiska funktioner
Dessa är tekniska specifikationer av utrustning tillverkningsdatum, modellnummer, plats, t.ex. Fördröjning funktioner är främst numeriska karaktär, blir statiska funktioner vanligen kategoriska variabler i modeller. Som ett exempel strömbrytare egenskaper, till exempel spänning, aktuella och power specifikationer tillsammans med transformatorn typer användes strömkällor etc. För bromsar skiva fel hjul typ av däck sådana som om de är legering eller stål användes som några av de statiska funktionerna.

Andra viktiga steg som hanterar saknade värden och normalisering ska utföras under genereringen av funktionen. Det finns flera metoder för saknade värdet uppräkning och databasnormalisering som inte beskrivs här. Dock är det bra att prova olika metoder för att se om en prestandaökning förutsägelse är möjligt.

Tabellen slutliga funktioner när funktionen engineering stegen som beskrivs ovan bör likna följande exempel dataschemat när enheten är en dag:

| Tillgångs-ID | Tid | Funktionen kolumner | Etikett |
| --- | --- | --- | --- |
| 1 |Dag 1 | | |
| 1 |Dag 2 | | |
| ... |... | | |
| 2 |Dag 1 | | |
| 2 |Dag 2 | | |
| ... |... | | |

## <a name="modeling-techniques"></a>Tekniker för förutsägelsemodellering
Förutsägande Underhåll är en mycket omfattande domän ofta använda verksamhetsrelaterade frågor som kan vara närmat sig från många olika vinklar för förutsägelsemodellering perspektiv. I nästa avsnitt får huvudsakliga tekniker som används för att modellera olika frågor som kan lösas med förutsägande Underhåll lösningar. Även om det finns likheter, har varje modell egna sätt att konstruera etiketter som beskrivs i detalj. Du kan referera till mallen förutsägande underhåll som ingår i exempelexperiment i Azure Machine Learning som en tillhörande resurs. Länkar till online materialet för den här mallen finns i avsnittet resurser. Du kan se hur några av funktionen engineering metoder som beskrivs ovan och modellering-teknik som beskrivs i nästa avsnitt används för att förutsäga flygplan motorn fel med hjälp av Azure Machine Learning.

### <a name="binary-classification-for-predictive-maintenance"></a>Binär klassificering för förebyggande underhåll
Binär klassificering för förebyggande underhåll används för att förutsäga sannolikheten att utrustning misslyckas i en framtida tidsperiod. Hur lång tid som bestäms av och baserat på regler och data till hands. Några vanliga tidsperioder är minsta ledtid som krävs för att köpa extra delar ersätta sannolikt att skada komponenter eller tid som krävs för att distribuera Underhåll resurser för att utföra underhållsrutiner för att åtgärda problemet som troligen kommer att äga rum inom denna tidsperiod. Vi kallar framtida horizon perioden ”X”.

Vi behöver identifiera två typer av exemplen som vi kallar positiva och negativa för att kunna använda binär klassificering. Varje exempel är en post som tillhör en tidsenhet för en tillgång begreppsmässigt beskriva och abstrahera dess driftsförhållanden upp till att enheten via funktionen engineering med historiska och andra datakällor som beskrivs ovan. I samband med binär klassificering för förebyggande underhåll positivt typen anger fel (1-etiketten) och negativa typen anger normal drift (etikett = 0) där etiketter är av typen kategoriska. Målet är att hitta en modell som identifierar varje ny exempel som kan misslyckas eller fungera normalt inom kommande X tidsenheter.

#### <a name="label-construction"></a>Etikett konstruktion
För att skapa en förutsägelsemodell för att besvara frågan ”vad är sannolikheten att tillgången misslyckas under kommande X tidsenheter”?, etikettering görs genom att ta X poster innan felet inträffade för en tillgång och ge dem etiketter som ”väg till misslyckas” (etikett = 1) när etiketter alla  andra poster ”normal” (etikett = 0). I den här metoden är etiketter kategoriska variabler (se bild 3).

![Bild 3. Etiketter för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

Bild 3. Etiketter för binär klassificering

För svarta fördröjningar och avbryta ut X som en dag för att förutsäga fördröjningar i nästkommande 24 timmar. Alla flygplan inom 24 timmar innan fel har betecknats som 1s. För ATM kontanter överflödiga fel, två binär klassificering modeller är byggda att förutsäga fel sannolikheten för en transaktion i 10 minuter och att förutsäga sannolikheten för fel i nästa 100 anteckningar vara. Alla transaktioner som har inträffat under de senaste 10 minuterna till felet är märkta som 1 för den första modellen. Och alla anteckningar vara i de sista 100 anteckningarna för ett fel har betecknats som 1 för den andra modellen. För strömbrytare fel är aktiviteten att förutsäga sannolikheten för att kommandot nästa strömbrytare misslyckas som väljs fallet X ska vara ett framtida kommando. För tåget dörren fel har binär klassificering modellen skapats för att förutsäga fel inom de kommande sju dagarna. För vind turbinen fel valdes X som tre månader.

Vindkraft turbinen och träna dörren fall används också för regressionsanalys att förutsäga återstående livslängd som använder samma data men genom att använda en annan märkning strategi som beskrivs i nästa avsnitt.

### <a name="regression-for-predictive-maintenance"></a>Regression för förebyggande underhåll
Regression modeller i förutsägande Underhåll används för att beräkna återstående livslängd (RUL) för en tillgång som definieras som tidsperiod som tillgången fungerar innan nästa fel inträffar. Samma som binär klassificering, varje exempel är en post som tillhör en tidsenhet ”Y” för en tillgång. I samband med regression är dock målet att hitta en modell som beräknar återstående livslängd varje ny exempel som en kontinuerlig tal som är tidsperioden som återstår innan felet inträffade. Vi kallar denna tidsperiod vissa multipel av Y. Varje exempel har även en återstående livslängd som kan beräknas genom att mäta hur lång tid som återstår för det exemplet innan nästa fel.

#### <a name="label-construction"></a>Etikett konstruktion
Beroende på frågan ”vad är utrustningen återstående livslängd?
”, märker för regressionsmodellen kan konstrueras genom att använda varje post innan felet inträffade och ge dem etiketter genom att beräkna hur många enheter tid som återstår innan nästa fel. I den här metoden är etiketter kontinuerlig variabler (se figur 4).

![Bild 4. Etiketter för regression](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

Bild 4. Etiketter för regression

Skiljer sig binär klassificering för regression, tillgångar utan några fel i data kan inte användas för modellering som etiketter görs i förhållande till en felpunkt och beräkningen är inte möjligt utan att känna till hur länge tillgången fortfarande varit i livet innan ett fel uppstod. Det här problemet är bäst med ett annat statistiska teknik som kallas överlevnad analys.
Vi kommer inte att diskutera överlevnad analys i den här playbook på grund av eventuella problem som kan uppstå när du tillämpar tekniken på förutsägande Underhåll användningsområden som avser tiden varierande data med återkommande intervall.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Flera klassen klassificering för förebyggande underhåll
Flera klassen klassificering för förebyggande underhåll kan användas för att förutsäga två framtida resultat. Den första är att tilldela en tillgång till en av flera möjliga tidsperioder att ge ett tidsintervall fel för varje tillgång. Den andra är att identifiera risken för fel i en framtida period på grund av flera rotorsaker. Som gör det möjligt för underhållspersonal som utrustade med denna kunskap för att hantera problem i förväg. En annan metod för flera klassen modellering fokuserar på de mest sannolikt bestämma grundorsaken till ett givet ett fel. Detta gör att rekommendationer för de översta underhållsåtgärder som ska vidtas för att åtgärda ett fel.
Genom att låta en rankningslista över rot orsakar och tillhörande repareringsåtgärder  
tekniker kan vara mer effektivt att deras första reparationsåtgärder efter fel.

#### <a name="label-construction"></a>Etikett konstruktion
Beroende på två frågor som ”vad är sannolikheten att en tillgång misslyckas i nästa” aZ ”enheter tid där” a ”är antalet perioder” och ”vad är sannolikheten att tillgången misslyckas i nästa X tidsenheter på grund av problem” P<sub>jag</sub>” där ”i” är antalet möjliga underliggande orsaker är etiketter klar på följande sätt för dessa tekniker.

För den första frågan etiketter görs genom att använda aZ poster innan felet inträffade för en tillgång och ge dem etiketter med buckets tid (3Z, 2Z Z) som etiketter när etiketter alla poster som ”normala” (etikett = 0). I den här metoden är etikett kategoriska variabeln (se bild 5).

![Bild 5. Etiketter för multiklass-baserad klassificering för fel tid förutsägelse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Bild 5. Etiketter för multiklass-baserad klassificering för fel tid förutsägelse

För den andra frågan etiketter görs genom att ta X poster innan felet inträffade för en tillgång och etikettering dem som ”kommer att misslyckas på grund av problem P<sub>jag</sub>” (etikett = P<sub>jag</sub>) när etiketter alla poster som ”normala” (etikett = 0). I den här metoden är etiketter kategoriska variabler (se bild 6).

![Bild 6. Etiketter för multiklass-baserad klassificering för rot-orsaken förutsägelse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Bild 6. Etiketter för multiklass-baserad klassificering för rot-orsaken förutsägelse

Modellen tilldelar en sannolikheten för fel på grund av varje P<sub>jag</sub> samt sannolikheten för fel. Dessa sannolikhet kan beställas storlek så att förutsägelser av de problem som är mest sannolikt att i framtiden. Användningsfall för flygplan komponenten fel struktur som multiklass-baserad klassificeringsproblem. Detta gör att förutsägelser av troliga misslyckades på grund av två olika trycket ventilkomponenter inträffar under nästa månad.

För att rekommendera underhållsåtgärder efter fel, kräver etikettering inte en framtida horizon tas ut. Detta beror på att modellen inte är att förutsäga fel i framtiden, men det är bara att förutsäga den mest sannolika orsaken när felet redan har skett. Snabba dörren fel hör till det tredje fallet där målet är att förutsäga orsaken till felet anges historiska data om driftsförhållanden. Den här modellen används sedan för att förutsäga den mest sannolika rotorsaker när ett fel har uppstått. En viktig fördel med den här modellen är van vid tekniker att enkelt diagnostisera och lösa problem som annars skulle behöva års erfarenhet underlättar.

## <a name="training-validation-and-testing-methods-in-predictive-maintenance"></a>Utbildning, verifiering och tester metoder i förutsägande Underhåll
Vanliga träning och testning rutinunderhåll måste beaktas i förutsägande underhåll, liknande till andra Lösningsutrymme som innehåller tidsstämplad data generalisera tid olika aspekter att bättre på överblivna framtida data.

### <a name="cross-validation"></a>Mellan verifiering
Många maskininlärningsalgoritmer beror på ett antal justeringsmodeller som kan ändra modellen prestanda avsevärt. Optimala värdena för dessa justeringsmodeller beräknas inte automatiskt när träna modellen, men anges av data forskare. Det finns flera sätt att hitta bra värdena för justeringsmodeller. Det vanligaste är ”k vikning korsvalidering” som delar upp exemplen slumpmässigt i vikningar som ”k”. För varje uppsättning justeringsmodeller värden är Inlärningsalgoritmen kör k gånger. Exemplen i den aktuella vikningen används som en verifiering, resten av exemplen som används som en träningsmängden vid varje iteration. Algoritmen tåg över utbildning exempel och prestandamåtten beräknas över verifieringen exempel. I slutet av den här upprepningen för varje uppsättning hyperparameter värden vi beräkna medelvärdet av k prestanda mått och välj hyperparameter värden som har bästa genomsnittliga prestanda.

Som tidigare nämnts förutsägande Underhåll problem registreras data som en tidsserie händelser som kommer från flera datakällor. Dessa poster kan beställas enligt tiden för etikettering en post eller ett exempel. Därför om vi delar datauppsättningen slumpmässigt i utbildning och validering är vissa av utbildning exemplen senare än några exempel validering. Detta resulterar i att uppskatta framtida prestanda för hyperparameter värden baserat på data som anlänt innan modellen har tränats. Dessa beräkningar kanske alltför optimistisk, särskilt om tidsserier inte är stilla och ändra deras beteende över tid. Valda hyperparameter värden kan därför inte är optimalt.

Ett bättre sätt att hitta bra värdena för justeringsmodeller är att dela exemplen i utbildning och validering i tid beroende sätt, så att alla validering exemplen är senare än alla utbildning exempel. För varje uppsättning värden för justeringsmodeller vi träna algoritmen över träningsmängden, mäta modellens prestanda över samma verifiering och sedan välja hyperparameter värden som visar att uppnå bästa prestanda. När time series-data är inte stilla och utvecklas över tid, hyperparameter värdena som valts av tåget/validering dela leda till ett bättre framtida ”modellens prestanda än med värden som väljs slumpmässigt av korsvalidering.

Den slutliga modellen genereras av utbildning en inlärningsalgoritm över hela data med hjälp av bästa hyperparameter värdena som finns med hjälp av utbildning/validering delade eller korsvalidering.

### <a name="testing-for-model-performance"></a>Testa prestanda för modellen
När du har skapat en modell som vi behöver beräkna framtida prestanda på nya data. Den enklaste uppskattningen kan vara prestanda för modellen över utbildning data. Men denna uppskattning alltför optimistisk eftersom modellen är anpassad till de data som används för att beräkna prestanda. En bättre uppfattning kan vara prestanda för mått hyperparameter värden som beräknats över verifiering eller ett mått för genomsnittliga prestanda som beräknats från korsvalidering. Men dessa beräkningar är fortfarande alltför optimistisk av samma skäl som tidigare nämnts anger. Vi behöver mer realistisk metoder för att mäta prestanda i modellen.

Ett sätt är att dela data slumpmässigt i uppsättningar för träning, validering och test. Uppsättningar för träning och validering används för att markera värden för justeringsmodeller och träna modellen med dem. Prestanda för modellen mäts över test.

Ett annat sätt som är relevant för förutsägande Underhåll är att dela exemplen i utbildning, verifiering och testuppsättningar i tid beroende sätt, så att alla test exemplen är senare än alla utbildning och validering exempel. När delning, modell generation och prestanda måttenhetsvärde görs på samma sätt som beskrivs ovan.

När-tidsserier är stilla och enkelt att förutsäga generera båda metoderna liknande uppskattningar av framtida prestanda. Men när tidsserie ej stationära och/eller svårt att förutse, det andra sättet genererar mer realistisk uppskattningar av framtida prestanda än den första.

### <a name="time-dependent-split"></a>Tid beroende delning
Som bästa praxis, i det här avsnittet tar vi en närmare titt på hur du implementerar tid beroende dela. Vi beskriver en dubbelriktad delning tid beroende mellan utbildnings- och testuppsättningar men exakt samma logik tillämpas för tid beroende dela för uppsättningar för träning och validering.

Anta att vi har en dataström med tidsstämplad händelser, t.ex mått från olika sensorer. Funktioner i utbildning och testa exempel samt deras etiketter definieras över tidsramar som innehåller flera händelser.
Till exempel för binär klassificering, såsom beskrivs i avsnitten funktionen Engineering och modellering tekniker, funktioner skapas baserat på de tidigare händelserna och etiketter skapas baserat på framtida händelser inom ”X” tidsenheter i framtiden. Därför kommer märkning tidsramen för ett exempel senare sedan tidsramen för dess funktioner. Tid beroende delning väljer vi en punkt i tiden då vi träna en modell med ögonen öppna för justeringsmodeller med hjälp av historiska data upp till som pekar. För att förhindra läckor av framtida etiketter som ligger utanför utbildning avklippt i träningsdata vi väljer senaste tidsramen etikett utbildning exempel ska X enheter före slutdatumet utbildning. På bild 7 representerar varje fylld cirkel en rad i datauppsättningen slutliga funktioner som de funktioner och etiketter beräknas enligt den metod som beskrivs ovan. Bilden visar anges som de poster som ska gå till träning och testning anger när du implementerar tid beroende delning för X = 2 och W = 3:

![Bild 7. Tid beroende dela för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

Bild 7. Tid beroende dela för binär klassificering

Grön rutor representera poster som hör till enheterna som kan användas för utbildning. Enligt beskrivningen tidigare varje utbildning exempel i det slutliga funktionstabellpost genereras genom att titta på föregående 3 perioderna för funktionen generation och 2 framtida perioder för etikettering innan utbildning dag avklippt. Vi använda inte exempel i utbildning när någon del av de kommande 2 perioderna för det exemplet ligger utanför utbildning avklippt eftersom antar vi att vi inte har insyn utöver utbildning avklippt. På grund av denna begränsning representerar svart exemplen poster på den sista märkt datamängd som inte ska användas i datauppsättningen utbildning. Dessa poster används inte vid testning av data, antingen eftersom de är innan utbildning avklippt och deras märkning tidsramar beror delvis på utbildning tidsramen som inte ska vara fallet som vill vi helt separata etiketter tidsramar för träning och testning för att förhindra etikett information sprids.

Den här tekniken möjliggör överlappar varandra i de data som används för generering av funktionen mellan träning och testning exemplen som ligger nära utbildning avklippt. Beroende på datatillgänglighet, kan en även allvarligare uppdelning åstadkommas genom att inte använda någon av exemplen i uppsättningen test som ligger inom W tidsenheter av utbildning avklippt.

Från vårt arbete påträffades att regression modeller som används för att förutsäga återstående livslängd mer störs av läckageproblem och med en slumpmässig delning leder till extrema overfitting. På liknande sätt ska regressionsproblem delningen vara så att poster som hör till tillgångar med fel innan utbildning avklippta ska användas för träningsmängden och resurser som har fel när avklippt ska användas för att testa uppsättningen.

Som en allmän metod, en annan viktig bästa praxis för att dela data för träning och testning är att använda är en delning av Tillgångsnummer så att ingen av de resurser som används i utbildning används för att testa eftersom idé tester är att se till att när en ny tillgång används t o Kontrollera förutsägelser på, modellen tillhandahåller realistiska resultat.

### <a name="handling-imbalanced-data"></a>Hantering av imbalanced data
Klassificering problem om det finns fler exempel på en klass än de andra är data anses vara imbalanced. Vi rekommenderar vill vi ha tillräckligt med representanter för varje klass i träningsinformationen för att kunna skilja mellan olika klasser. Om en klass är mindre än 10% av data, kan vi säga att data är imbalanced och vi kallar underrepresented dataset minoritet klassen. I många fall kan det mycket, hitta imbalanced datauppsättningar där en klass kraftigt underrepresented jämfört med andra exempelvis genom att endast som utgör 0,001% för datapunkter. Klassen obalans är ett problem i många domäner inklusive att upptäcka bedrägerier, intrång och förutsägande Underhåll där fel har vanligtvis sällsynta förekomster av tillgångar som utgör minoritet klassen exemplen.

Om klassen obalans manipuleras prestanda för de flesta standard algoritmer för maskininlärning som de syftar till att minimera den övergripande Felfrekvensen. För en datauppsättning med 99% negativt klassen exempel och 1% positivt klassen exempel kan vi få 99% noggrannhet genom att helt enkelt etiketter alla instanser som negativt. Men det felaktigt klassificerar alla positivt exempel algoritmen är inte en användbar även om noggrannhet måttet är mycket hög. Vanliga utvärdering mätvärden, till exempel övergripande noggrannhet på frekvens för fel är därför inte tillräckliga vid imbalanced learning. Andra mätvärden, till exempel precision, återkallning, F1 resultat och kostnaden justerade ROC kurvor som används för utvärderingar vid imbalanced datauppsättningar som beskrivs i avsnittet utvärdering mått.

Det finns dock några metoder som hjälper avhjälpa klassen obalans problem. De två viktigaste som samlar in tekniker och cost känsliga learning.

#### <a name="sampling-methods"></a>Provtagning
Prover metoder i imbalanced learning består av ändring av datauppsättningen av vissa metoder för att ge en belastningsutjämnade dataset. Även om det finns en mängd olika provtagningar, de flesta direkt vidarebefordra som är slumpmässig oversampling och under provtagning.

Bara angivna, slumpmässiga oversampling väljer slumpmässigt från minoritet klass, replikeras de här exemplen och lägga till dem i datauppsättning för träning. Detta ökar antalet totala exemplen i minoritet klass och slutligen balansera antalet exempel på olika klasser. En risk för oversampling är att flera instanser av vissa exempel kan orsaka klassificeraren ska bli för specifika leder till overfitting.
Detta skulle leda till hög utbildning noggrannhet men ha mycket dåliga prestanda på den överblivna testa data. Däremot väljer slumpmässiga under provtagning slumpmässigt från majoritet klass och ta bort dessa exempel från datauppsättning för träning. Tar bort exempel från majoritet klass kanske dock klassificeraren att missa viktiga begrepp som rör majoritet klassen. Hybrid provtagning där minoritet klassen översamplas och majoritet klassen under samplas samtidigt är ett annat användbart sätt. Det finns många andra mer avancerade provtagningar är tillgängliga och effektiva provtagningsmetoderna för klassen obalans är ett populärt research område emot konstant uppmärksamhet och bidrag från många kanaler. Användning av olika metoder för att välja de mest effektiva lämnas vanligtvis till data-forskare att utforska och experimentera och kan variera mycket beroende på dataegenskaper. Dessutom är det viktigt för att se till att provtagning används enbart för utbildning, men inte testet.

#### <a name="cost-sensitive-learning"></a>Kostnad känsliga learning
I förutsägande Underhåll fel som utgör klassen minoritet av större intresse än vanliga exempel och därmed fokus ligger på prestandan hos algoritmen på fel är vanligtvis fokus. Detta kallas ofta olika förlust eller asymmetriska kostnaderna för misclassifying element i olika klasser där felaktigt förutsäga ett positivt som negativt kan kosta mer än vice versa. Den önskade klassificeraren ska kunna ge hög förutsägelsefunktionen via klassen minoritet utan att kraftigt precisionen för klassen majoritet.

Det finns flera sätt som du kan göra detta. Genom att tilldela en hög kostnad för felklassificering i klassen minoritet och försök att minska den sammanlagda kostnaden, problemet med olika förlorar kan hanteras effektivt med. Vissa maskininlärningsalgoritmer använder den här idén natur, till exempel SVMs (Support Vector datorer) där kostnaden för positiva och negativa exempel kan införlivas under utbildning. På samma sätt används metoderna och vanligtvis visa goda prestanda vid imbalanced data som ökar beslut trädet algoritmer.

## <a name="evaluation-metrics"></a>Utvärdering av mått
Som tidigare nämnts gör klass obalans sämre prestanda som algoritmer tenderar att klassificera majoritet klassen exempel bättre i kostnaden för minoritet klassen fall som totalt felklassificering fel har förbättrats avsevärt när majoritet klass är märkt med korrekt. Detta orsakar låg återkallning priser och blir ett större problem när kostnaden för falsklarm på företaget är mycket hög. Precisionen är den mest populära mått som används för att beskriva en klassificerare prestanda. Men som beskrivs ovan noggrannhet är ineffektiv och återspeglar inte verkliga prestanda för en klassificerare funktioner som den är mycket känslig för data-distributioner. I stället för andra utvärdering mått att utvärdera imbalanced learning problem. I sådana fall ska precision, återkallning och F1 resultat inledande mått titta på när du utvärderar prestanda för förutsägande Underhåll modellen. I förutsägande Underhåll anger återkallning priser hur många fel i uppsättningen test identifierades korrekt av modellen. Högre återkallning priser betyder modellen är att fånga true fel. Precision mått avser antalet falsklarm där lägre precision priser motsvarar högre falsklarm. F1 poäng anser både precision och återkalla priser med bästa värdet 1 och sämsta är 0.

Dessutom för binär klassificering, decile tabeller och lift är diagram mycket informativa när du utvärderar prestanda. De fokus ligger endast på klassen positivt (fel) och ger en mer komplex bild av algoritmen prestanda än vad som visas genom att titta på bara en fast operativa punkt på kurvan ROC (mottagare operativsystem egenskap).
Decile tabeller erhålls genom att beställa test exemplen enligt deras förväntade troliga fel beräknas av modell innan tröskelvärde besluta om den slutliga etiketten. Sorterad exempel grupperas sedan i deciles (d.v.s. 10% exemplen med största sannolikhet och 20%, 30% och så vidare). Genom att beräkna förhållandet mellan true positivt hastighet för varje decile och dess slumpmässiga baslinjen (d.v.s. 0.1, 0,2.) kan en uppskatta hur algoritmen prestanda ändras vid varje decile. Inga Lift scheman används för att rita decile värden genom att markera decile SANT positiva satsen jämfört med slumpmässiga true positiva satsen par för alla deciles. Vanligtvis är de första deciles fokus för resultaten eftersom här största vinster visas. Första deciles kan också ses som representativa för ”oskyddad” när den används för förebyggande underhåll.

## <a name="sample-solution-architecture"></a>Exempel lösningsarkitektur
När du distribuerar en lösning med förutsägande Underhåll kan är vi intresserad av en heltäckande lösning som ger en kontinuerlig cykel för datapåfyllning, lagring av data för modellen utbildning, funktionen generering, förutsägelse och visualisering av resultaten tillsammans med en avisering Generera mekanism, till exempel en tillgång övervakning instrumentpanel. Vi vill en pipeline för data som ger framtida insikter till användaren i ett kontinuerligt automatiserat sätt. En exempel förutsägande Underhåll arkitektur för sådana en IoT data pipeline illustreras i bild 8 nedan. I arkitekturen realtid telemetri samlas in till en Händelsehubb som lagrar strömmande data. Dessa data inhämtas av stream analytics för realtidsbearbetning av data, till exempel generering av funktionen. Funktionerna används sedan för att anropa webbtjänsten förutsägelsemodell och resultatet visas på instrumentpanelen. På samma gång infogade data också lagras i en historisk databas och samman med externa datakällor som lokala data databaser för att skapa utbildning exempel för modellering.
Samma datalager kan användas för batch poängsättning av exemplen och lagring av de resultat som kan användas igen och tillhandahåller förutsägbar rapporter på instrumentpanelen.

![Figur 8. Exempel lösningsarkitektur för förebyggande underhåll](./media/cortana-analytics-playbook-predictive-maintenance/example-solution-architecture-for-predictive-maintenance.png)

Figur 8. Exempel lösningsarkitektur för förebyggande underhåll

Mer information om varje komponent i arkitekturen finns i [Azure](https://azure.microsoft.com/) dokumentation.

