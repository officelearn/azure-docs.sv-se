---
title: Azure AI-guide för lösningar för förebyggande underhåll | Microsoft Docs
description: En heltäckande beskrivning av datavetenskap som används av lösningar för förebyggande underhåll i flera branscher.
services: machine-learning
author: fboylu
manager: cgronlun
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: fboylu
ms.openlocfilehash: 2a5d8540dba491dec8b2da227b0e84c10ff09c77
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395017"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-guide för lösningar för förebyggande underhåll

## <a name="summary"></a>Sammanfattning

Förebyggande underhåll (**PdM**) är ett populärt program till förutsägande analys som hjälper företag i flera olika branscher att uppnå hög tillgången användning och besparingar i driftskostnader. Den här guiden innehåller företag och analytiska riktlinjer och bästa praxis att utveckla och distribuera kontaktar lösningar med hjälp av den [Microsoft Azure AI-plattformen](https://azure.microsoft.com/overview/ai-platform) teknik.

Till att börja med innehåller den här guiden branschspecifika företagsscenarier och processen för att kvalificera dig för dessa scenarier för kontaktar. Det finns också datakrav och teknik för modellering för att skapa PdM-lösningar. Innehållet i guiden är på data science process, inklusive steg för förberedelse av data, funktionsframställning, modellskapandet och driftsättning av modellen. Den här guiden visar en lista över en uppsättning mallar för lösningar för att snabba på programutvecklingen kontaktar för att komplettera dessa viktiga begrepp. Guiden hjälper också till användbara utbildningsresurser för läkare att lära dig mer om AI bakom datavetenskap. 

### <a name="data-science-guide-overview-and-target-audience"></a>Data Science guide översikt och mål målgrupp
Den första delen av den här guiden beskriver vanliga affärsproblem, fördelarna med att använda PdM för att åtgärda dessa problem och visar några vanliga användningsområden. Beslutsfattare (BDMs) kommer att dra nytta av det här innehållet. Den andra delen förklarar datavetenskap bakom kontaktar och innehåller en lista över PdM-lösningar som skapats med principerna som beskrivs i den här guiden. Det ger också utbildningsvägar och pekare till utbildningsmaterial. Tekniska beslutsfattare (TDMs) kan vara bra det här innehållet.

| Börja med... | Om du är... |
|:---------------|:---------------|
| [Affärsfall för förutsägande Underhåll](#Business-case-for-predictive-maintenance) |en beslutsfattare inom företaget (BDM) vill minska driftstopp och driftskostnader och förbättra användningen av utrustning |
| [Datavetenskap för förebyggande underhåll](#Data-Science-for-predictive-maintenance) |en teknisk beslutsfattare (TDM) evaluerar kontaktar tekniker att förstå de unika databearbetning och AI-krav för förebyggande underhåll |
| [Mallar för lösningar för förutsägande Underhåll](#Solution-templates-for-predictive-maintenance)|en programvaruarkitekt eller AI-utvecklare som vill skapa snabbt en demonstration eller proof-of-concept |
| [Utbildningsresurser för förutsägande Underhåll](#Training-resources-for-predictive-maintenance) | allt eller något av ovanstående och vill lära dig de grundläggande koncepten bakom datavetenskap, verktyg och tekniker.

### <a name="prerequisite-knowledge"></a>Förhandskunskaper
Innehållet BDM förväntar sig inte läsaren ha kännedom tidigare data science. Grundläggande kunskaper om statistik och datavetenskap är användbart för TDM-innehåll. Kunskap om Azure Data och AI-tjänster, Python, R, XML och JSON rekommenderas. AI-tekniker implementeras i Python- och R-paket. Lösningsmallar implementeras med hjälp av Azure-tjänster, utvecklingsverktyg och SDK: er.

## <a name="business-case-for-predictive-maintenance"></a>Affärsfall för förutsägande Underhåll

Företag kräver kritiska utrustning att köras på högsta effektivitet och användning för att upptäcka deras avkastningen på investeringar. Dessa resurser kan vara allt från flygplansmotorer, syfte, hissar eller industriell chillers - som kostar miljoner - ned vardagliga installationer som kopiatorer, kaffe datorer eller Vattenkylare.
- Som standard är de flesta företag förlitar sig på _korrigerande Underhåll_, där delar ersätts som och när de misslyckas. Korrigerande Underhåll garanterar delar används helt (därför inte slösa komponenten livslängd), men kostar verksamheten i driftstopp, arbete och oplanerat Underhåll krav (av timmar eller olämplig platser).
- På nästa nivå, företag Övning _förebyggande underhåll_, där de fastställa användbara livslängden för en del och underhålla och Ersätt det innan ett fel. Förebyggande underhåll undviker oplanerade och kritiska fel. Men de höga kostnaderna för schemalagd stilleståndstid under användning av komponenten innan dess fullständiga livslängd för användning och arbete som fortfarande är kvar.
- Målet med _förebyggande underhåll_ är att optimera balans mellan korrigerande och förebyggande underhåll genom att aktivera _just-in-time_ ersättning av komponenter. Den här metoden ersätter endast komponenterna när de ligger nära ett fel. Genom att utöka komponenten lifespans (jämfört med förebyggande underhåll) och minska oplanerat underhåll och arbetsbelastningen (över korrigerande underhåll), företag kan få kostnadsbesparingar och konkurrensmässiga fördelar.

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

- _dataexperter_ att analysera och lösa specifika förutsägande problem.
- _arkitekter och utvecklare i molnet_ sätta ihop en lösning från slutpunkt till slutpunkt.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalificera dig för problem för förebyggande underhåll
Det är viktigt att betona att inte alla användningsfall eller affärsproblem effektivt kan lösas genom kontaktar. Det finns tre viktiga kvalificerande kriterier som måste beaktas vid val av problemet:

- Problemet måste vara förutsägande i natur. det vill säga ska det vara ett mål eller ett resultat att förutsäga. Problemet bör även ha en tydlig sökväg till åtgärder för att förhindra fel när de identifieras.
- Problemet bör ha koll på operativa historiken för den utrustning som innehåller _både bra och felaktiga resultat_. Uppsättningen åtgärder som vidtas för att åtgärda felaktiga resultat ska också vara tillgänglig som en del av dessa poster. Felrapporter, loggar för underhåll av försämrade prestanda, reparera och Ersätt loggarna är också viktigt. Dessutom är reparationer åtagit sig att förbättra dem och ersättning poster också användbara.
- Inspelade historiken visas i _relevanta_ informationen av _tillräcklig_ tillräckligt med kvalitet för användningsfallet. Läs mer om data relevans och kontrollsystemet [uppgifter som krävs för förebyggande underhåll](#Data-requirements-for-predictive-maintenance).
- Slutligen bör verksamheten ha områdesexperter som har en förståelse för problemet. De bör känna till den interna processer och praxis för att kunna hjälpa analytikern förstå och tolka data. De bör också kunna göra nödvändiga ändringar i befintliga affärsprocesser för att samla in rätt data för problem, om det behövs.

## <a name="sample-pdm-use-cases"></a>Exempel på användningsområden för kontaktar
Det här avsnittet fokuserar på en uppsättning kontaktar användningsfall från flera olika branscher, till exempel flygindustrin, verktyg och transport. Varje avsnitt börjar med ett affärsproblem och beskriver fördelarna med kontaktar, relevanta data omgivande affärsproblemet och slutligen fördelarna med en PdM-lösning.

| Affärsproblem | Fördelar med kontaktar |
|:-----------------|-------------------|
|**Aviation**      |                   |
|_Flight fördröjning och avbokningar_ på grund av mekanisk problem. Fel som inte kan åtgärdas i tid kan orsaka flyg så avbryts och störa schemaläggning och åtgärder. |Kontaktar lösningar kan förutse sannolikheten för ett flygplan som försenades eller har avbrutits på grund av motorfel.|
|_Flygplan motorn delar fel_: byte av flygplan motorn delar är bland de vanligaste uppgifterna för underhåll i flygbranschen. Underhåll lösningar kräver noggrann hantering av lagerartiklar komponenttillgänglighet, leverans och planerar|Att kunna samla in information om komponenten tillförlitlighet leder till betydande minskning på investeringskostnader.|
|**Finans** |                         |
|_ATM-fel_ är ett vanligt problem inom bank-branschen. Problemet här är att rapportera sannolikheten att en ATM kontanter återkallar transaktion hämtar avbryts på grund av papper har fastnat eller en del pengar utgivaren. Utifrån förutsägelser för transaktionsfel kan Network betjänas proaktivt för att förhindra att fel inträffar.| I stället för att tillåta att datorn misslyckas halvvägs en transaktion, är önskvärt alternativet att programmet datorn för att neka tjänsten baserat på förutsägelser.|
|**Energi** |                          |
|_Vindkraft turbinen fel_: vind syfte är den viktigaste energikällan i ansvar för miljön länder/regioner och omfattar höga kapitalkostnader. En viktig del i vind syfte är att generator-motorn. det inte gick återger turbinen ineffektiv. Det är också mycket billigare att åtgärda.|Förutse KPI: er, till exempel MTTF (tiden för felet) kan energibolag förhindra turbinen fel och se till att minimal avbrottstid. Fel sannolikhet informera tekniker för att övervaka syfte som sannolikt kommer att misslyckas snart och schemalägga en tidsbaserad Underhåll systemen. Förutsägande modeller ger insikter om olika faktorer som bidrar till fel, vilket hjälper till att tekniker bättre förstå rotorsaken till problem.|
|_Kretsbrytare fel_: Distribution av el till hem och företag kräver power rader ska fungera när som helst att garantera energi leverans. Utlösts hjälper att begränsa eller undvika skada power rader under överbelastning eller negativ väderförhållanden. Affärsproblem här är att förutsäga kretsbrytare fel.| Kontaktar lösningar att minska kostnaderna för reparation och öka livslängden för nätverksutrustning som utlösts. De hjälper att förbättra kvaliteten på power-nätverk genom att minska oväntade fel och avbrott i tjänsten.|
|**Transport och logistik** |    |
|_Hiss dörren fel_: stora hiss företag tillhandahålla en fullständig stack-tjänst för miljontals funktionella hissar runt om i världen. Hiss säkerhet, tillförlitlighet och drifttid är de viktigaste aspekterna för sina kunder. Dessa företag spåra dessa och andra attribut via sensorer, för att hjälpa dem med korrigerande och förebyggande underhåll. I en hiss viktigaste kunden problemet fungerar inte hiss dörrar. Problem i verksamheten är i det här fallet att tillhandahålla en kunskapsbas förutsägande program som beräknar potentialen orsakar dörren fel.| Hissar är investeringar för potentiellt en livslängd på 20 – 30 år. Så att varje potentiell försäljning kan vara konkurrenskraftiga; Därför är förväntningar för service och support hög. Förutsägande Underhåll kan ge dessa företag flera fördelar jämfört med sina konkurrenter i sin produkt och Tjänsterbjudanden.|
|_Snurra fel_: snurra fel konto för hälften av alla träna urspårning och kostnad miljarder i branschen globala rail. Hjul fel orsaka också rails försämras, vilket ibland gör rail att bryta tidigt. Rail radbrytningar leda till oåterkallelig händelser, t.ex urspårning. Om du vill undvika sådana instanser järnvägar övervaka prestanda för hjul och ersätta dem på ett förebyggande sätt. Affärsproblem här är förutsägelser av hjul fel.| Förutsägande underhåll av hjul hjälper med just-in-time-ersättning av hjul |
|_Subway träna dörren fel_: en större orsak till fördröjningar i subway åtgärder är dörren fel av träna bilar. Affärsproblem här är att förutsäga träna dörren fel.|Tidig medvetenhet om dörren fel eller antalet dagar tills dörren fel, kan företag-optimera träna dörren Underhåll scheman.|

Nästa avsnitt hämtar detaljer om hur du nytta av kontaktar fördelarna som beskrivs ovan.

## <a name="data-science-for-predictive-maintenance"></a>Datavetenskap för förebyggande underhåll

Det här avsnittet innehåller allmänna riktlinjer för data science principer och praxis för kontaktar. Den är avsedd att hjälpa en TDM, Lösningsarkitekt, eller en utvecklare lära dig om kraven och processen för att skapa AI-program för slutpunkt till slutpunkt för kontaktar. Du kan läsa det här avsnittet tillsammans med en granskning av demonstrationerna och proof of concept-mallar som anges i [mallar för lösningar för förutsägande Underhåll](#Solution-templates-for-predictive-maintenance). Du kan sedan använda dessa principer och bästa praxis för att implementera din PdM-lösning i Azure.

> [!NOTE]
> Den här guiden är inte avsedd att lära läsaren Data Science. Flera användbara källor tillhandahålls för att läsa mer i avsnittet för [utbildningsresurser för förutsägande Underhåll](#Training-resources-for-predictive-maintenance). Den [lösningsmallar](#Solution-templates-for-predictive-maintenance) visas i guiden visar några av dessa AI-metoder för specifika kontaktar problem.

## <a name="data-requirements-for-predictive-maintenance"></a>Uppgifter som krävs för förebyggande underhåll

Någon beror på (a) kvaliteten på vad som undervisats och (b) möjligheten för eleven. Förutsägelsemodeller Läs mönster från historiska data och förutsäga framtida resultat med vissa sannolikhet baserat på dessa observerade mönster. En förutsägande modellnoggrannheten beror på plats, kontrollsystemet och kvaliteten på data för träning och testning. Den nya informationen som ”beräknas” med hjälp av den här modellen bör ha samma funktioner och schema som träning och testning. Funktionen egenskaper (typ, densitet, distribution och så vidare) av nya data bör matcha datauppsättningar träning och testning. Fokus för det här avsnittet finns på dessa krav.

### <a name="relevant-data"></a>Relevanta data

Först data måste vara _berör problemet_. Överväg att den _snurra fel_ användningsfall som beskrivs ovan - träningsdata ska innehålla funktioner som är relaterade till hjul-åtgärder. Om problemet har att förutsäga fel på den _dirigera system_, träningsdata måste omfatta de olika komponenterna för dirigera-system. Det första fallet riktar sig mot en viss komponent medan det andra fallet riktar sig mot fel på ett större undersystem. Allmän rekommendation är att utforma förutsägelse system om specifika komponenter i stället för större undersystem eftersom det senare kommer är mer utspridda data. Domänexpert (se [kvalificerande problem för förebyggande underhåll](#Qualifying-problems-for-predictive-maintenance)) är till hjälp vid att välja de mest relevanta delmängderna av data för analys. Relevanta datakällor beskrivs mer detaljerat i [förberedelse av Data för förutsägande Underhåll](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Tillräckligt med data
Två frågor är vanliga när det gäller historikdata för fel: (1) ”hur många felhändelser krävs för att träna en modell”? (2) ”hur många poster betraktas som” tillräckligt ””? Det finns inga slutgiltiga svar, men endast tumregel. För (1) bättre mer antalet misslyckade händelser modellen. För (2), och det exakta antalet misslyckade händelser beror på data och kontexten för det aktuella problemet. Men å andra sidan, om en dator misslyckas alltför ofta sedan verksamheten ersätter den, vilket minskar fel instanser. Här igen och är vägledning från domänen experten viktigt. Det finns dock metoder för att hantera problemet med _sällsynta händelser_. De beskrivs i avsnittet [hantering av imbalanced data](#Handling-imbalanced-data).

### <a name="quality-data"></a>Data kvalitet
Kvaliteten på data är viktigt – varje ge säkrare prognoser attributvärdet måste vara _korrekt_ tillsammans med värdet för en målvariabel. DQS är ett väl studerade område i statistik och datahantering och kan därför ut av den här guidens omfattning.

> [!NOTE]
> Det finns flera resurser och företagsprodukter att leverera kvalitet data. Ett exempel på referenser finns nedan:
> - Dasu T, Johnson, T., undersökande datautvinning och datarensning, Wiley, 2003.
> - [Undersökande dataanalys, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein J, kvantitativa Data rensning för stora databaser](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der slå, M, introduktion till Datarensning med R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

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
Underhåll historiken för en tillgång innehåller information om komponenter som ersätts, reparera aktiviteter som utförs osv. De här händelserna registrera försämring mönster. Avsaknad av den här viktiga informationen i träningsdata kan leda till vilseledande modellresultat. Felhistorik finns också i underhållsläge historik som särskilda felkoder eller beställningsdatum för delar. Ytterligare datakällor som påverkar fel mönster bör undersökas och tillhandahålls av områdesexperter.

#### <a name="machine-operating-conditions"></a>Datorn driftsförhållanden
Sensorn baserat (eller andra) strömmande data utrustning i åtgärden är en viktigt-datakälla. En nyckel i kontaktar antas att en dators hälsostatus försämras med tiden under dess rutinunderhåll. Informationen som förväntas innehålla tidsvarierande funktioner som samlar in den här föråldras mönster och avvikelser som leder till försämring. Den temporala delen av informationen som krävs för algoritmen att lära dig till felet och icke-fel mönster över tid. Baserat på dessa datapunkter kan algoritmen som lär sig att förutsäga hur många fler enheter av tid som en virtuell dator kan fortsätta att arbeta innan det misslyckas.

#### <a name="static-feature-data"></a>Statisk funktionsdata
Statiska funktioner är metadata om utrustningen. Exempel är enhetens tillverkare, modell, tillverkad datum, starta underhållsdatum, platsen för systemet och andra tekniska specifikationer.

Exempel på relevanta data för den [exempel kontaktar användningsfall](#Sample-PdM-use-cases) i tabellen nedan:

| Användningsfall | Exempel på relevanta data |
|:---------|---------------------------|
|_Flygförseningen och avbokningar_ | Flight flödesinformationen i form av flygning ben och sidan loggar. Flight ben data inkluderar routning information som han eller hon lämnar/ankomst datum, tid, flygplatsen, layovers osv. Sidan loggen innehåller ett antal fel och underhåll koder som har registrerats av underhållspersonal grunden.|
|_Delar av flygplan motorfel_ | Data som samlas in från sensorer med flygplan med information om de olika delarna. Underhåll poster att identifiera när komponentfel inträffat och när de har ersatts.|
|_ATM-fel_ | Sensoravläsningar för varje transaktion (deponera kontanter/kontroll) och leverans av kontanter. Information på mätning klyftan mellan anteckningar, Observera tjocklek, Observera ankomst avståndet, kontrollera attribut osv. Underhåll poster som innehåller felkoder, reparationsinformation senast tid kontanter utgivaren har fyllas.|
|_Vind turbinen fel_ | Sensorer övervaka turbinen villkor, till exempel temperatur, vindriktning, power genereras, generator hastighet osv. Data har samlats in från flera vind sig från vind-grupper som finns i olika regioner. Varje turbinen har vanligtvis flera sensoravläsningar vidarebefordrar mätning av faktisk användning med ett fast tidsintervall.|
|_Kretsbrytare fel_ | Underhåll av loggar med korrigerande, förebyggande och systematiskt åtgärder. Driftdata med automatisk och manuell kommandon som skickas till utlösts som för åtgärder som öppna och stänga. Enhetsmetadata, till exempel datum för tillverkning, plats, modell osv. Kretsbrytare specifikationer som spänning nivåer, geoplats, omgivande villkor.|
|_Hiss dörren fel_| Hiss metadata, till exempel typ av hissen, tillverkad datum, Underhåll frekvens, skapa typ och så vidare. Teknisk information, till exempel många dörren cykler, genomsnittlig dörren Stäng tid. Felhistorik med orsaker.|
|_Hjul fel_ | Sensordata att mått snurra acceleration, bromsar instanser, utveckla avståndet, hastighet osv. Statisk information på hjul som tillverkare som tillverkade datum. Feldata härleds från en del ordning databas som spårar beställningsdatum och kvantiteter.|
|_Subway träna dörren fel_ | Dörren inledande och avslutande gånger, andra operativa data, till exempel aktuella villkor för träna dörrar. Statiska data omfattar tillgång identifierare, tid och kolumner i villkoret värden.|

### <a name="data-types"></a>Datatyper
Med ovanstående datakällor kan är de två huvudsakliga datatyper som observerats i kontaktar domän:

- _Den temporala data_: operativ telemetri, datorn villkor, arbete ordning typer prioritetskoder som har tidsstämplar vid tidpunkten för inspelning. Fel och underhåll/reparation användningshistorik har också tidsstämplar som är associerade med varje händelse.
- _Statiska data_: datorn funktioner och operatorn funktioner i allmänhet är statiska eftersom de beskriver de tekniska specifikationerna för datorer eller operator-attribut. Om dessa funktioner kan ändras med tiden, bör de har tidsstämplar som är kopplade till dem också.

Ge säkrare prognoser och target-variabler ska vara meddelandefilsposter/omvandlas till [numeriska kategoriska och andra datatyper](https://www.statsdirect.com/help/basics/measurement_scales.htm) beroende på vilken algoritm som används.

### <a name="data-preprocessing"></a>Förbearbeta data
Som ett krav att _funktionstekniker_, förbereda data från olika strömmar till att skapa ett schema som det är enkelt att skapa funktioner. Visualisera data först som en tabell med poster. Varje rad i tabellen representerar en instans för utbildning och kolumnerna representerar _ge säkrare prognoser_ funktioner (kallas även oberoende attribut eller variabler). Ordna data så att den eller de sista kolumnerna är den _target_ (beroende variabel). För varje instans utbildning kan tilldela en _etikett_ som värde för den här kolumnen.

Dela upp varaktigheten för sensordata i tidsenheter för den temporala data. Varje post ska tillhöra en tidsenhet för en tillgång _och bör erbjuda distinkta information_. Tidsenheter definieras baserat på affärsbehov i multipler av sekunder, minuter, timmar, dagar, månader, och så vidare. Tidsenheten _behöver inte vara samma som frekvens för insamling av_. Om frekvensen är hög, visas data kan inte några betydande skillnad från en enhet till en annan. Anta exempelvis att temperatur samlades in var tionde sekund. Med hjälp av samma tid för utbildningsdata endast ökar antalet exempel utan att ange ytterligare information. För det här fallet är en strategi för bättre att använda medelvärde data över 10 minuter eller en timme baserat på affärsrelaterad motivering.

För statiska data.
- _Underhåll poster_: Raw Underhåll data har en plats-ID och tidsstämpel med information om underhållsaktiviteter som har utförts vid en viss tidpunkt. Omvandla underhållsaktiviteter i _kategoriska_ kolumner, där varje kategori descriptor unikt mappar till en specifik underhållsåtgärd. Schemat för Underhåll poster omfattar tillgång identifierare, tid och åtgärd för underhåll.

- _Fel poster_: fel eller orsaker till felet kan registreras som specifika felkoder eller felhändelser som definieras av specifika villkor. I fall där utrustningen som har flera felkoder ska experten domänen identifiera de som är relevanta för en målvariabel. Använda återstående felkoder eller villkor för att konstruera _ge säkrare prognoser_ funktioner som att göra med de här felen. Schemat för fel poster omfattar tillgång identifierare, tid, fel eller felorsak - om det är tillgängligt.

- _Metadata för datorn och operatorn_: Koppla datorn och den valda operatorn data till ett schema för att associera en tillgång med dess operator, tillsammans med deras respektive attribut. Schemat för datorn villkor omfattar tillgång identifierare, tillgången funktioner, operatorn identifierare och operatorn funktioner.

Andra data Förbearbeta steg innehålla _hanterar saknade värden_ och _normalisering_ av attributvärden. Detaljerad information är utanför omfattningen för den här guiden - finns i nästa avsnitt för vissa användbara referenser.

Med ovanstående som förbearbetats datakällor på plats, sista transformeringen innan funktionsframställning är att ansluta till ovanstående tabeller baserat på plats-ID och tidsstämpel. Den resulterande tabellen skulle ha null-värden för kolumnen fel när datorn är i normal drift. Dessa null-värden kan vara tillräknade genom en indikator för normal drift. Använd den här fel-kolumnen för att skapa _etiketter för förutsägelsemodellen_. Mer information finns i avsnittet på [modellering tekniker för förutsägande Underhåll](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Funktionstekniker
Funktioner är det första steget innan modellering av data. Dess roll i data science process [som beskrivs här](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). En _funktionen_ är ett förutsägande attribut för modellen – till exempel temperatur, tryck, vibrationer och så vidare. För PdM innebär funktionsframställning att abstrahera en dators hälsa över historiska data som samlas in via en volymer varaktighet. Den skiljer sig från dess peer-datorer, till exempel fjärrövervakning, avvikelseidentifiering och felidentifiering som. 

### <a name="time-windows"></a>Tidsfönster
Fjärrövervakning innefattar rapporteringshändelser som sker med _tidpunkter_. Avvikelseidentifiering identifiering modeller utvärdera (poäng) inkommande dataströmmar flaggan avvikelser från och med punkter i tiden. Felidentifiering klassificerar fel vara specifika typer av när de inträffar punkter i tiden. Däremot kontaktar innebär att förutsäga fel över en _framtida tidsperiod_, baserat på funktioner som representerar datorn beter sig över _historiska tidsperiod_. För kontaktar är funktionsdata från enskilda platser tid för brusig vara förutsägande. Så data för varje funktion måste vara _smoothened_ genom att sammanställa datapunkter över tidsfönster.

### <a name="lag-features"></a>Lag-funktioner
Affärskraven definierar hur långt modellen har att förutsäga i framtiden. I sin tur varaktigheten bidrar till att definiera ”hur långt tillbaka modellen måste söka” att göra dessa förutsägelser. Den här 'ser tillbaka' tidsperiod kallas den _fördröjning_, och funktioner som utvecklats under perioden fördröjning kallas _lag funktioner_. Det här avsnittet beskrivs lag-funktioner som kan skapas från datakällor med tidsstämplar och skapa en funktion från statiska datakällor. Lag funktioner är vanligtvis _numeriska_ sin natur.

> [!IMPORTANT]
> Fönstrets storlek bestäms via experimentering och bör slutföras med hjälp av en domän som är experten. Samma villkor gäller för val av och definition av lag funktion, sina aggregeringar och vilken typ av windows.

#### <a name="rolling-aggregates"></a>Löpande aggregeringar
För varje post för en tillgång väljs ett rullande tidsfönster storlek ”W” som antalet tidsenheter att beräkna aggregeringar. Lag funktioner beräknas sedan med W perioder _före datumet_ för den posten. I bild 1 visar blå raderna sensorvärdena registreras för en tillgång för varje enhet av time. De anger en rullande medelvärdet för funktionen värden över ett fönster med storleken W = 3. Det rullande medelvärdet beräknas över alla poster med tidsstämplar som är inom räckhåll t<sub>1</sub> (i orange) till t<sub>2</sub> (i grönt). Värdet för W är vanligtvis i minuter eller timmar beroende på typen av uppgift. Men för vissa problem, välja ett stort W (exempelvis 12 månader) kan tillhandahålla hela historiken för en tillgång tills posten.

![Bild 1. Löpande aggregerade funktioner](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) bild 1. Löpande aggregerade funktioner

Exempel på löpande aggregeringar via ett tidsfönster är antal, genomsnitt, CUMESUM (kumulativa summan) mått, min/max-värden. Dessutom används ofta avvikelse, standardavvikelse och antal extremvärden utöver N standardavvikelser. Exempel på aggregeringar som kan användas för den [användningsfall](#Sample-PdM-use-cases) i den här handboken finns nedan. 
- _Flight fördröjning_: antal felkoder över sista dag/vecka.
- _Flygplan motorn en del fel_: löpande innebär, standardavvikelse och summan under den senaste dagen vecka osv. Det här måttet ska fastställas tillsammans med experten företagsdomänen.
- _ATM-fel_: löpande innebär, median, intervallet, standardavvikelser, antal extremvärden utöver tre standardavvikelser övre och nedre CUMESUM.
- _Subway träna dörren fel_: antal händelser över senaste dag, vecka, två veckor osv.
- _Kretsbrytare fel_: fel antal över senaste veckan, år, tre år osv.

En annan användbar teknik i kontaktar är att samla in trend ändringar, toppar och ändras med hjälp av algoritmer som identifierar avvikelser i data.

#### <a name="tumbling-aggregates"></a>Rullande aggregeringar
Etikett för varje post för en tillgång, ett fönster med storleken _W -<sub>k</sub>_  har definierats, där _k_ är antalet windows storlek _W_. Aggregeringar skapas sedan över _k_ _rullande fönster_ _W-k, W -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  för perioder före en post tidsstämpel. _k_ kan vara ett fåtal avbilda kortsiktig effekterna eller ett stort antal avbilda långsiktiga försämring mönster. (se bild 2).

![Figur 2. Rullande aggregerade funktioner](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) bild 2. Rullande aggregerade funktioner

Till exempel lag funktioner för användningsfall för vind syfte kan skapas med W = 1 och k = 3. De innebär fördröjning för var och en av de senaste tre månaderna med hjälp av övre och nedre extremvärden.

### <a name="static-features"></a>Statiska funktioner

Tekniska specifikationer utrustning, till exempel datum till tillverkning, modellnummer, plats, är några exempel på statiska funktioner. De behandlas som _kategoriska_ variabler för modellering. Några exempel på användningsfall kretsbrytare är spänning, aktuellt, Strömkapaciteten, omvandlare typ och strömkälla. För hjul fel är typ av däck hjul (legerat vs stål) ett exempel.

På arbetet för förberedelse av data beskrivs hittills ska leda till de data som är ordnade enligt nedan. Utbildning, testning och validering data bör ha det här logiska schemat (det här exemplet visar tid i antal dagar).

| Tillgångs-ID | Tid | <Feature Columns> | Etikett |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

Det sista steget i funktionsframställning är den **märkning** på mål-variabeln. Den här processen är beroende av teknik för modellering. I sin tur beror modellering tekniken på affärsproblem och arten av alla tillgängliga data. Märkning beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förberedelse av data och funktioner är lika viktigt som modellering tekniker för att komma fram till framgångsrika PdM-lösningar. Domänexpert och läkaren bör investera mycket tid på vägen mot rätt funktioner och data för modellen. Ett litet antal från många böcker om funktionsframställning visas nedan:
> - Pyle, D. dataförberedelse för datautvinning (Morgan Kaufmann serien i Data Management System), 1999
> - Zheng A., Casari A. Funktionsframställning för Machine Learning: principer och metoder för Dataexperter, O'Reilly, 2018.
> - Dong G. Liu, H. (Redigerare), funktionen teknik för Maskininlärning och dataanalys Chapman & Hall/CRC datautvinning och Knowledge Discovery-serien, CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Teknik för modellering för förutsägande Underhåll

Det här avsnittet beskrivs teknik huvudsakliga modellering för kontaktar problem, tillsammans med viss etikett konstruktion metoderna. Observera att en enda modellering tekniken kan användas i olika branscher. Modellering tekniken är länkad till data science problemet i stället för kontexten för data till hands.

> [!IMPORTANT]
> Valet av etiketter för fel fall och strategi för etikettering  
> beror i samråd med domänen som är experten.

### <a name="binary-classification"></a>Binär klassificering
Binär klassificering används för att _sannolikhet utrustning misslyckas i en framtida tidsperiod_ – kallas den _framtida horizon period X_. X bestäms av problem i verksamheten och data till hands i samråd med domänen som är experten. Några exempel:
- _minsta ledtid_ krävs för att ersätta komponenter, distribuera Underhåll resurser, utföra underhåll för att undvika ett problem som kan uppstå under denna tid.
- _minsta antal händelser_ som kan inträffa innan ett problem uppstår.

Två typer av utbildning exempel identifieras i den här tekniken. Ett positivt exempel _som indikerar ett fel_, med etiketten = 1. En negativ exempel som visar normal drift, = 0 med etiketten. En målvariabel och kan därför etikettvärden är _kategoriska_. Modellen måste identifiera varje ny exempel eftersom den sannolikt kan misslyckas eller fungerar som vanligt under närmaste X tidsenheter.

#### <a name="label-construction-for-binary-classification"></a>Etikett-konstruktion för binär klassificering
Den här frågan är ”: Vad är sannolikheten att tillgången inte fungerar i nästa X tidsenheter”? Besvara den här frågan, etikett X poster innan felet av en tillgång som ”om du kunde inte” (etikett = 1), och märka alla poster som är ”normal” (etikett = 0). (se bild 3).

![Bild 3. Etiketter för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) bild 3. Etiketter för binär klassificering

Exempel på märkning strategi för några av användningsfallen listas nedan.
- _Flight fördröjningar_: X kan väljas som 1 dag att förutsäga fördröjningar i 24 timmar. Sedan alla flygningar som ligger inom ett dygn innan fel är märkta som 1.
- _ATM kontanter befria fel_: ett mål kan vara att avgöra sannolikheten för fel för en transaktion i nästa en timme. I så fall kan är alla transaktioner som inom den senaste timmen av fel märkta som 1. För att förutse sannolikheten för fel under nästa N valuta är information vara, alla anteckningar vara inom de senaste N-anteckningarna av ett fel märkta som 1.
- _Kretsbrytare fel_: Målet kan vara att förutsäga nästa kretsbrytare-Kommandofel. I så fall väljs X ska vara ett framtida kommando.
- _Träna dörren fel_: X kan väljas som två dagar.
- _Vindkraft turbinen fel_: X kan väljas som två månader.

### <a name="regression-for-predictive-maintenance"></a>Regression för förutsägande Underhåll
Regressionsmodeller är vana vid _compute återstående livslängd (RUL) för en tillgång_. RUL definieras som mängden tid som en tillgång är i drift innan nästa fel inträffar. Varje exempel utbildning är en post som hör till en tidsenhet _nY_ för en tillgång, där _n_ är multipeln. Modellen kan beräkna RUL för varje ny exempel som en _kontinuerligt nummer_. Det här talet anger tidsperioden som återstår före felet.

#### <a name="label-construction-for-regression"></a>Etikett-konstruktion för regression
Den här frågan är: ”Vad är den återstående användbara livslängd (RUL)”? Beräkna etikett för att antalet tidsenheter kvar innan nästa fel för varje post innan felet. I den här metoden är etiketter kontinuerlig variabler. (Se bild 4)

![Bild 4. Etiketter för regression](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) bild 4. Etiketter för regression

För regression görs märkning med referens till en felpunkt. Beräkningen är inte möjligt utan att känna till hur lång tid tillgången texten, har överlevt innan ett fel uppstod. Så kan i kontrast till binär klassificering tillgångar utan några fel i data inte användas för modellering. Det här problemet är det bäst med en annan statistisk teknik som kallas [analys av fortsatt giltighet](https://en.wikipedia.org/wiki/Survival_analysis). Men potentiella problem kan uppstå när du använder den här tekniken till kontaktar problem som rör tidsvarierande data med regelbundet. Läs mer på analys av fortsatt giltighet [det här en personsökare](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Flera klassificering för förebyggande underhåll
Flera klassificeringstekniker kan användas i kontaktar lösningar för två scenarier:
- Förutsäga _två framtida resultat_: första resultatet är _ett tidsintervall för fel_ för en tillgång. Tillgången är tilldelad till en av flera möjliga tidsperioder. Andra resultatet är risken för fel i en framtida period på grund av _gör att en av flera roten_. Den här förutsägelse kan Underhåll besättningen att bevaka symptom och plan underhållsscheman.
- Förutsäga _mest sannolikt grundorsaken_ för ett visst fel. Det här resultatet rekommenderar rätt uppsättning underhållsåtgärder för att åtgärda ett fel. En rankningslista över underliggande orsaker och rekommenderade reparationer kan tekniker prioritera sina repareringsåtgärder efter ett fel.

#### <a name="label-construction-for-multi-class-classification"></a>Etikett-konstruktion för flera klassificering
Den här frågan är ”: Vad är sannolikheten att en tillgång misslyckas i nästa _nZ_ tidsenheter där _n_ är antalet perioder”? Besvara den här frågan, etikett nZ poster innan felet för en tillgång med buckets tid (3Z 2Z Z). Etikett för alla andra registrerar ”normal” (etikett = 0). I den här metoden en målvariabel innehåller _kategoriska_ värden. (Se bild 5).

![Bild 5. Etiketter för multiklass-baserad klassificering för fel tid förutsägelse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) bild 5. Etiketter för flera klassificering för fel tid förutsägelse

Den här frågan är ”: Vad är sannolikheten att tillgången inte fungerar i nästa X tidsenheter på grund av orsaken/rotproblemet _P<sub>jag</sub>_”? där _jag_ är antalet möjliga underliggande orsaker. Besvara den här frågan, etikett X poster innan felet av en tillgång som ”upphör snart att misslyckas på grund av orsaken _P<sub>jag</sub>_” (etikett = _P<sub>jag</sub>_). Etikettera alla poster som är ”normal” (etikett = 0). I den här metoden är etiketter dessutom kategoriska (se bild 6).

![Bild 6. Etiketter för multiklass-baserad klassificering för rot orsak förutsägelse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) bild 6. Etiketter för flera klassificering för rot orsak förutsägelse

Modellen tilldelar en sannolikheten för fel på grund av var och en _P<sub>jag</sub>_  samt sannolikheten för fel. Dessa sannolikheter kan beställas storlek så att förutsägelse av de problem som är mest sannolikt kan förekomma i framtiden.

Den här frågan är: ”vilka underhållsåtgärder rekommenderar ni efter ett fel”? Besvara den här frågan märkning _kräver inte en framtida horizon som ska väljas_eftersom modellen inte förutse fel i framtiden. Det är bara att förutsäga mest sannolikt rotorsaken _när felet har redan skett_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Utbildning, validering och analysmetoder för förebyggande underhåll
Den [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) ger en fullständig täckning för modellen train-test-Validera cykel. Det här avsnittet diskuteras säkerhetsaspekter som är unika för kontaktar.

### <a name="cross-validation"></a>Korsvalidering
Målet med [korsvalidering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) är att definiera en uppsättning data till ”test” modellen i fasen utbildning. Den här datamängden kallas den _verifiering set_. Den här metoden hjälper till att gränsen problem angående _overfitting_ och ger en inblick i hur modellen kommer generalisera till en fristående uppsättning data. Det vill säga en okänd för datauppsättning, vilket kan orsaka från verkliga problem. Rutinen utbildning och testning för kontaktar måste ta hänsyn till de olika delarna tid att generalisera bättre på överblivna framtida data.

Många machine learning-algoritmer är beroende av ett antal hyperparametrar som kan ändra modellprestanda avsevärt. Optimala värdena för dessa hyperparametrar beräknas inte automatiskt när träna modellen. De bör anges med data Science. Det finns flera sätt att hitta bra värden av hyperparametrar.

De vanligaste en är _k-vikning korsvalidering_ som delar upp exempel slumpmässigt i _k_ vikningar. För varje uppsättning av hyperparametrar värden kör Inlärningsalgoritmen _k_ gånger. Vid varje iteration använder du exemplen i den aktuella vikningen som en uppsättning verifiering och resten av exemplen som en uppsättning för utbildning. Träna algoritmen över utbildning exempel och beräkna verifiering exempel prestandamåtten. I slutet av den här loopen beräkna medelvärdet för _k_ prestandamått. För varje uppsättning finjustering värden, väljer du de som har bästa genomsnittliga prestanda. Uppgiften för att välja hyperparametrar är ofta experimentella natur.

Kontaktar problem registreras data som en tidsserie med händelser som kommer från flera datakällor. Dessa poster kan sorteras enligt tidpunkten för märkning. Därför om datauppsättningen delas _slumpmässigt_ till uppsättningar för träning och verifiering, _vissa av exemplen utbildning kan vara senare än några av verifiering exempel_. Framtida prestanda för finjustering värden ska beräknas baserat på vissa data som anlänt _innan_ modellen har tränats. Dessa beräkningar kan vara alltför optimistisk, särskilt om de time serien-inte står stilla och utvecklas över tid. De valda finjustering värdena kan därför vara icke-optimal.

Det rekommenderade sättet är att dela upp exemplen i utbildnings- och verifiering som angetts i en _tidsberoende_ sätt, där alla verifiering kan till exempel vara senare än alla utbildning exemplen. För varje uppsättning finjustering värden, träna algoritmen utifrån datauppsättningar för utbildning. Mäta modellens prestanda över samma verifiering. Välj finjustering värden som visar bästa möjliga prestanda. Finjustering värdena som väljs av träna/verifiering dela resulterar i bättre framtida modellens prestanda än med de värden som väljs slumpmässigt av korsvalidering.

Den slutliga modellen kan skapas genom att träna en inlärningsalgoritm över hela träningsdata med de bästa finjustering-värdena.

### <a name="testing-for-model-performance"></a>Testa för modellprestanda
När du har skapat en modell, krävs en uppskattning av framtida prestanda på nya data. En bra beräkna prestandamått för finjustering värden som beräknats över verifieringen eller ett genomsnittligt prestanda-mått beräknas från korsvalidering. Dessa beräkningar är ofta alltför optimistisk. Företaget kanske ofta vissa ytterligare riktlinjer för hur de vill testa modellen.

Det rekommenderade sättet för kontaktar är att dela exemplen i utbildning, verifiering, och testdata en _tidsberoende_ sätt. Alla test exemplen ska vara senare än alla utbildnings- och verifiering exemplen. Generera modellen efter delning, och mäter dess prestanda enligt beskrivningen ovan.

När time series-är stillastående och enkla att förutsäga, generera slumpmässiga och tidsberoende metoder liknande uppskattningar av framtida prestanda. Men när time series-är icke-stillastående och/eller svårt att förutse, tidsberoende metoden genererar mer realistiska uppskattningar av framtida prestanda.

### <a name="time-dependent-split"></a>Tidsberoende dela
Det här avsnittet beskriver Metodtips för att implementera tidsberoende dela. En tidsberoende dubbelriktad delning mellan utbildnings- och testuppsättningar beskrivs nedan.

Anta att en dataström med tidsstämplad händelser, t.ex mått från olika sensorer. Definiera funktioner och etiketter för träning och testning exempel över tidsramar som innehåller flera händelser. Till exempel för binär klassificering, skapa funktioner som bygger på historiska händelser och skapa etiketter som baseras på framtida händelser i ”X” tidsenheter i framtiden (se avsnitt på [funktionstekniker](#Feature-engineering) och [modellering tekniker](#Modeling-techniques-applied-to-PdM-use-cases)). Därför kommer etikettering tidsram på ett exempel senare än tid som dess funktioner.

Tidsberoende delning, Välj en _utbildning klara tiden T<sub>c</sub>_  då att träna en modell med hyperparametrar justerade med hjälp av historiska data upp till T<sub>c</sub>. Att förhindra läckage av framtida etiketter som ligger utanför T<sub>c</sub> till utbildningsdata, väljer du senaste tiden till etiketten utbildning exempel ska X enheter innan T<sub>c</sub>. I exemplet som visas på bild 7, representerar varje ruta en post i datauppsättningen där funktioner och etiketter beräknas enligt beskrivningen ovan. Bilden visar de poster som ska gå i utbildning och testningsuppsättningar för X = 2 och W = 3:

![Bild 7. Tidsberoende dela upp för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) bild 7. Tidsberoende dela upp för binär klassificering

Grön rutor representerar poster som hör till enheterna som kan användas för utbildning. Varje exempel utbildning genereras av överväger tidigare perioderna för funktionen generation och två framtida perioder för etikettering innan T<sub>c</sub>. När någon del av de två framtida perioderna ligger utanför T<sub>c</sub>, undantas det exemplet från datauppsättningen utbildning eftersom ingen synlighet antas utöver T<sub>c</sub>.

De svarta rutorna representerar poster i den slutliga taggade datauppsättning som inte ska användas i datauppsättningen utbildning fått ovanstående villkor. Dessa poster också används inte vid testning av data, eftersom de är innan T<sub>c</sub>. Dessutom kan beror deras etikettering tidsramar delvis på tidsramen utbildning som inte är idealiskt. Träning och testning data bör ha separata etikettering tidsramar för att förhindra att information läcker ut för etiketten.

Den metod som beskrivs hittills kan för ett överlapp mellan träning och testning exempel som har tidsstämplar nära T<sub>c</sub>. En lösning för att uppnå större uppdelning är att undanta exempel som är inom W tidsenheter av T<sub>c</sub> från testet. Men en aggressiv dela beror på gott om datatillgänglighet.

Regressionsmodeller som används för att förutsäga återstående användbara Livslängden störs mer av läckageproblem. Med hjälp av metoden slumpmässiga dela leder till extrema över passning. Regression problem delningen ska vara så att de poster som hör till tillgångar med felaktiga inloggningar innan T<sub>c</sub> går du till träningsmängden. Poster över tillgångar med fel efter brytfrekvens går du till uppsättningar för testning.

En annan bästa praxis för att dela data för träning och testning är att använda en delning av tillgången-ID. Delningen ska vara så att ingen av de resurser som används i träningsmängden används i testa modellprestanda. En modell har med den här metoden större möjligheter till att tillhandahålla mer realistisk resultat med nya tillgångar.

### <a name="handling-imbalanced-data"></a>Hantering av imbalanced data
Klassificering problem om det finns fler exempel på en klass än de andra datauppsättningen anses _imbalanced_. Vi rekommenderar är tillräckligt med representanter för varje klass i träningsdata rekommenderat att aktivera skillnaderna mellan olika klasser. Om en klass är mindre än 10% av data, anses data som imbalanced. Underrepresented klassen kallas ett _minority klass_.

Många PdM problem inför sådana imbalanced datauppsättningar, där en klass kraftigt underrepresented jämfört med de andra klass eller klasser. I vissa situationer kan utgöra klassen minority endast 0,001% av totalt antal datapunkter. Klass obalans är inte unikt för kontaktar. Andra domäner där fel och avvikelser är ovanligt förekomster inför ett liknande problem, exempel, identifiering av bedrägerier och intrång. De här felen utgör minority klass exemplen.

Med klassen obalans i data, är prestanda för de flesta standard learning-algoritmer skadad, eftersom de försöka minska den övergripande Felfrekvensen. För en datamängd med 99% negativa och positiva exempel av 1%, kan en modell visas ha 99% noggrannhet genom märkning alla instanser som negativt. Men modellen kommer argumentantal klassificera alla positivt exemplen; Så även om dess Precision är hög algoritmen inte är en användbar. Därför konventionella utvärderingsmått som _övergripande noggrannhet på Felfrekvens_ inte finns tillräckliga imbalanced learning. När möter med imbalanced datauppsättningar, som andra mått används för utvärdering av modellen:
- Precision
- Återkalla
- F1-poäng
- Kostnad justeras ROC (mottagare operativa egenskaper)

Läs mer om de här måtten [modellera utvärdering](#Model-evaluation).

Det finns dock några metoder som hjälper att åtgärda klass obalans problem. De två viktigaste som är _sampling tekniker_ och _kostnad känsliga learning_.

#### <a name="sampling-methods"></a>Sampling metoder
Imbalanced learning innebär användning av sampling metoder för att ändra träningsmängden data till en belastningsutjämnad uppsättning. Sampling metoder är inte tillämpas på test-uppsättningen. Även om det finns flera tekniker för sampling, de flesta okomplicerat som är _slumpmässiga oversampling_ och _under sampling_.

_Slumpmässig oversampling_ processen att välja ett slumpmässigt urval från minority klass, replikeras de här exemplen och lägga till dem i datauppsättningen för utbildning. Följaktligen hur många exemplen i minority klass ökas och så småningom balansera antalet exempel på olika klasser. En nackdel med oversampling är att flera instanser av vissa exempel som kan orsaka klassificerare att bli för specifika, vilket leder till stora passning. Modellen kan visa hög utbildning Precision, men dess prestanda på överblivna testdata kan vara icke-optimal.

Däremot _slumpmässigt under sampling_ är att välja ett slumpmässigt urval från en majoritet klass och ta bort dessa exempel från datauppsättningen för utbildning. Ta bort exempel från majoritet klass kan emellertid orsaka leder klassificerare att missa viktiga begrepp som hör till klassen majoritet. _Hybrid sampling_ där minority klassen är över provade och majoritet klass är under-samplas med samma tid är en annan lönsamma metod.

Det finns många avancerade provtagningar. Tekniken valt är beroende av egenskaper för data och resultatet av iterativa experiment med data Science.

#### <a name="cost-sensitive-learning"></a>Kostnad känsliga learning
I PdM är fel som utgör klassen minority mer intressanta än normala exempel. Så är fokuserar främst på den algoritmen prestanda på fel. Felaktigt att förutsäga en positiv klass som en negativ klass kan kosta mer än vice versa. Den här situationen kallas ofta olika förlust eller asymmetrisk kostnaden för argumentantal klassificera element till olika klasser. Perfekt klassificeraren ska leverera hög förutsägelsefunktionen över klassen minority utan att kompromissa med precisionen för klassen majoritet.

Det finns flera sätt att få balans. Tilldela en hög kostnad till felkonfigurerat klassificering i klassen minority för att lösa problemet med ojämn förlust, och försök att minska den sammanlagda kostnaden. Algoritmer som _SVMs (Support Vector datorer)_ antar den här metoden sin natur, genom att tillåta att kostnaden för positiva och negativa exempel anges under utbildningen. På samma sätt kan öka metoder som _förstärkta beslutsträd_ vanligtvis visa bra prestanda med imbalanced data.

## <a name="model-evaluation"></a>Modell-utvärdering
Felkonfigurerat klassificeringen är ett betydande problem för kontaktar scenarier där kostnaden för falsklarm verksamheten är hög. Exempelvis kan en beslutet att stående baserat på en felaktig förutsägelser av motorfel störa scheman och reseplaner. Tar en dator offline från en sammanställningsrad kan leda till förlust av intäkter. Utvärdering av modellen med rätt prestandavärden mot nya testdata är så viktigt.

Vanliga prestandamått som används för att utvärdera kontaktar modeller beskrivs nedan:

- [Precision](https://en.wikipedia.org/wiki/Accuracy_and_precision) är det mest populära mått som används för att beskriva en klassificerare prestanda. Men Precision är känsligt för data-distributioner och är en ineffektiv mått för scenarier med imbalanced datauppsättningar. Andra mått används i stället. Verktyg som [felmatris](https://en.wikipedia.org/wiki/Confusion_matrix) används för att beräkna och skäl om korrektheten i modellen.
- [Precision](https://en.wikipedia.org/wiki/Precision_and_recall) av kontaktar modeller som är relaterade till antalet falsklarm. Lägre precision av modellen motsvarar vanligtvis en högre andel falsklarm.
- [Kom ihåg](https://en.wikipedia.org/wiki/Precision_and_recall) frekvensen anger hur många fel i test-uppsättningen identifierades korrekt av modellen. Priserna för högre återkallande innebär modellen har lyckats identifiera SANT felen.
- [F1 poäng](https://en.wikipedia.org/wiki/F1_score) är övertonsutsläpp medelvärdet av precision och kanske kommer ihåg, med dess värde mellan 0 (sämsta) till 1 (bäst).

För binär klassificering
- [Mottagare som fungerar kurvor (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) är också ett populärt mått. I ROC kurvor tolkas modellprestanda baserat på en operativ fixtal på ROC.
- Men för kontaktar problem _decile tabeller_ och _lyfta diagram_ är mer informativ. De koncentrera sig på klassen positivt (fel) och ger en mer komplex bild algoritm-prestanda än ROC kurvor.
  - _Decile tabeller_ skapas med hjälp av test-exempel i en fallande ordning av sannolikheten för fel. Sorterad exemplen grupperas sedan i deciles (10% av exemplen med sannolikhet, och sedan 20%, 30% och så vidare). Förhållandet mellan (SANT positiva identifieringar) /(random baseline) för varje decile hjälper till att beräkna algoritmen prestanda vid varje decile. Slumpmässig baslinjen tar på värden 0.1, 0.2 och så vidare.
  - [Flytta diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) Rita decile SANT positiva identifieringar jämfört med slumpmässiga SANT positiva identifieringar för alla deciles. De första deciles är vanligtvis fokus resultat, eftersom de visar de största vinsterna. Första deciles kan också ses som är representativ för ”oskyddad”, när den används för kontaktar.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modellen driftsättning för förutsägande Underhåll

Fördelen är data science Övning realiserade endast när den tränade modellen är operationella. Det vill säga måste modellen distribueras till affärssystem för att göra förutsägelser baserade på nytt, tidigare överblivna data.  Den nya informationen måste exakt överensstämmer med den _modellen signatur_ av den tränade modellen på två sätt:
- alla funktioner måste finnas i varje logisk instans (exempelvis en rad i en tabell) av nya data.
- den nya informationen måste bearbetas i förväg och var och en av funktionerna utformat, exakt på samma sätt som träningsdata.

Ovanstående procedur anges på många sätt i akademiska och industri-dokumentationen. Men alla följande instruktioner betyda samma sak:
- _Rangordna nya data_ med hjälp av modellen
- _Tillämpar modellen_ för nya data
- _Operationalisera_ modellen
- _Distribuera_ modellen
- _Kör modellen_ mot nya data

Som tidigare nämnts skiljer modellen driftsättning för kontaktar sig från dess peer-datorer. Implementera scenarier som rör identifiering av avvikelser och felidentifiering vanligtvis _online bedömning_ (kallas även _realtid bedömning_). Här, modellen _poäng_ posterna inkommande, och returnerar en förutsägelse. För identifiering av avvikelser, förutsägelsen är en indikation om att ett fel uppstod (exempel: en klass SVM). Det vore för felidentifiering, typ eller klass av fel.

Däremot kontaktar omfattar _batchbedömning_. För att uppfylla modellen signaturen, vara funktionerna i den nya informationen utformat på samma sätt som träningsdata. För den stora datamängder som är typiskt för nya data kan aggregeras över tidsfönster funktioner och poängsättas i batch. Batchbedömning görs normalt i distribuerade system som [Spark](http://spark.apache.org/) eller [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Det finns ett par alternativ - både icke-optimal:
- Strömmande datamotorer hantera aggregering i windows i minnet. Så det kan vara gällande att de stöder online bedömning. Men dessa system är lämpliga för kompakta data i smal windows tid eller null-optimerade element över bredare windows. De kan inte skalas bra för kompakta data över bredare tidsfönster som visas i kontaktar scenarier.
- Om batchbedömning inte är tillgänglig är lösningen att anpassa online bedömning för att hantera nya data i små batchar i taget.

## <a name="solution-templates-for-predictive-maintenance"></a>Mallar för lösningar för förutsägande Underhåll

Den sista delen av den här guiden innehåller en lista över kontaktar lösningsmallar, självstudier och experiment som implementeras i Azure. Dessa PdM-program kan distribueras till en Azure-prenumeration inom några minuter i vissa fall. De kan användas som proof of concept demonstrationer, sandbox-miljöer att experimentera med alternativ eller acceleratorer för implementeringar av faktiska produktionen. Dessa mallar finns i den [Azure AI-galleriet](http://gallery.azure.ai) eller [Azure GitHub](https://github.com/Azure). Dessa olika exempel kommer att finnas i den här lösningsmallen över tid.

| # | Titel | Beskrivning |
|--:|:------|-------------|
| 1 | [Azures förutsägande Underhåll Machine Learning-exempel](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) |Kontaktar exempel att förutsäga fel under nästa N enheterna. Det här exemplet skrivs som ett projekt med Azure ML Workbench och är perfekt för nybörjare till kontaktar. [Ytterligare dokumentation](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance) relaterade till det här exemplet.|
| 2 | [Lösningsmallen för azures förutsägande Underhåll](https://github.com/Azure/AI-PredictiveMaintenance) | En mall med öppen källkod som visar ML-modeller och en fullständig Azure-infrastrukturen kan stödja scenarier för förebyggande underhåll i samband med IoT fjärrövervakning. |
| 3 | [Djupinlärning för förutsägande Underhåll](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure-anteckningsbok med en demo-lösning för att använda LSTM (lång kortvariga minne) nätverk (en klass på återkommande Neurala nätverk) för förutsägande underhåll, med en [blogginlägget på det här exemplet](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Guide för Hotmodellering i förebyggande underhåll i R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Guide för hotmodellering i PdM med skript i R.|
| 5 | [Azure Förutsägelseunderhåll för flygindustrin](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | En av de första PdM-lösningsmallar baserat på Azure ML v1.0 för flygplan underhåll. Den här guiden kommer från det här projektet. |
| 6 | [Azure AI-verktyg för IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI i IoT edge med TensorFlow; Toolkit paket deep learning-modeller i Azure IoT Edge-kompatibel Docker-behållare och exponera dessa modeller som REST API: er.
| 7 | [Förebyggande underhåll i Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite datorer - förkonfigurerade lösningen. Flygplan kontaktar mallen Underhåll med IoT Suite. [Ett annat dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) och [genomgången](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) relaterade till samma projekt. |
| 8 | [Mallen för förebyggande underhåll med hjälp av SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstration av återstående livslängd scenariot utifrån R services. |
| 9 | [Guide för Hotmodellering i förebyggande underhåll](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Flygplan Underhåll datauppsättning funktionen utformat med R med [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) och [datauppsättningar](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) och [Azure notebook](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) och [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)i AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Utbildningsresurser för förutsägande Underhåll

Microsoft Azure erbjuder utbildningsvägar för grundläggande koncepten bakom PdM-teknik, förutom innehåll och utbildning på allmänt begrepp för AI och praxis.

| Utbildning resurs  | Tillgänglighet |
|:-------------------|--------------|
| [Utbildningsväg för PdM med träd och slumpmässiga skog](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Offentligt | 
| [Utbildningsväg för PdM med Djupinlärning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Offentligt |
| [AI Developer för Azure](http://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Offentligt |
| [Microsofts AI-skola](http://aischool.microsoft.com/learning-paths) | Offentligt |
| [Azure AI-utbildning från GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Offentligt |
| [LinkedIn Learning](http://www.linkedin.com/learning) | Offentligt |
| [Microsoft AI Youtube Webbseminarier](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Offentligt |
| [Microsoft AI Show](http://channel9.msdn.com/Shows/AI-Show) | Offentligt |
| [LearnAI@MS](http://learnanalytics.microsoft.com) | Partner |
| [Microsoft Partner Network](http://learningportal.microsoft.com) | Partner |

Dessutom är kostnadsfria MOOC (stora öppna onlinekurser) på AI online erbjuds av akademiska institutioner som Stanford och MIT och andra educational företag.
