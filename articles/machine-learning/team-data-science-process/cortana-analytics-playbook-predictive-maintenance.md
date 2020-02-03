---
title: Azure AI-guide för lösningar för förebyggande underhåll - Team Data Science Process
description: En heltäckande beskrivning av datavetenskap som används av lösningar för förebyggande underhåll i flera branscher.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 5cd16280ba942404ffb23fd1c9d0e1a20af8c7c4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721820"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-guide för lösningar för förebyggande underhåll

## <a name="summary"></a>Sammanfattning

Förutsägande underhåll (**PdM**) är en populär tillämpning av förutsägelse analys som kan hjälpa företag i flera branscher att uppnå hög till gångs användning och besparingar i drifts kostnader. Den här guiden samlar in affärs-och analys rikt linjer och metod tips för att utveckla och distribuera PdM-lösningar med hjälp av [Microsoft Azure AI-plattforms](https://azure.microsoft.com/overview/ai-platform) teknik.

Till att börja med innehåller den här guiden branschspecifika företagsscenarier och processen för att kvalificera dig för dessa scenarier för kontaktar. Det finns också datakrav och teknik för modellering för att skapa PdM-lösningar. Innehållet i guiden är på data science process, inklusive steg för förberedelse av data, funktionsframställning, modellskapandet och driftsättning av modellen. Den här guiden visar en lista över en uppsättning mallar för lösningar för att snabba på programutvecklingen kontaktar för att komplettera dessa viktiga begrepp. Guiden hjälper också till användbara utbildningsresurser för läkare att lära dig mer om AI bakom datavetenskap. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science guide översikt och mål målgrupp
Den första delen av den här guiden beskriver vanliga affärsproblem, fördelarna med att använda PdM för att åtgärda dessa problem och visar några vanliga användningsområden. Beslutsfattare (BDMs) kommer att dra nytta av det här innehållet. Den andra delen förklarar datavetenskap bakom kontaktar och innehåller en lista över PdM-lösningar som skapats med principerna som beskrivs i den här guiden. Det ger också utbildningsvägar och pekare till utbildningsmaterial. Tekniska beslutsfattare (TDMs) kan vara bra det här innehållet.

| Börja med... | Om du är... |
|:---------------|:---------------|
| [Affärs fall för förutsägande underhåll](#business-case-for-predictive-maintenance) |en beslutsfattare inom företaget (BDM) vill minska driftstopp och driftskostnader och förbättra användningen av utrustning |
| [Data vetenskap för förutsägande underhåll](#data-science-for-predictive-maintenance) |en teknisk beslutsfattare (TDM) evaluerar kontaktar tekniker att förstå de unika databearbetning och AI-krav för förebyggande underhåll |
| [Solution-mallar för förutsägande underhåll](#solution-templates-for-predictive-maintenance)|en programvaruarkitekt eller AI-utvecklare som vill skapa snabbt en demonstration eller proof-of-concept |
| [Utbildnings resurser för förutsägande underhåll](#training-resources-for-predictive-maintenance) | allt eller något av ovanstående och vill lära dig de grundläggande koncepten bakom datavetenskap, verktyg och tekniker.

### <a name="prerequisite-knowledge"></a>Förhandskunskaper
Innehållet BDM förväntar sig inte läsaren ha kännedom tidigare data science. Grundläggande kunskaper om statistik och datavetenskap är användbart för TDM-innehåll. Kunskap om Azure Data och AI-tjänster, Python, R, XML och JSON rekommenderas. AI-tekniker implementeras i Python- och R-paket. Lösningsmallar implementeras med hjälp av Azure-tjänster, utvecklingsverktyg och SDK: er.

## <a name="business-case-for-predictive-maintenance"></a>Affärsfall för förutsägande Underhåll

Företag kräver kritiska utrustning att köras på högsta effektivitet och användning för att upptäcka deras avkastningen på investeringar. Dessa resurser kan vara allt från flygplansmotorer, syfte, hissar eller industriell chillers - som kostar miljoner - ned vardagliga installationer som kopiatorer, kaffe datorer eller Vattenkylare.
- Som standard förlitar sig de flesta företag på _rätt underhåll_, där delar byts ut som och när de inte fungerar. Korrigerande Underhåll garanterar delar används helt (därför inte slösa komponenten livslängd), men kostar verksamheten i driftstopp, arbete och oplanerat Underhåll krav (av timmar eller olämplig platser).
- På nästa nivå kan företag öva på _förebyggande underhåll_, där de fastställer den användbara livs längd för en del, och upprätthålla eller ersätta den innan ett problem uppstår. Förebyggande underhåll undviker oplanerade och kritiska fel. Men höga kostnader för schemalagd stillestånds tid, underutnyttjande av komponenten under dess livs längd och arbete fortfarande kvarstår.
- Målet med _förutsägande underhåll_ är att optimera balansen mellan korrigerat och förebyggande underhåll genom att aktivera _just i tids_ byte av komponenter. Den här metoden ersätter endast komponenterna när de ligger nära ett fel. Genom att utöka komponenten lifespans (jämfört med förebyggande underhåll) och minska oplanerat underhåll och arbetsbelastningen (över korrigerande underhåll), företag kan få kostnadsbesparingar och konkurrensmässiga fördelar.

## <a name="business-problems-in-pdm"></a>Affärsproblem i kontaktar
Företag möter operativa högrisk på grund av oväntade fel och har begränsad insikt i de grundläggande orsakerna till problem i komplexa system. Några av de viktiga affärsfrågor är:

- Identifiera avvikelser i utrustning eller systemets prestanda eller funktioner.
- Förutsäga om en tillgång kan misslyckas inom en snar framtid.
- Beräkna återstående livslängd för en tillgång.
- Identifiera de främsta orsakerna till fel på en tillgång.
- Identifiera vad underhållsåtgärder som behöver göras för en tillgång och på.

Vanliga mål instruktioner från PdM är:

- Minska riskerna verksamhetskritiska kritiska utrustning.
- Öka avkastningen på tillgångar genom att förutsäga fel innan de inträffar.
- Kontrollera kostnaden för underhåll genom att aktivera just-in-time-underhåll.
- Sänk kunden partikelhållfasthet, förbättra varumärke avbildningen och förlorade försäljning.
- Lägre kostnader för inventering genom att minska lagernivåer genom att förutsäga ändra ordning.
- Upptäck mönster som är anslutna till olika underhållsproblem.
- Ange KPI: er (nyckeltal), till exempel hälsotillstånd poäng för tillgången villkor.
- Beräkna återstående livslängd av tillgångar.
- Rekommenderar att du snabbt underhållsaktiviteter.
- Aktivera just-in-time-inventering genom att uppskatta beställningsdatum för utbyte av delar.

Dessa mål-uttryck är startpunkter för:

- _data_ tekniker för att analysera och lösa vissa förutsägelse problem.
- _moln arkitekter och utvecklare_ kan samla in en lösning från slut punkt till slut punkt.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalificera dig för problem för förebyggande underhåll
Det är viktigt att betona att inte alla användningsfall eller affärsproblem effektivt kan lösas genom kontaktar. Det finns tre viktiga kvalificerande kriterier som måste beaktas vid val av problemet:

- Problemet måste vara förutsägande i natur. det vill säga ska det vara ett mål eller ett resultat att förutsäga. Problemet bör även ha en tydlig sökväg till åtgärder för att förhindra fel när de identifieras.
- Problemet bör ha en post i drift historiken för den utrustning som innehåller _både bra och dåliga resultat_. Uppsättningen åtgärder som vidtas för att åtgärda felaktiga resultat ska också vara tillgänglig som en del av dessa poster. Felrapporter, loggar för underhåll av försämrade prestanda, reparera och Ersätt loggarna är också viktigt. Dessutom är reparationer åtagit sig att förbättra dem och ersättning poster också användbara.
- Den inspelade historiken bör avspeglas i _relevanta_ data som har tillräckligt med _tillräcklig_ kvalitet för att stödja användnings fallet. Mer information om data relevans och mer information finns i [data krav för förutsägande underhåll](#data-requirements-for-predictive-maintenance).
- Slutligen bör verksamheten ha områdesexperter som har en förståelse för problemet. De bör känna till den interna processer och praxis för att kunna hjälpa analytikern förstå och tolka data. De bör också kunna göra nödvändiga ändringar i befintliga affärsprocesser för att samla in rätt data för problem, om det behövs.

## <a name="sample-pdm-use-cases"></a>Exempel på användningsområden för kontaktar
Det här avsnittet fokuserar på en uppsättning kontaktar användningsfall från flera olika branscher, till exempel flygindustrin, verktyg och transport. Varje avsnitt börjar med ett affärsproblem och beskriver fördelarna med kontaktar, relevanta data omgivande affärsproblemet och slutligen fördelarna med en PdM-lösning.

| Affärsproblem | Fördelar med kontaktar |
|:-----------------|-------------------|
|**Skyddet**      |                   |
|_Avgångs-och uppsägnings fördröjningar_ på grund av mekaniska problem. Fel som inte kan åtgärdas i tid kan orsaka flyg så avbryts och störa schemaläggning och åtgärder. |Kontaktar lösningar kan förutse sannolikheten för ett flygplan som försenades eller har avbrutits på grund av motorfel.|
|_Flyg Plans motor delar inte_: ersättningar för luft dels motorer är bland de vanligaste underhålls aktiviteterna inom flyg branschen. Underhåll lösningar kräver noggrann hantering av lagerartiklar komponenttillgänglighet, leverans och planerar|Att kunna samla in information om komponenten tillförlitlighet leder till betydande minskning på investeringskostnader.|
|**Räntor** |                         |
|_ATM-fel_ är ett vanligt problem inom bank branschen. Problemet här är att rapportera sannolikheten att en ATM kontanter återkallar transaktion hämtar avbryts på grund av papper har fastnat eller en del pengar utgivaren. Utifrån förutsägelser för transaktionsfel kan Network betjänas proaktivt för att förhindra att fel inträffar.| I stället för att tillåta att datorn misslyckas halvvägs en transaktion, är önskvärt alternativet att programmet datorn för att neka tjänsten baserat på förutsägelser.|
|**Energi** |                          |
|_Lindnings turbin-problem_: vind turbiner är den huvudsakliga energi källan i miljö ansvariga länder/regioner och innefattar höga kapital kostnader. En viktig komponent i lindnings turbiner är Generator-motorn, vars haveri återger den ineffektiva turbinen. Det är också mycket billigare att åtgärda.|Förutse KPI: er, till exempel MTTF (tiden för felet) kan energibolag förhindra turbinen fel och se till att minimal avbrottstid. Fel sannolikhet informera tekniker för att övervaka syfte som sannolikt kommer att misslyckas snart och schemalägga en tidsbaserad Underhåll systemen. Förutsägande modeller ger insikter om olika faktorer som bidrar till fel, vilket hjälper till att tekniker bättre förstå rotorsaken till problem.|
|_Krets brytar haverier_: distribution av el till hus och företag kräver att energi linjer fungerar hela tiden för att garantera energi leverans. Utlösts hjälper att begränsa eller undvika skada power rader under överbelastning eller negativ väderförhållanden. Affärsproblem här är att förutsäga kretsbrytare fel.| Kontaktar lösningar att minska kostnaderna för reparation och öka livslängden för nätverksutrustning som utlösts. De hjälper att förbättra kvaliteten på power-nätverk genom att minska oväntade fel och avbrott i tjänsten.|
|**Transport och logistik** |    |
|_Problem med hiss dörr_: stora hiss företag tillhandahåller en full stack tjänst för miljon tals funktionella hissar runt om i världen. Hiss säkerhet, tillförlitlighet och drifttid är de viktigaste aspekterna för sina kunder. Dessa företag spåra dessa och andra attribut via sensorer, för att hjälpa dem med korrigerande och förebyggande underhåll. I en hiss viktigaste kunden problemet fungerar inte hiss dörrar. Problem i verksamheten är i det här fallet att tillhandahålla en kunskapsbas förutsägande program som beräknar potentialen orsakar dörren fel.| Hissar är investeringar för potentiellt en livslängd på 20 – 30 år. Så att varje potentiell försäljning kan vara konkurrenskraftiga; Därför är förväntningar för service och support hög. Förutsägande Underhåll kan ge dessa företag flera fördelar jämfört med sina konkurrenter i sin produkt och Tjänsterbjudanden.|
|_Hjul haverier_: arbets hjuls haverier för hälften av alla tränar och kostar miljarder till den globala järnvägs branschen. Hjul fel orsaka också rails försämras, vilket ibland gör rail att bryta tidigt. Rail radbrytningar leda till oåterkallelig händelser, t.ex urspårning. Om du vill undvika sådana instanser järnvägar övervaka prestanda för hjul och ersätta dem på ett förebyggande sätt. Affärsproblem här är förutsägelser av hjul fel.| Förutsägande underhåll av hjul hjälper med just-in-time-ersättning av hjul |
|_Subway träna dörr haverier_: en stor orsak till fördröjningar i Subway-åtgärder är dörr haverier på tåg bilar. Affärsproblem här är att förutsäga träna dörren fel.|Tidig medvetenhet om dörren fel eller antalet dagar tills dörren fel, kan företag-optimera träna dörren Underhåll scheman.|

Nästa avsnitt hämtar detaljer om hur du nytta av kontaktar fördelarna som beskrivs ovan.

## <a name="data-science-for-predictive-maintenance"></a>Datavetenskap för förebyggande underhåll

Det här avsnittet innehåller allmänna riktlinjer för data science principer och praxis för kontaktar. Den är avsedd att hjälpa en TDM, Lösningsarkitekt, eller en utvecklare lära dig om kraven och processen för att skapa AI-program för slutpunkt till slutpunkt för kontaktar. Du kan läsa det här avsnittet tillsammans med en granskning av de demonstrationer och proof-of-Concept-mallar som anges i [Solution templates för förutsägande underhåll](#solution-templates-for-predictive-maintenance). Du kan sedan använda dessa principer och bästa praxis för att implementera din PdM-lösning i Azure.

> [!NOTE]
> Den här guiden är inte avsedd att lära läsaren Data Science. Det finns flera användbara källor för att läsa mer i avsnittet om [utbildnings resurser för förebyggande underhåll](#training-resources-for-predictive-maintenance). I de [Solution templates](#solution-templates-for-predictive-maintenance) i guiden visas några av de här AI-teknikerna för vissa PdM-problem.

## <a name="data-requirements-for-predictive-maintenance"></a>Uppgifter som krävs för förebyggande underhåll

Någon beror på (a) kvaliteten på vad som undervisats och (b) möjligheten för eleven. Förutsägelsemodeller Läs mönster från historiska data och förutsäga framtida resultat med vissa sannolikhet baserat på dessa observerade mönster. En förutsägande modellnoggrannheten beror på plats, kontrollsystemet och kvaliteten på data för träning och testning. Den nya informationen som ”beräknas” med hjälp av den här modellen bör ha samma funktioner och schema som träning och testning. Funktionen egenskaper (typ, densitet, distribution och så vidare) av nya data bör matcha datauppsättningar träning och testning. Fokus för det här avsnittet finns på dessa krav.

### <a name="relevant-data"></a>Relevanta data

Först måste data vara _relevanta för problemet_. Överväg det användnings fall för _hjulet_ som beskrivs ovan – tränings data bör innehålla funktioner relaterade till hjul åtgärderna. Om problemet var att förutsäga fel i _traktions systemet_, måste tränings data omfatta alla olika komponenter för traktions systemet. Det första fallet riktar sig mot en viss komponent medan det andra fallet riktar sig mot fel på ett större undersystem. Allmän rekommendation är att utforma förutsägelse system om specifika komponenter i stället för större undersystem eftersom det senare kommer är mer utspridda data. Domän experten (se [kvalificerings problem för förutsägande underhåll](#qualifying-problems-for-predictive-maintenance)) bör hjälpa till att välja de mest relevanta del mängderna av data för analysen. Relevanta data källor beskrivs i detalj i [förberedelse av data för förutsägande underhåll](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Tillräckligt med data
Två frågor är vanliga när det gäller historikdata för fel: (1) ”hur många felhändelser krävs för att träna en modell”? (2) "hur många poster betraktas som" tillräckligt "?" Det finns inga slutgiltiga svar, utan endast regler för tummen. För (1) bättre mer antalet misslyckade händelser modellen. För (2), och det exakta antalet misslyckade händelser beror på data och kontexten för det aktuella problemet. Men å andra sidan, om en dator misslyckas alltför ofta sedan verksamheten ersätter den, vilket minskar fel instanser. Här igen och är vägledning från domänen experten viktigt. Det finns dock metoder för att hantera problemet med _sällsynta händelser_. De beskrivs i avsnittet [Hantera sambalanserade data](#handling-imbalanced-data).

### <a name="quality-data"></a>Data kvalitet
Data kvaliteten är kritiskt-varje värde för förutsägande attribut måste vara _korrekt_ tillsammans med värdet för mål variabeln. DQS är ett väl studerade område i statistik och datahantering och kan därför ut av den här guidens omfattning.

> [!NOTE]
> Det finns flera resurser och företagsprodukter att leverera kvalitet data. Ett exempel på referenser finns nedan:
> - Dasu T, Johnson, T., undersökande datautvinning och datarensning, Wiley, 2003.
> - [Analys av prov data, wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, kvantitativ data rensning för stora databaser](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der Loo, M, introduktion till data rengöring med R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Förberedelse av data för förutsägande Underhåll

### <a name="data-sources"></a>Datakällor

Relevanta datakällor för förutsägande Underhåll omfattar, men är inte begränsade till:
- Felhistorik
- Underhåll/reparation-historik
- Datorn driftsförhållanden
- Utrustning metadata

#### <a name="failure-history"></a>Felhistorik
Felhändelser är ovanligt inom kontaktar program. När du bygger förutsägande modeller, måste algoritmen som vill veta mer om en komponent normala operativa mönster, samt dess fel-mönster. Så träningsdata ska innehålla tillräckligt antal exempel från båda kategorier. Historik för Underhåll poster och delar ersättning är bra källor för att hitta felhändelser. Med hjälp av kunskap om vissa definieras avvikelser i träningsdata också som kodfel.

#### <a name="maintenancerepair-history"></a>Underhåll/reparation-historik
Underhålls historiken för en till gång innehåller information om komponenter som ersatts, reparations aktiviteter som utförs osv. Dessa händelser registrerar försämrings mönster. Avsaknad av den här viktiga informationen i träningsdata kan leda till vilseledande modellresultat. Felhistorik finns också i underhållsläge historik som särskilda felkoder eller beställningsdatum för delar. Ytterligare datakällor som påverkar fel mönster bör undersökas och tillhandahålls av områdesexperter.

#### <a name="machine-operating-conditions"></a>Datorn driftsförhållanden
Sensorn baserat (eller andra) strömmande data utrustning i åtgärden är en viktigt-datakälla. En nyckel i kontaktar antas att en dators hälsostatus försämras med tiden under dess rutinunderhåll. Informationen som förväntas innehålla tidsvarierande funktioner som samlar in den här föråldras mönster och avvikelser som leder till försämring. Den temporala delen av informationen som krävs för algoritmen att lära dig till felet och icke-fel mönster över tid. Baserat på dessa datapunkter kan algoritmen som lär sig att förutsäga hur många fler enheter av tid som en virtuell dator kan fortsätta att arbeta innan det misslyckas.

#### <a name="static-feature-data"></a>Statisk funktionsdata
Statiska funktioner är metadata om utrustningen. Exempel är enhetens tillverkare, modell, tillverkad datum, starta underhållsdatum, platsen för systemet och andra tekniska specifikationer.

Exempel på relevanta data för [exempel på PdM användnings fall](#sample-pdm-use-cases) är tabellen nedan:

| Användningsfall | Exempel på relevanta data |
|:---------|---------------------------|
|_Fördröjning och uppsägning av flygning_ | Flight flödesinformationen i form av flygning ben och sidan loggar. Flyg benets data innehåller routningsinformation, till exempel avgångs-/ankomst datum, tid, flyg plats, layovers osv. Sid loggen innehåller en serie fel och underhålls koder som registrerats av personal underhålls personalen.|
|_Delar av flyg Plans motor delar_ | Data som samlas in från sensorer med flygplan med information om de olika delarna. Underhåll poster att identifiera när komponentfel inträffat och när de har ersatts.|
|_ATM-problem_ | Sensoravläsningar för varje transaktion (deponera kontanter/kontroll) och leverans av kontanter. Information om avstånds mätning mellan anteckningar, antecknings tjock lek, anteckningens avstånd, kontrol lera attribut osv. Underhålls poster som innehåller felkoder, reparations information, senaste gången kassa utfyllnaden har fyllts i.|
|_Lindnings turbin-felkod_ | Sensorer övervakar turbin-villkor som temperatur, vridnings riktning, Power genered, generator hastighet osv. Data samlas in från flera lindnings turbiner från lindnings grupper som finns i olika regioner. Varje turbinen har vanligtvis flera sensoravläsningar vidarebefordrar mätning av faktisk användning med ett fast tidsintervall.|
|_Krets brytar avbrott_ | Underhåll av loggar med korrigerande, förebyggande och systematiskt åtgärder. Driftdata med automatisk och manuell kommandon som skickas till utlösts som för åtgärder som öppna och stänga. Metadata för enheten, till exempel datum för tillverkning, plats, modell osv. Specifikationer för krets brytare, till exempel spännings nivåer, geolokalisering, omgivande förhållanden.|
|_Problem med hiss dörr_| Hiss metadata, till exempel typ av hissen, tillverkad datum, Underhåll frekvens, skapa typ och så vidare. Teknisk information, till exempel många dörren cykler, genomsnittlig dörren Stäng tid. Felhistorik med orsaker.|
|_Hjul haverier_ | Sensor data som mäter hjul acceleration, bromsnings instanser, fart avstånd, hastighet osv. Statisk information om hjul som tillverkare, tillverkar datum. Feldata härleds från en del ordning databas som spårar beställningsdatum och kvantiteter.|
|_Subway träna dörr haverier_ | Dörren inledande och avslutande gånger, andra operativa data, till exempel aktuella villkor för träna dörrar. Statiska data omfattar tillgång identifierare, tid och kolumner i villkoret värden.|

### <a name="data-types"></a>Datatyper
Med ovanstående datakällor kan är de två huvudsakliga datatyper som observerats i kontaktar domän:

- _Temporala data_: operationell telemetri, maskin villkor, arbets order typer, prioritets koder som har tidsstämplar vid tidpunkten för registrering. Fel och underhåll/reparation användningshistorik har också tidsstämplar som är associerade med varje händelse.
- _Statiska data_: dator funktioner och operatörs funktioner i allmänhet är statiska eftersom de beskriver de tekniska specifikationerna för datorer eller operatörs attribut. Om dessa funktioner kan ändras med tiden, bör de har tidsstämplar som är kopplade till dem också.

Förutsägare och mål variabler bör förbearbetas/omvandlas till [numeriska, kategoriska och andra data typer](https://www.statsdirect.com/help/basics/measurement_scales.htm) beroende på vilken algoritm som används.

### <a name="data-preprocessing"></a>Förbearbeta data
Som en förutsättning för _funktions teknik_förbereder du data från olika strömmar för att skapa ett schema från vilket det är enkelt att skapa funktioner. Visualisera data först som en tabell med poster. Varje rad i tabellen representerar en utbildnings instans och kolumnerna representerar _förutsägande_ funktioner (kallas även oberoende attribut eller variabler). Organisera data så att den eller de sista kolumnerna är _målet_ (beroende variabel). För varje utbildnings instans tilldelar du en _etikett_ som värde för den här kolumnen.

Dela upp varaktigheten för sensordata i tidsenheter för den temporala data. Varje post ska tillhöra en tidsenhet för en till gång _och bör ha distinkt information_. Tidsenheter definieras baserat på affärsbehov i multipler av sekunder, minuter, timmar, dagar, månader, och så vidare. Tidsenheten _behöver inte vara samma som frekvensen för data insamling_. Om frekvensen är hög, visas data kan inte några betydande skillnad från en enhet till en annan. Anta exempelvis att temperatur samlades in var tionde sekund. Med hjälp av samma tid för utbildningsdata endast ökar antalet exempel utan att ange ytterligare information. För det här fallet är en strategi för bättre att använda medelvärde data över 10 minuter eller en timme baserat på affärsrelaterad motivering.

För statiska data.
- _Underhålls poster_: rå data för underhåll har ett till gångs-ID och tidsstämpel med information om underhålls aktiviteter som har utförts vid en viss tidpunkt. Transformera underhålls aktiviteter till _kategoriska_ -kolumner där varje kategori Beskrivning unikt mappar till en viss underhålls åtgärd. Schemat för Underhåll poster omfattar tillgång identifierare, tid och åtgärd för underhåll.

- _Fel poster_: fel eller fel orsaker kan registreras som specifika felkoder eller fel händelser som definieras av specifika affärs villkor. I fall där utrustningen som har flera felkoder ska experten domänen identifiera de som är relevanta för en målvariabel. Använd de återstående fel koderna eller villkoren för att skapa _förutsägbara_ funktioner som korrelerar med dessa fel. Schemat för fel poster omfattar tillgång identifierare, tid, fel eller felorsak - om det är tillgängligt.

- _Metadata för dator och operator_: slå samman dator-och operatörs data till ett schema för att associera en till gång med dess operatör, tillsammans med deras respektive attribut. Schemat för datorn villkor omfattar tillgång identifierare, tillgången funktioner, operatorn identifierare och operatorn funktioner.

Andra data bearbetnings steg omfattar _hantering av saknade värden_ och _normalisering_ av attributvärden. Detaljerad information är utanför omfattningen för den här guiden - finns i nästa avsnitt för vissa användbara referenser.

Med ovanstående som förbearbetats datakällor på plats, sista transformeringen innan funktionsframställning är att ansluta till ovanstående tabeller baserat på plats-ID och tidsstämpel. Den resulterande tabellen skulle ha null-värden för kolumnen fel när datorn är i normal drift. Dessa null-värden kan vara tillräknade genom en indikator för normal drift. Använd den här kolumnen för att skapa _etiketter för den förutsägande modellen_. Mer information finns i avsnittet om [modellerings tekniker för förutsägande underhåll](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Funktionstekniker
Funktioner är det första steget innan modellering av data. Dess roll i data vetenskaps processen [beskrivs här](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). En _funktion_ är ett förutsägande attribut för modellen – till exempel temperatur, tryck, vibrationer och så vidare. För PdM innebär funktionsframställning att abstrahera en dators hälsa över historiska data som samlas in via en volymer varaktighet. Den skiljer sig från dess peer-datorer, till exempel fjärrövervakning, avvikelseidentifiering och felidentifiering som. 

### <a name="time-windows"></a>Tidsfönster
Fjärrövervakning innebär rapportering av händelser som inträffar vid _tidpunkter_. Avvikelseidentifiering identifiering modeller utvärdera (poäng) inkommande dataströmmar flaggan avvikelser från och med punkter i tiden. Felidentifiering klassificerar fel vara specifika typer av när de inträffar punkter i tiden. PdM omfattar däremot förutsägelser av problem under en _framtida tids period_, baserat på funktioner som representerar dator beteende under _historisk tids period_. För kontaktar är funktionsdata från enskilda platser tid för brusig vara förutsägande. Därför måste data för varje funktion vara _smoothened_ genom att aggregera data punkter över tid Windows.

### <a name="lag-features"></a>Lag-funktioner
Affärskraven definierar hur långt modellen har att förutsäga i framtiden. I sin tur varaktigheten bidrar till att definiera ”hur långt tillbaka modellen måste söka” att göra dessa förutsägelser. Den här perioden för att se bakåt kallas _fördröjningen_och funktioner som har byggts över denna fördröjning kallas _fördröjnings funktioner_. Det här avsnittet beskrivs lag-funktioner som kan skapas från datakällor med tidsstämplar och skapa en funktion från statiska datakällor. Fördröjnings funktionerna är vanligt vis _numeriska_ i typ.

> [!IMPORTANT]
> Fönstrets storlek bestäms via experimentering och bör slutföras med hjälp av en domän som är experten. Samma villkor gäller för val av och definition av lag funktion, sina aggregeringar och vilken typ av windows.

#### <a name="rolling-aggregates"></a>Löpande aggregeringar
För varje post för en tillgång väljs ett rullande tidsfönster storlek ”W” som antalet tidsenheter att beräkna aggregeringar. Fördröjnings funktionerna beräknas sedan med hjälp av W-perioder _före datumet_ för den posten. I bild 1 visar blå raderna sensorvärdena registreras för en tillgång för varje enhet av time. De anger en rullande medelvärdet för funktionen värden över ett fönster med storleken W = 3. Det rullande medelvärdet beräknas över alla poster med tidsstämplar i intervallet t<sub>1</sub> (i orange) till t<sub>2</sub> (i grönt). Värdet för W är vanligtvis i minuter eller timmar beroende på typen av uppgift. Men för vissa problem, välja ett stort W (exempelvis 12 månader) kan tillhandahålla hela historiken för en tillgång tills posten.

![Bild 1. Löpande aggregerade funktioner](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

Bild 1. Löpande aggregerade funktioner

Exempel på löpande aggregeringar via ett tidsfönster är antal, genomsnitt, CUMESUM (kumulativa summan) mått, min/max-värden. Dessutom används ofta avvikelse, standardavvikelse och antal extremvärden utöver N standardavvikelser. Exempel på agg regeringar som kan användas för [användnings fall](#sample-pdm-use-cases) i den här hand boken visas nedan. 
- _Flyg fördröjning_: antal felkoder under den senaste dagen/veckan.
- _Del av flyg Plans motorn_: rullande medelvärde, standard avvikelse och Summa under den senaste dagen, veckan osv. Detta mått bör fastställas tillsammans med företags experten.
- _ATM-problem_: rullande medelvärden, median, intervall, standard avvikelser, antal avvikare utöver tre standard avvikelser, övre och nedre CUMESUM.
- _Subway träna dörr haverier_: antal händelser under den senaste dagen, veckan, två veckor osv.
- _Krets brytar haverier_: antal haverier under den senaste veckan, år, tre år osv.

En annan användbar teknik i kontaktar är att samla in trend ändringar, toppar och ändras med hjälp av algoritmer som identifierar avvikelser i data.

#### <a name="tumbling-aggregates"></a>Rullande aggregeringar
För varje etikettad post för en till gång definieras ett fönster med storleken _w-<sub>k</sub>_  , där _k_ är antalet fönster i storlek _w_. Agg regeringar skapas sedan över _k_ _rullande Windows_ _W-k, w-<sub>(k-1)</sub>,..., w-<sub>2</sub>, w-<sub>1</sub>_  för perioderna före en posts tidstämpel. _k_ kan vara ett litet tal för att samla in kortsiktiga effekter eller ett stort antal för att samla in långsiktiga försämrings mönster. (se bild 2).

![Figur 2. Rullande aggregerade funktioner](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

Figur 2. Rullande aggregerade funktioner

Till exempel lag funktioner för användningsfall för vind syfte kan skapas med W = 1 och k = 3. De innebär fördröjning för var och en av de senaste tre månaderna med hjälp av övre och nedre extremvärden.

### <a name="static-features"></a>Statiska funktioner

Tekniska specifikationer utrustning, till exempel datum till tillverkning, modellnummer, plats, är några exempel på statiska funktioner. De behandlas som _kategoriska_ -variabler för modellering. Några exempel på användningsfall kretsbrytare är spänning, aktuellt, Strömkapaciteten, omvandlare typ och strömkälla. För hjul fel är typ av däck hjul (legerat vs stål) ett exempel.

På arbetet för förberedelse av data beskrivs hittills ska leda till de data som är ordnade enligt nedan. Utbildning, testning och validering data bör ha det här logiska schemat (det här exemplet visar tid i antal dagar).

| Tillgångs-ID | Tid | \<funktions kolumner > | Etikett |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

Det sista steget i funktions teknik är **märkningen** av mål variabeln. Den här processen är beroende av teknik för modellering. I sin tur beror modellering tekniken på affärsproblem och arten av alla tillgängliga data. Märkning beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förberedelse av data och funktioner är lika viktigt som modellering tekniker för att komma fram till framgångsrika PdM-lösningar. Domänexpert och läkaren bör investera mycket tid på vägen mot rätt funktioner och data för modellen. Ett litet antal från många böcker om funktionsframställning visas nedan:
> - Pyle, D. dataförberedelse för datautvinning (Morgan Kaufmann serien i Data Management System), 1999
> - Zheng A., Casari A. Funktionsframställning för Machine Learning: principer och metoder för Dataexperter, O'Reilly, 2018.
> - Dong, G. LiU, H. (redigerare), funktions teknik för Machine Learning och data analys (Chapman & Hall/CRC-Data utvinning och identifierings serie för kunskap), CRC-tryck, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Teknik för modellering för förutsägande Underhåll

Det här avsnittet beskrivs teknik huvudsakliga modellering för kontaktar problem, tillsammans med viss etikett konstruktion metoderna. Observera att en enda modellering tekniken kan användas i olika branscher. Modellering tekniken är länkad till data science problemet i stället för kontexten för data till hands.

> [!IMPORTANT]
> Valet av etiketter för fel fall och strategi för etikettering  
> beror i samråd med domänen som är experten.

### <a name="binary-classification"></a>Binär klassificering
Binära klassificering används för att _förutsäga sannolikheten för att en del av utrustningen kraschar inom en framtida tids period_ , kallad den _framtida Horisont perioden X_. X bestäms av affärs problemet och de data som finns till hands, i samråd med domän experten. Några exempel:
- _minsta ledtid_ som krävs för att ersätta komponenter, distribuera underhålls resurser, utföra underhåll för att undvika ett problem som sannolikt inträffar under den perioden.
- _minsta antal händelser_ som kan inträffa innan ett problem uppstår.

Två typer av utbildning exempel identifieras i den här tekniken. Ett positivt exempel _som indikerar ett haveri_med etiketten = 1. En negativ exempel som visar normal drift, = 0 med etiketten. Mål variabeln, och därför är etikett värden _kategoriska_. Modellen måste identifiera varje ny exempel eftersom den sannolikt kan misslyckas eller fungerar som vanligt under närmaste X tidsenheter.

#### <a name="label-construction-for-binary-classification"></a>Etikett-konstruktion för binär klassificering
Den här frågan är ”: Vad är sannolikheten att tillgången inte fungerar i nästa X tidsenheter”? Besvara den här frågan, etikett X poster innan felet av en tillgång som ”om du kunde inte” (etikett = 1), och märka alla poster som är ”normal” (etikett = 0). (se bild 3).

![Bild 3. Etiketter för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

Bild 3. Etiketter för binär klassificering

Exempel på märkning strategi för några av användningsfallen listas nedan.
- _Fördröjningar i flygning_: X kan väljas som en dag för att förutsäga fördröjningar under de närmaste 24 timmarna. Sedan alla flygningar som ligger inom ett dygn innan fel är märkta som 1.
- _Problem med ATM-likviditeten_: ett mål kan vara att fastställa antalet lyckade försök för en transaktion inom en timme. I så fall kan är alla transaktioner som inom den senaste timmen av fel märkta som 1. För att förutse sannolikheten för fel under nästa N valuta är information vara, alla anteckningar vara inom de senaste N-anteckningarna av ett fel märkta som 1.
- _Krets brytar haverier_: målet kan vara att förutsäga nästa krets brytar kommando haveri. I så fall väljs X ska vara ett framtida kommando.
- _Träna dörr haverier_: X kan väljas som två dagar.
- _Lindnings turbin-problem_: X kan väljas som två månader.

### <a name="regression-for-predictive-maintenance"></a>Regression för förutsägande Underhåll
Regressions modeller används för att _Beräkna den återstående användbara livs längden (RUL) för en till gång_. RUL definieras som mängden tid som en tillgång är i drift innan nästa fel inträffar. Varje övnings exempel är en post som tillhör en tidsenhet _ny_ för en till gång, där _n_ är multipel. Modellen ska beräkna RUL för varje nytt exempel som ett _kontinuerligt tal_. Det här talet anger tidsperioden som återstår före felet.

#### <a name="label-construction-for-regression"></a>Etikett-konstruktion för regression
Den här frågan är: ”Vad är den återstående användbara livslängd (RUL)”? Beräkna etikett för att antalet tidsenheter kvar innan nästa fel för varje post innan felet. I den här metoden är etiketter kontinuerlig variabler. (Se bild 4)

![Bild 4. Etiketter för regression](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

Bild 4. Etiketter för regression

För regression görs märkning med referens till en felpunkt. Beräkningen är inte möjligt utan att känna till hur lång tid tillgången texten, har överlevt innan ett fel uppstod. Så kan i kontrast till binär klassificering tillgångar utan några fel i data inte användas för modellering. Det här problemet beskrivs bäst av en annan statistisk teknik som kallas [överlevnads analys](https://en.wikipedia.org/wiki/Survival_analysis). Men potentiella problem kan uppstå när du använder den här tekniken till kontaktar problem som rör tidsvarierande data med regelbundet. Mer information om överlevnads analys finns i [den här One-pager](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Flera klassificering för förebyggande underhåll
Flera klassificeringstekniker kan användas i kontaktar lösningar för två scenarier:
- Förutsäg _två framtida_resultat: det första resultatet är ett tidsintervall _som inte_ kan utföras för en till gång. Tillgången är tilldelad till en av flera möjliga tidsperioder. Det andra resultatet är sannolikheten för fel i en framtida period på grund av _en av flera Rotors_Aker. Den här förutsägelse kan Underhåll besättningen att bevaka symptom och plan underhållsscheman.
- Förutsäg _den mest sannolika rotor saken_ till ett angivet fel. Det här resultatet rekommenderar rätt uppsättning underhållsåtgärder för att åtgärda ett fel. En rankningslista över underliggande orsaker och rekommenderade reparationer kan tekniker prioritera sina repareringsåtgärder efter ett fel.

#### <a name="label-construction-for-multi-class-classification"></a>Etikett-konstruktion för flera klassificering
Frågan här är: "Vad är sannolikheten att en till gång kommer att Miss Miss i nästa _NZ_ -tidsenhet där _n_ är antalet perioder?" Besvara den här frågan, etikett nZ poster innan felet för en tillgång med buckets tid (3Z 2Z Z). Etikett för alla andra registrerar ”normal” (etikett = 0). I den här metoden innehåller Target-variabeln _kategoriska_ -värden. (Se bild 5).

![Bild 5. Tids förutsägelse etiketter för klassificering av multiklass](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Bild 5. Etiketter för flera klassificering för fel tid förutsägelse

Frågan här är: "Vad är sannolikheten för att till gången kommer att Miss förfaller inom de närmaste X tidsintervallen på grund av rotor saken/problem _P<sub>i</sub>_ ?" där _jag_ är antalet möjliga rotor orsaker. För att svara på den här frågan, etiketter X poster innan en till gång har misslyckats som "om att Miss lyckas på grund av rotor saken _P<sub>i</sub>_ " (etikett = _P<sub>i</sub>_ ). Etikettera alla poster som är ”normal” (etikett = 0). I den här metoden är etiketter dessutom kategoriska (se bild 6).

![Bild 6. Rotor Saks förutsägelse etiketter för klassificering av multiklass](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Bild 6. Etiketter för flera klassificering för rot orsak förutsägelse

Modellen tilldelar en fel sannolikhet på grund av varje _P<sub>i</sub>_  och sannolikheten att inget fel uppstår. Dessa sannolikheter kan beställas storlek så att förutsägelse av de problem som är mest sannolikt kan förekomma i framtiden.

Den här frågan är: ”vilka underhållsåtgärder rekommenderar ni efter ett fel”? För att svara på den här frågan _kräver inte etiketter att en framtida Horisont plockas_, eftersom modellen inte förutsäger felet i framtiden. Det förutsäger bara den mest sannolika rotor saken _när felet redan har inträffat_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Utbildning, validering och analysmetoder för förebyggande underhåll
[Team data science-processen](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) ger fullständig täckning av modell träna-test-validate-cykeln. Det här avsnittet diskuteras säkerhetsaspekter som är unika för kontaktar.

### <a name="cross-validation"></a>Korsvalidering
Målet med [kors validering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) är att definiera en data uppsättning för att "testa" modellen i övnings fasen. Den här data uppsättningen kallas för _verifierings uppsättningen_. Den här tekniken hjälper till att begränsa problem som _övermontering_ och ger en inblick i hur modellen generaliserar till en oberoende data uppsättning. Det vill säga en okänd för datauppsättning, vilket kan orsaka från verkliga problem. Rutinen utbildning och testning för kontaktar måste ta hänsyn till de olika delarna tid att generalisera bättre på överblivna framtida data.

Många machine learning-algoritmer är beroende av ett antal hyperparametrar som kan ändra modellprestanda avsevärt. Optimala värdena för dessa hyperparametrar beräknas inte automatiskt när träna modellen. De bör anges med data Science. Det finns flera sätt att hitta bra värden av hyperparametrar.

Det vanligaste är en _n-vik kors validering_ som delar upp exemplen slumpmässigt i _n_ -vik. För varje uppsättning med biparameter värden kör du Learning-algoritmen _k_ gånger. Vid varje iteration använder du exemplen i den aktuella vikningen som en uppsättning verifiering och resten av exemplen som en uppsättning för utbildning. Träna algoritmen över utbildning exempel och beräkna verifiering exempel prestandamåtten. I slutet av den här slingan beräknar du medelvärdet av _k_ prestanda mått. För varje uppsättning finjustering värden, väljer du de som har bästa genomsnittliga prestanda. Uppgiften för att välja hyperparametrar är ofta experimentella natur.

Kontaktar problem registreras data som en tidsserie med händelser som kommer från flera datakällor. Dessa poster kan sorteras enligt tidpunkten för märkning. Om data uppsättningen däremot är uppdelad _slumpmässigt_ i utbildning och validerings uppsättning, _kan några av inlärnings exemplen gå senare i tid än några av verifierings exemplen_. Framtida prestanda för de flesta parameter värden beräknas utifrån vissa data som anlänt _innan_ modellen tränades. Dessa beräkningar kan vara alltför optimistisk, särskilt om de time serien-inte står stilla och utvecklas över tid. De valda finjustering värdena kan därför vara icke-optimal.

Det rekommenderade sättet är att dela upp exemplen i utbildning och validerings uppsättning i ett _tidsberoende_ sätt, där alla verifierings exempel är senare i tid än alla inlärnings exempel. För varje uppsättning finjustering värden, träna algoritmen utifrån datauppsättningar för utbildning. Mäta modellens prestanda över samma verifiering. Välj finjustering värden som visar bästa möjliga prestanda. Finjustering värdena som väljs av träna/verifiering dela resulterar i bättre framtida modellens prestanda än med de värden som väljs slumpmässigt av korsvalidering.

Den slutliga modellen kan skapas genom att träna en inlärningsalgoritm över hela träningsdata med de bästa finjustering-värdena.

### <a name="testing-for-model-performance"></a>Testa för modellprestanda
När du har skapat en modell, krävs en uppskattning av framtida prestanda på nya data. En bra beräkna prestandamått för finjustering värden som beräknats över verifieringen eller ett genomsnittligt prestanda-mått beräknas från korsvalidering. Dessa beräkningar är ofta alltför optimistisk. Företaget kanske ofta vissa ytterligare riktlinjer för hur de vill testa modellen.

Det rekommenderade sättet för PdM är att dela upp exemplen i utbildning, verifiering och test av data uppsättningar på ett _tids beroende_ sätt. Alla test exemplen ska vara senare än alla utbildnings- och verifiering exemplen. Generera modellen efter delning, och mäter dess prestanda enligt beskrivningen ovan.

När time series-är stillastående och enkla att förutsäga, generera slumpmässiga och tidsberoende metoder liknande uppskattningar av framtida prestanda. Men när time series-är icke-stillastående och/eller svårt att förutse, tidsberoende metoden genererar mer realistiska uppskattningar av framtida prestanda.

### <a name="time-dependent-split"></a>Tidsberoende dela
Det här avsnittet beskriver Metodtips för att implementera tidsberoende dela. En tidsberoende dubbelriktad delning mellan utbildnings- och testuppsättningar beskrivs nedan.

Anta att en dataström med tidsstämplad händelser, t.ex mått från olika sensorer. Definiera funktioner och etiketter för träning och testning exempel över tidsramar som innehåller flera händelser. Till exempel, för binär klassificering, skapa funktioner baserade på tidigare händelser och skapa etiketter baserade på framtida händelser inom "X"-enheter i framtiden (se avsnitten om teknik för [teknik](#feature-engineering) och modellering). Därför kommer etikettering tidsram på ett exempel senare än tid som dess funktioner.

För tidsbaserad delning väljer du en _tränings period, T<sub>c</sub>_  , för att träna en modell, med grundparametrar som är justerade med historiska data upp till T<sub>c</sub>. För att förhindra läckage av framtida etiketter som ligger utanför T<sub>c</sub> i tränings data, väljer du den senaste tiden för att ge träna exempel till X-enheter före T<sub>c</sub>. I exemplet som visas på bild 7, representerar varje ruta en post i datauppsättningen där funktioner och etiketter beräknas enligt beskrivningen ovan. Bilden visar de poster som ska gå i utbildning och testningsuppsättningar för X = 2 och W = 3:

![Bild 7. Tidsberoende dela upp för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

Bild 7. Tidsberoende dela upp för binär klassificering

Grön rutor representerar poster som hör till enheterna som kan användas för utbildning. Varje övnings exempel genereras genom att ta hänsyn till de tre senaste perioderna för att skapa funktioner och två framtida perioder för etiketter före T. ex.<sub>c</sub>. När någon del av de två framtida perioderna är mer än T<sub>c</sub>, utesluter du det exemplet från träning-datauppsättningen eftersom ingen synlighet antas utöver T<sub>c</sub>.

De svarta rutorna representerar poster i den slutliga taggade datauppsättning som inte ska användas i datauppsättningen utbildning fått ovanstående villkor. Dessa poster används inte heller för att testa data, eftersom de är före T<sub>c</sub>. Dessutom kan beror deras etikettering tidsramar delvis på tidsramen utbildning som inte är idealiskt. Träning och testning data bör ha separata etikettering tidsramar för att förhindra att information läcker ut för etiketten.

Den teknik som diskuteras så att den kan överlappa varandra mellan utbildning och testning av exempel som har tidsstämplar nära T<sub>c</sub>. En lösning för att uppnå större separering är att utesluta exempel som ligger inom W Time-enheter om T<sub>c</sub> från test uppsättningen. Men en aggressiv dela beror på gott om datatillgänglighet.

Regressionsmodeller som används för att förutsäga återstående användbara Livslängden störs mer av läckageproblem. Med hjälp av metoden slumpmässiga dela leder till extrema över passning. Vid Regressions problem bör delningen vara sådan att de poster som hör till till gångar med fel<sub>innan T.</sub> ex. går till inlärnings uppsättningen. Poster över tillgångar med fel efter brytfrekvens går du till uppsättningar för testning.

En annan bästa praxis för att dela data för träning och testning är att använda en delning av tillgången-ID. Delningen ska vara så att ingen av de resurser som används i träningsmängden används i testa modellprestanda. En modell har med den här metoden större möjligheter till att tillhandahålla mer realistisk resultat med nya tillgångar.

### <a name="handling-imbalanced-data"></a>Hantering av imbalanced data
I klassificerings problem, om det finns fler exempel på en klass än de andra, så anses data uppsättningen vara _obalanserad_. Vi rekommenderar är tillräckligt med representanter för varje klass i träningsdata rekommenderat att aktivera skillnaderna mellan olika klasser. Om en klass är mindre än 10% av data, anses data som imbalanced. Den underrefererade klassen kallas en _minoritets klass_.

Många PdM problem inför sådana imbalanced datauppsättningar, där en klass kraftigt underrepresented jämfört med de andra klass eller klasser. I vissa situationer kan utgöra klassen minority endast 0,001% av totalt antal datapunkter. Klass obalans är inte unikt för kontaktar. Andra domäner där fel och avvikelser är ovanligt förekomster inför ett liknande problem, exempel, identifiering av bedrägerier och intrång. De här felen utgör minority klass exemplen.

Med klassen obalans i data, är prestanda för de flesta standard learning-algoritmer skadad, eftersom de försöka minska den övergripande Felfrekvensen. För en datamängd med 99% negativa och positiva exempel av 1%, kan en modell visas ha 99% noggrannhet genom märkning alla instanser som negativt. Men modellen kommer argumentantal klassificera alla positivt exemplen; Så även om dess Precision är hög algoritmen inte är en användbar. Därför räcker det inte med konventionella utvärderings mått, till exempel _den övergripande precisionen för fel frekvens_ för obalanserad inlärning. När möter med imbalanced datauppsättningar, som andra mått används för utvärdering av modellen:
- Precision
- Återkalla
- F1-poäng
- Kostnad justeras ROC (mottagare operativa egenskaper)

Mer information om dessa mått finns i [modell utvärdering](#model-evaluation).

Det finns dock några metoder som hjälper att åtgärda klass obalans problem. De två viktigaste är _samplings tekniker_ och _kostnads känslig inlärning_.

#### <a name="sampling-methods"></a>Sampling metoder
Imbalanced learning innebär användning av sampling metoder för att ändra träningsmängden data till en belastningsutjämnad uppsättning. Sampling metoder är inte tillämpas på test-uppsättningen. Även om det finns flera samplings tekniker är de flesta enkla vidarebefordran _slumpmässiga översamplingar_ och _under sampling_.

_Slumpmässig översampling_ innebär att du väljer ett slumpmässigt exempel från en minoritets klass, som replikerar de här exemplen och lägger till dem i tränings data uppsättning. Följaktligen hur många exemplen i minority klass ökas och så småningom balansera antalet exempel på olika klasser. En nackdel med oversampling är att flera instanser av vissa exempel som kan orsaka klassificerare att bli för specifika, vilket leder till stora passning. Modellen kan visa hög utbildning Precision, men dess prestanda på överblivna testdata kan vara icke-optimal.

_Slumpmässigt under samplingen_ är att välja ett slumpmässigt exempel från en majoritets klass och ta bort dessa exempel från tränings data uppsättning. Ta bort exempel från majoritet klass kan emellertid orsaka leder klassificerare att missa viktiga begrepp som hör till klassen majoritet. _Hybrid sampling_ där minoritets klass är över-sampel och majoritets klass är undersamplad på samma gång som en annan praktisk metod.

Det finns många avancerade provtagningar. Tekniken valt är beroende av egenskaper för data och resultatet av iterativa experiment med data Science.

#### <a name="cost-sensitive-learning"></a>Kostnad känsliga learning
I PdM är fel som utgör klassen minority mer intressanta än normala exempel. Så är fokuserar främst på den algoritmen prestanda på fel. Felaktigt att förutsäga en positiv klass som en negativ klass kan kosta mer än vice versa. Den här situationen kallas ofta olika förlust eller asymmetrisk kostnaden för argumentantal klassificera element till olika klasser. Perfekt klassificeraren ska leverera hög förutsägelsefunktionen över klassen minority utan att kompromissa med precisionen för klassen majoritet.

Det finns flera sätt att få balans. Tilldela en hög kostnad till felkonfigurerat klassificering i klassen minority för att lösa problemet med ojämn förlust, och försök att minska den sammanlagda kostnaden. Algoritmer som _SVMs (support Vector Machines)_ använder den här metoden i sin egen metod, genom att tillåta kostnad för positiva och negativa exempel som anges under utbildningen. På samma sätt visar de metoder som _utökat besluts träd_ vanligt vis prestanda med obalanserade data.

## <a name="model-evaluation"></a>Modell-utvärdering
Felkonfigurerat klassificeringen är ett betydande problem för kontaktar scenarier där kostnaden för falsklarm verksamheten är hög. Exempelvis kan en beslutet att stående baserat på en felaktig förutsägelser av motorfel störa scheman och reseplaner. Tar en dator offline från en sammanställningsrad kan leda till förlust av intäkter. Utvärdering av modellen med rätt prestandavärden mot nya testdata är så viktigt.

Vanliga prestandamått som används för att utvärdera kontaktar modeller beskrivs nedan:

- [Noggrannhet](https://en.wikipedia.org/wiki/Accuracy_and_precision) är det mest populära måttet som används för att beskriva en klassificerares prestanda. Men Precision är känsligt för data-distributioner och är en ineffektiv mått för scenarier med imbalanced datauppsättningar. Andra mått används i stället. Verktyg som till exempel en [förvirrings mat ris](https://en.wikipedia.org/wiki/Confusion_matrix) används för att beräkna och motivera modellens noggrannhet.
- [Precisionen](https://en.wikipedia.org/wiki/Precision_and_recall) för PdM-modeller avser antalet falska larm. Lägre precision av modellen motsvarar vanligtvis en högre andel falsklarm.
- [Åter kallelse](https://en.wikipedia.org/wiki/Precision_and_recall) frekvens anger hur många av felen i test uppsättningen som identifierades korrekt av modellen. Priserna för högre återkallande innebär modellen har lyckats identifiera SANT felen.
- [F1 Poäng](https://en.wikipedia.org/wiki/F1_score) är det harmoniska medelvärdet av precision och återkallande, med värdet mellan 0 (sämst) och 1 (bästa).

För binär klassificering
- [Mottagar drifts kurvor (Roc)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) är också ett populärt mått. I ROC kurvor tolkas modellprestanda baserat på en operativ fixtal på ROC.
- Men för PdM-problem är _decile-tabeller_ och lyft- _diagram_ mer informativa. De koncentrera sig på klassen positivt (fel) och ger en mer komplex bild algoritm-prestanda än ROC kurvor.
  - _Decile-tabeller_ skapas med hjälp av test exempel i en fallande ordning av felsannolikheter. Sorterad exemplen grupperas sedan i deciles (10% av exemplen med sannolikhet, och sedan 20%, 30% och så vidare). Förhållandet mellan (SANT positiva identifieringar) /(random baseline) för varje decile hjälper till att beräkna algoritmen prestanda vid varje decile. Slumpmässig baslinjen tar på värden 0.1, 0.2 och så vidare.
  - [Lyft diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) ritar decile sanna positiva positiva frekvens mot slumpmässig sann positiv kostnad för alla deciles. De första deciles är vanligtvis fokus resultat, eftersom de visar de största vinsterna. Första deciles kan också ses som är representativ för ”oskyddad”, när den används för kontaktar.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modellen driftsättning för förutsägande Underhåll

Fördelen är data science Övning realiserade endast när den tränade modellen är operationella. Det vill säga måste modellen distribueras till affärssystem för att göra förutsägelser baserade på nytt, tidigare överblivna data.  De nya data måste exakt följa _modellens signatur_ för den tränade modellen på två sätt:
- alla funktioner måste finnas i varje logisk instans (exempelvis en rad i en tabell) av nya data.
- den nya informationen måste bearbetas i förväg och var och en av funktionerna utformat, exakt på samma sätt som träningsdata.

Ovanstående procedur anges på många sätt i akademiska och industri-dokumentationen. Men alla följande instruktioner betyda samma sak:
- _Poäng för nya data_ med modellen
- _Använd modellen_ för nya data
- _Operationalisera_ modellen
- _Distribuera_ modellen
- _Kör modellen_ mot nya data

Som tidigare nämnts skiljer modellen driftsättning för kontaktar sig från dess peer-datorer. Scenarier som rör avvikelse identifiering och fel identifiering implementerar vanligt vis _online_ -bedömning (även kallat _real tids poängsättning_). Här _visar modellen varje_ inkommande post och returnerar en förutsägelse. För identifiering av avvikelser, förutsägelsen är en indikation om att ett fel uppstod (exempel: en klass SVM). Det vore för felidentifiering, typ eller klass av fel.

PdM omfattar däremot batch- _Poäng_. För att uppfylla modellen signaturen, vara funktionerna i den nya informationen utformat på samma sätt som träningsdata. För den stora datamängder som är typiskt för nya data kan aggregeras över tidsfönster funktioner och poängsättas i batch. Batch-bedömning sker vanligt vis i distribuerade system som [Spark](https://spark.apache.org/) eller [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Det finns ett par alternativ - både icke-optimal:
- Strömmande datamotorer hantera aggregering i windows i minnet. Så det kan vara gällande att de stöder online bedömning. Men dessa system är lämpliga för kompakta data i smal windows tid eller null-optimerade element över bredare windows. De kan inte skalas bra för kompakta data över bredare tidsfönster som visas i kontaktar scenarier.
- Om batchbedömning inte är tillgänglig är lösningen att anpassa online bedömning för att hantera nya data i små batchar i taget.

## <a name="solution-templates-for-predictive-maintenance"></a>Mallar för lösningar för förutsägande Underhåll

Den sista delen av den här guiden innehåller en lista över kontaktar lösningsmallar, självstudier och experiment som implementeras i Azure. Dessa PdM-program kan distribueras till en Azure-prenumeration inom några minuter i vissa fall. De kan användas som proof of concept demonstrationer, sandbox-miljöer att experimentera med alternativ eller acceleratorer för implementeringar av faktiska produktionen. Dessa mallar finns i [Azure AI Gallery](https://gallery.azure.ai) -eller [Azure-GitHub](https://github.com/Azure). Dessa olika exempel kommer att finnas i den här lösningsmallen över tid.

| # | Titel | Description |
|--:|:------|-------------|
| 2 | [Mall för lösningar för förebyggande underhåll i Azure](https://github.com/Azure/AI-PredictiveMaintenance) | En lösnings mall med öppen källkod som demonstrerar Azure ML-modellering och en fullständig Azure-infrastruktur som kan stödja förebyggande underhålls scenarier i samband med IoT-fjärrövervakning. |
| 3 | [Djup inlärning för förutsägande underhåll](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure Notebook med en demonstrations lösning för att använda LSTM-nätverk (Long-Shortest Memory) (en klass av aktuella neurala-nätverk) för förutsägande underhåll, med ett [blogg inlägg i det här exemplet](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Modellerings guide för förebyggande underhåll i R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Guide för hotmodellering i PdM med skript i R.|
| 5 | [Förebyggande underhåll i Azure för Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | En av de första PdM-lösningsmallar baserat på Azure ML v1.0 för flygplan underhåll. Den här guiden kommer från det här projektet. |
| 6 | [Azure AI Toolkit för IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI i IoT Edge med TensorFlow; Toolkit-paket djup inlärnings modeller i Azure IoT Edge-kompatibla Docker-behållare och exponera dessa modeller som REST-API: er.
| 7 | [Förutsägande underhåll av Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite datorer - förkonfigurerade lösningen. Flygplan kontaktar mallen Underhåll med IoT Suite. [Ett annat dokument och en](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) [genom gång](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) som är relaterad till samma projekt. |
| 8 | [Mall för förutsägelse underhåll med SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstration av återstående livslängd scenariot utifrån R services. |
| 9 | [Modellerings guide för förebyggande underhåll](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Data uppsättnings funktionen för flyg Plans underhåll som utformats med R med [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) och [data uppsättningar](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) och [Azure Notebook](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) och [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) i azureml v 1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Utbildningsresurser för förutsägande Underhåll

Microsoft Azure erbjuder utbildningsvägar för grundläggande koncepten bakom PdM-teknik, förutom innehåll och utbildning på allmänt begrepp för AI och praxis.

| Utbildning resurs  | Tillgänglighet |
|:-------------------|--------------|
| [Utbildnings väg för PdM med träd och slumpmässig skog](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Offentligt | 
| [Utbildnings väg för PdM med djup inlärning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Offentligt |
| [AI-utvecklare på Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Offentligt |
| [Microsoft AI-skola](https://aischool.microsoft.com/learning-paths) | Offentligt |
| [Azure AI-utbildning från GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Offentligt |
| [LinkedIn-utbildning](https://www.linkedin.com/learning) | Offentligt |
| [Microsoft AI YouTube-webb seminarier](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Offentligt |
| [Microsoft AI show](https://channel9.msdn.com/Shows/AI-Show) | Offentligt |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partner |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partner |

Dessutom är kostnadsfria MOOC (stora öppna onlinekurser) på AI online erbjuds av akademiska institutioner som Stanford och MIT och andra educational företag.
