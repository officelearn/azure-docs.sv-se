---
title: Azure AI-guide för förutsägande Underhåll lösningar | Microsoft Docs
description: En fullständig beskrivning av datavetenskap som används av förutsägande Underhåll lösningar i flera branscher.
services: machine-learning
author: fboylu
manager: cgronlun
editor: ''
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: fboylu
ms.openlocfilehash: ff2e1660ffcc1f397697b27084e000371c7c84f3
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938017"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-guide för förutsägande Underhåll lösningar

## <a name="summary"></a>Sammanfattning

Förutsägande Underhåll (**PdM**) är ett populärt program för förutsägelseanalys som hjälper företag i flera branscher uppnå hög tillgången utnyttjande och besparingar i driftskostnaderna. Den här guiden samlar företag och analytiska riktlinjer och bästa praxis för att utveckla och distribuera PdM lösningar med hjälp av den [AI för Microsoft Azure-plattformen](https://azure.microsoft.com/overview/ai-platform) teknik.

Den här guiden beskriver till att börja branschspecifika affärsscenarier och processen för att kvalificera dessa scenarier för PdM. Det finns också datakrav och tekniker för förutsägelsemodellering för att skapa PdM lösningar. Innehållet i handboken finns på den datavetenskap process, inklusive steg för förberedelse av data, funktionen tekniker, modellen skapas och modellen operationalization. Den här guiden innehåller en uppsättning lösningsmallar för att påskynda PdM programutveckling för att komplettera dessa viktiga begrepp. Guiden pekar också användbart utbildningsresurser för läkare lära dig mer om AI bakom datavetenskap. 

### <a name="data-science-guide-overview-and-target-audience"></a>Datavetenskap guiden översikt och mål målgrupp
Den första delen av den här guiden beskriver vanliga affärsproblem fördelarna med att implementera PdM för att åtgärda dessa problem och visar några vanliga användningsområden. Företagets beslutsfattare (BDMs) drar nytta av det här innehållet. Den andra delen beskriver datavetenskap bakom PdM och innehåller en lista över PdM lösningar som skapats med hjälp av principer som beskrivs i den här guiden. Det ger också utbildningsvägar och pekare till utbildningsmaterial. Tekniska beslutsfattare (TDMs) kommer användbart det här innehållet.

| Börja med... | Om du är... |
|:---------------|:---------------|
| [Företag fallet för förebyggande underhåll](#Business-case-for-predictive-maintenance) |en beslutsfattare inom företaget (BDM) att minimera driftavbrott och driftskostnaderna och förbättra användning av utrustning |
| [Datavetenskap för förebyggande underhåll](#Data-Science-for-predictive-maintenance) |en teknisk beslutsfattare (TDM) utvärderar PdM tekniker att förstå de unika databehandling och AI krav för förebyggande underhåll |
| [Lösningsmallar för förebyggande underhåll](#Solution-templates-for-predictive-maintenance)|en programvara systemarkitekt eller AI-utvecklare som vill snabbt klara av en demonstration eller en--konceptbevis |
| [Utbildningsresurser för förebyggande underhåll](#Training-resources-for-predictive-maintenance) | allt eller något av ovanstående och vill veta grundläggande koncepten bakom datavetenskap, verktyg och tekniker.

### <a name="prerequisite-knowledge"></a>Nödvändig information
Innehållet BDM förväntar sig inte läsaren har kännedom vetenskap tidigare data. Grundläggande kunskaper om statistik och datavetenskap är användbart för TDM-innehåll. Kunskap om Azure Data och AI-tjänster, Python, R, XML och JSON rekommenderas. AI tekniker implementeras i Python och R-paket. Lösningsmallar implementeras med hjälp av Azure-tjänster, utvecklingsverktyg och SDK: er.

## <a name="business-case-for-predictive-maintenance"></a>Företag fallet för förebyggande underhåll

Företag kräver kritiska utrustning köras vid högsta effektivitet och användning Tänk sina avkastning på kapitalinvesteringar. Dessa tillgångar kan röra sig om flygplan motorer, turbiner, hissar eller industriella chillers - som kostar miljoner - nedåt vanliga installationer som kopiatorer, kaffe datorer eller Vattenkylare.
- Som standard är de flesta företag förlitar sig på _korrigerande Underhåll_, där delar ersätts som och när de inte. Åtgärda Underhåll garanterar delar används helt (därför inte avmagrade komponenten livslängd), men kostnader verksamheten i driftstopp och och oplanerat Underhåll krav (av timmar eller olämplig platser).
- Vid nästa nivå, företag Övning _förebyggande underhåll_, där de bestämma användbar livslängd för en del och underhåll eller ersätta innan ett fel uppstod. Förebyggande underhåll undviker oplanerade och oåterkalleligt fel. Men hög kostnaderna för schemalagd stilleståndstid under användning av komponenten innan dess fullständiga livslängd för användning och arbete fortfarande kvar.
- Målet med _förutsägande Underhåll_ är att optimera balansen mellan korrigerande och förebyggande underhåll genom att aktivera _precis i tid_ ersättning av komponenter. Den här metoden ersätter endast komponenterna när de är nära ett fel. Genom att utöka komponenten lifespans (jämfört med förebyggande underhåll) och minskar oplanerat underhåll och arbetskostnader (via korrigerande underhåll), företag kan få kostnadsbesparingar och konkurrenskraftiga fördelar.

## <a name="business-problems-in-pdm"></a>Affärsproblem i PdM
Företag står inför hög operativa risker på grund av oväntade fel och har begränsad insyn i den grundläggande orsaken till problem i komplexa system. Några viktiga frågor är:

- Identifiera avvikelser i utrustning eller systemets prestanda eller funktioner.
- Förutsäga om en tillgång kan misslyckas inom en snar framtid.
- Beräkna återstående livslängd för en tillgång.
- Identifiera huvudsakliga orsakerna till felet av en tillgång.
- Identifiera vilka underhållsåtgärder som måste göras på en tillgång och på.

Vanliga mål-satser från PdM är:

- Minska operativa risken för verksamhetskritiska kritiska utrustning.
- Öka avkastningen på tillgångar genom att förutsäga fel innan de inträffar.
- Kontrollera kostnaden för underhåll genom att aktivera just-in-time-underhållsåtgärder.
- Lägre kunden partikelhållfasthet kan förbättra varumärken avbildningen och förlorade försäljning.
- Lägre kostnader för inventering genom att minska inventering nivåer genom att förutsäga beställningspunkten.
- Identifiera mönster som är ansluten till olika underhållsproblem med.
- Ange KPI: er (nyckeltal), till exempel hälsotillstånd resultat för tillgångsinformation villkor.
- Beräkna återstående livslängd av tillgångar.
- Rekommendera rimlig underhållsaktiviteter.
- Aktivera bara i gång lagret genom att uppskatta datum för ersättning av delar.

Dessa mål-satser är startpunkter för:

- _datavetare_ att analysera och lösa specifika förutsägande problem.
- _molnet arkitekter och utvecklare_ sätta ihop en heltäckande lösning.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalificerande problem för förebyggande underhåll
Det är viktigt att betona att inte alla användningsfall eller affärsproblem effektivt kan lösas genom PdM. Det finns tre viktiga kvalificerande kriterier som måste beaktas vid val av problemet:

- Problemet måste vara förutsägande i natur. att ska det vara ett mål eller resultat att förutsäga. Problemet bör ha en tydlig sökväg till åtgärder för att förhindra fel när de identifieras.
- Problemet bör ha en post av utrustning som innehåller operativa tidigare _både bra och felaktiga resultat_. En uppsättning åtgärder som vidtas för att minimera felaktiga resultat ska också vara tillgänglig som en del av dessa poster. Felrapporter, loggar för underhåll av prestandaförsämring, reparera och Ersätt loggar är också viktigt. Dessutom är reparationer som görs för att förbättra dem och ersättning poster också användbara.
- Inspelad historiken visas i _relevanta_ informationen av _tillräckligt_ tillräckligt med kvalitet att stödja användningsfallet. Mer information om data relevans och kontrollsystemet finns [kraven för förebyggande underhåll](#Data-requirements-for-predictive-maintenance).
- Slutligen bör verksamheten ha domän experter som har en förståelse för problemet. De bör vara medveten om interna processer och metoder för att kunna hjälpa analytiker förstå och tolka data. De bör också kunna göra nödvändiga ändringar i befintliga processer för att samla in rätt data för problem, om det behövs.

## <a name="sample-pdm-use-cases"></a>Exempel på användningsområden för PdM
Det här avsnittet fokuserar på en mängd PdM användningsområden från flera branscher som Aerospace, verktyg och transport. Varje avsnitt börjar med affärsproblem och beskriver fördelarna med PdM relevanta data runt problem i verksamheten och slutligen fördelarna med en PdM lösning.

| Affärsproblem | Fördelarna med en PdM |
|:-----------------|-------------------|
|**Luftfart**      |                   |
|_Flight fördröjning och avbryta_ på grund av mekanisk problem. Fel som inte kan repareras i tid kan orsaka flygplan avbrytas och störa schemaläggning och åtgärder. |PdM lösningar kan förutsäga sannolikheten att ett flygplan som fördröjd eller avbröts på grund av mekaniskt fel.|
|_Flygplan motorn delar fel_: flygplan motorn del ersättningar är bland de vanligaste underhållsaktiviteter inom flygbolag bransch. Underhåll lösningar kräver noggrann hantering av lager komponenttillgänglighet leverans och planering|Att kunna samla in underrättelser om komponenten tillförlitlighet leder till betydande minskning på investeringar.|
|**Finans** |                         |
|_ATM-fel_ är ett vanligt problem inom bank bransch. Det här problemet är att rapportera sannolikheten att en ATM kontanter återkallelse transaktion hämtar avbryts på grund av ett papper har fastnat eller en del i kontanter utgivaren. Baserat på förutsägelser transaktion fel, kan bl.a betjänas proaktivt för att förhindra fel.| I stället för att tillåta datorn misslyckas halvvägs via en transaktion är önskvärt alternativ att programmet datorn för att neka tjänsten baserat på förutsägelser.|
|**Energi** |                          |
|_Vindkraft turbinen fel_: vind turbiner största energi-källan i ansvar för miljön länder och omfatta hög utgifter. En viktig del i vind turbiner är generator-motor. fel återger turbinen ineffektiv. Det är också mycket billigare att åtgärda.|Förutsäga KPI: er, till exempel MTTF (tiden fel) hjälper elbolag förhindra turbinen fel och kontrollera minimal avbrottstid. Fel sannolikhet informera tekniker för att övervaka turbiner som sannolikt kommer att misslyckas snart och schemalägga tidsbaserade Underhåll systemen. Förutsägelsemodeller ger insikter om olika faktorer som bidrar till fel, vilket hjälper tekniker bättre förstå rotorsaken till problem.|
|_Strömbrytare fel_: Distribution av el till hem och företag kräver power rader ska fungera vid alla tidpunkter för att garantera energi leverans. Utlösts hjälpa att begränsa eller undvika skada power rader under överbelastning eller negativa väder villkor. Problem i verksamheten här är att förutsäga strömbrytare fel.| PdM lösningar att minska kostnaderna för reparation och öka livslängd utrustning som utlösts. De hjälper att förbättra kvaliteten på power nätverket genom att minska oväntade fel och avbrott i tjänsten.|
|**Transport och logistik** |    |
|_Snabba dörren fel_: stora snabba företag tillhandahåller en fullständig stack-tjänst för miljontals funktionella hissar runtom i världen. Snabba säkerhet, tillförlitlighet och drifttid är de viktigaste aspekterna för sina kunder. Dessa företag spåra dessa och andra attribut via sensorer som hjälper dem med korrigerande och förebyggande underhåll. I en snabba tydligaste kunden problemet fungerar inte snabba dörrar. Problem i verksamheten är i det här fallet att tillhandahålla ett kunskapsbas förutsägande program som beräknar risken orsakar dörren fel.| Hissar är kapitalinvesteringar för potentiellt 20 – 30 år livslängd. Så att varje potentiell försäljning kan vara konkurrenskraftiga; förväntningar för service- och är därför höga. Förutsägande Underhåll kan ge dessa företag flera fördelar jämfört med sina konkurrenter i sin produkt och Tjänsterbjudanden.|
|_Rulla fel_: rulla fel konto för hälften av alla träna urspårning och kostnaden miljarder globala tåg branschen. Hjul fel också göra att spår försämras, ibland orsakar spåret att bryta för tidigt. Tåg radbrytningar leda till katastrofer, till exempel urspårning. Om du vill undvika sådana instanser järnvägar övervaka prestanda för hjul och ersätta dem på ett förebyggande sätt. Här affärsproblem är förutsägelser hjul fel.| Förutsägande underhåll av hjul hjälper med just-in-time-ersättning av hjul |
|_Språng train dörren fel_: en större orsak till fördröjningar i språng åtgärder är dörren fel train bilar. Problem i verksamheten här är att förutsäga train dörren fel.|Tidig avslöjar ett dörren fel eller antalet dagar tills ett dörren fel hjälper företag-optimera träna dörren servicing scheman.|

I nästa avsnitt hämtar detaljer om hur du inser PdM fördelarna som beskrivs ovan.

## <a name="data-science-for-predictive-maintenance"></a>Datavetenskap för förebyggande underhåll

Det här avsnittet innehåller allmänna riktlinjer av datavetenskap principer och praxis för PdM. Den är avsedd att hjälpa en TDM, Lösningsarkitekt eller en utvecklare förstå kraven och processen för att bygga program för slutpunkt till slutpunkt AI för PdM. Du kan läsa det här avsnittet tillsammans med en granskning av dessa demonstrationer och proof of concept mallar som anges i [Lösningsmallar för förebyggande underhåll](#Solution-templates-for-predictive-maintenance). Du kan sedan använda dessa principer och bästa praxis för att implementera lösningen för en PdM i Azure.

> [!NOTE]
> Den här guiden är inte avsedd att utbilda läsaren datavetenskap. Flera användbara källor tillhandahålls Läs mer i avsnittet för [utbildningsresurser för förebyggande underhåll](#Training-resources-for-predictive-maintenance). Den [lösningsmallar](#Solution-templates-for-predictive-maintenance) som anges i guiden demonstrerar några av de här AI-teknikerna för specifika PdM problem.

## <a name="data-requirements-for-predictive-maintenance"></a>Kraven för förebyggande underhåll

Alla learning framgång beror på a kvaliteten på vad är att vilka undervisning förekommer och (b) möjligheten för deltagaren. Förutsägelsemodeller Lär dig mönster från historiska data och förutsäga framtida resultat med vissa sannolikhet baserat på dessa observerade mönster. En modell förutsägande noggrannhet beror på relevans, kontrollsystemet och kvaliteten på träning och testning data. Nya data, beräknas' med hjälp av den här modellen ska ha samma funktioner och schema som träning och testning. Funktionen egenskaper (typ, densitet, distribution och så vidare) nya data bör matcha datauppsättningar träning och testning. Det här avsnittet fokuserar på sådana datakrav.

### <a name="relevant-data"></a>Relevanta data

Först data måste vara _berör problemet_. Överväg att i _rulla fel_ användningsfall som beskrivs ovan - utbildning data ska innehålla funktioner relaterade till hjul-åtgärder. Om problemet är att förutsäga fel på den _drivande system_, av att träningsinformationen måste omfatta de olika komponenterna för drivande system. Det första fallet riktar sig till en särskild komponent, medan det andra fallet riktar sig mot fel på en större undersystemet. Allmänt rekommenderar vi att konstruera förutsägelse system om specifika komponenter i stället för större undersystem, eftersom dessa kommer är mer utspridda data. Domän-expert (se [kvalificerande problem för förebyggande underhåll](#Qualifying-problems-for-predictive-maintenance)) bör bidra till att välja de mest relevanta delmängderna av data för analys. De relevanta datakällorna beskrivs mer detaljerat i [förberedelse av Data för förebyggande underhåll](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Tillräckligt med data
Två frågor är vanliga avseende fel historikdata: (1) ”hur många misslyckade händelser krävs för att träna en modell”? (2) ”hur många poster som betraktas som” det finns tillräckligt med ””? Det finns inga slutgiltiga svar, men endast tumregel. För (1) bättre fler antal misslyckade händelser modellen. För (2), och det exakta antalet misslyckade händelser beror på data och kontexten för att lösa problemet. Men på Vänd-sida om en dator misslyckas för ofta sedan verksamheten ersätts den, vilket minskar fel instanser. Här igen och är riktlinjerna från domänen expert viktigt. Det finns dock metoder för att hantera problemet med _ovanliga händelser_. De beskrivs i avsnittet [hantering av imbalanced data](#Handling-imbalanced-data).

### <a name="quality-data"></a>Data kvalitet
Kvaliteten på data som är kritiska - varje ge prognoser attributvärdet måste vara _korrekt_ tillsammans med ett värde för target-variabel. Kvalitet är ett väl studerade område i hantering av statistik och data och därför ut av den här guidens omfattning.

> [!NOTE]
> Det finns flera resurser och enterprise-produkter att leverera data kvalitet. Ett exempel på referenser finns nedan:
> - Dasu T, Johnson, T., undersökande datautvinning och datarensning, Wiley, 2003.
> - [Undersökande dataanalys, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein J kvantitativa Datarensning för stora databaser](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [Tyskland Jonge, E, van der slå M introduktion till Datarensning med R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Förberedelse av data för förebyggande underhåll

### <a name="data-sources"></a>Datakällor

De relevanta data för förebyggande underhåll innefatta, men är inte begränsade till:
- Fel-historik
- Underhåll och reparera historik
- Datorn driftsförhållanden
- Utrustning metadata

#### <a name="failure-history"></a>Fel-historik
Felhändelser är ovanliga i PdM program. När du skapar modeller för förutsägelse måste algoritmen att lära dig om en komponent normal drift mönster som dess fel-mönster. Så utbildning data bör innehålla tillräckligt antal exempel från båda kategorier. Historik för underhållsläge poster och delar ersättning är bra källor att hitta felhändelser. Med hjälp av kunskap om vissa definieras avvikelser i utbildning data också som fel.

#### <a name="maintenancerepair-history"></a>Underhåll och reparera historik
Underhållshistorik för en tillgång innehåller information om komponenter bytts ut kan reparera aktiviteter som utförs etc. Dessa händelser registrera försämring mönster. Avsaknad av den här viktiga informationen i av att träningsinformationen kan leda till missvisande modellen resultat. Fel-Historik finns även i Underhållshistorik som särskilda felkoder eller datum för delar. Ytterligare datakällor som påverkar fel mönster bör undersökas och tillhandahålls av domän experter.

#### <a name="machine-operating-conditions"></a>Datorn driftsförhållanden
Temperatursensor baserat (eller andra) strömmande data av utrustning i åtgärden är en viktig källa. En nyckel i PdM antas att en dators hälsostatus försämras med tiden under driften rutinunderhåll. Data förväntas innehålla tid olika funktioner som samlar in det här mönstret ålder och eventuella avvikelser som leder till försämring. Tidsbestämd aspekt av data krävs för algoritmen att lära dig felet och icke-fel mönster över tid. Baserat på dessa datapunkter algoritmen lär sig att förutse hur många fler enheter tid som en dator kan fortsätta att arbeta innan det misslyckas.

#### <a name="static-feature-data"></a>Statisk funktionsdata
Statiska funktioner är metadata om utrustning. Exempel är enhetens tillverkare, modell, tillverkade datum, startar datum för tjänsten, platsen för systemet och andra tekniska specifikationer.

Exempel på relevanta data för den [exempel PdM användningsfall](#Sample-PdM-use-cases) tabellen nedan:

| Användningsfall | Exempel på relevanta data |
|:---------|---------------------------|
|_Svarta fördröjning och avbryta_ | Väg flyginformation i form av svarta ben och sidan loggfiler. Svarta ben data omfattar routning information, till exempel avvikelse/ankomst datum, tid, flygplats, layovers osv. Sidan loggen innehåller ett antal fel och underhåll koder som har registrerats av grunden underhållspersonal.|
|_Flygplan motorn delar fel_ | Data samlas in från sensorer i flygplan som innehåller information om de olika delarna. Underhåll poster att identifiera när fel uppstod och när de har ersatts.|
|_ATM-fel_ | Sensoravläsningar för varje transaktion (deponera kontanter/checka) och leverans av kontanter. Information på mätning mellanrummet mellan anteckningar, Observera tjocklek, Observera ankomst avståndet, kontrollera attribut osv. Underhåll poster som innehåller felkoder, reparationsinformation senast tid kontanter utgivaren har fyllas.|
|_Vind turbinen fel_ | Sensorer övervaka turbinen villkor, till exempel temperatur, vindriktning, power genereras, generator hastighet osv. Data har samlats in från flera vind turbiner från vind grupper i olika regioner. Varje turbinen har vanligtvis flera sensoravläsningar vidarebefordrar mått med ett fast tidsintervall.|
|_Fel för strömbrytare_ | Underhåll av loggar med korrigerande, förebyggande och systematiskt åtgärder. Användningsdata med automatisk och manuell kommandon som skickas till utlösts som för åtgärder som öppna och stänga. Enhetens metadata, till exempel datum för tillverkning, plats, modell, osv. Strömbrytare specifikationer som spänning nivåer, geoplats, omgivande förhållanden.|
|_Snabba dörren fel_| Snabba metadata, till exempel typ av snabba, tillverkade datum, Underhåll frekvens, skapande av typen och så vidare. Teknisk information, till exempel antalet dörren cykler, genomsnittlig dörren Stäng tid. Fel historik med orsaker.|
|_Hjul fel_ | Sensordata att åtgärder rulla acceleration, bromsar instanser, intresseväckande avståndet, velocity osv. Information om statisk på hjul som tillverkare tillverkats datum. Fel data härledas från en del ordning databas som spårar ordning datum och kvantiteter.|
|_Språng train dörren fel_ | Dörren inledande och avslutande gånger, andra operativa data, till exempel train dörrar aktuella tillstånd. Statiska data omfattar tillgången identifierare, tid och villkoret värdet kolumner.|

### <a name="data-types"></a>Datatyper
Anges ovan datakällorna är två huvudsakliga datatyperna i PdM domän:

- _Tidsbestämd data_: operativa telemetri, datorn villkor, arbete ordning typer prioritetskoder som har tidsstämplar vid tiden för registrering. Fel, underhåll och reparera och användningshistorik har också tidsstämplar som är associerade med varje händelse.
- _Statiska data_: datorn funktioner och operatorn funktioner i allmänhet är statiska eftersom de beskriver de tekniska specifikationerna för datorer eller operator-attribut. De bör också ha tidsstämplar som är kopplade till dem om dessa funktioner kan ändras med tiden.

Ge prognoser och mål för variabler ska vara meddelandefilsposter/omvandlas till [numeriska kategoriska och andra datatyper](https://www.statsdirect.com/help/basics/measurement_scales.htm) beroende på den algoritm som används.

### <a name="data-preprocessing"></a>Förbearbetning av data
Att _egenskapsval_, förbereda data från olika dataströmmar för att skapa ett schema som det är enkelt att skapa funktioner. Visualisera data först som en tabell med poster. Varje rad i tabellen representerar en instans av utbildning och kolumnerna representerar _ge prognoser_ funktioner (kallas även oberoende attribut eller variabler). Ordna data så att de senaste kolumner är den _mål_ (beroende variabel). För varje instans av utbildning, tilldela en _etikett_ som värde för den här kolumnen.

Dela upp sensordata varaktighet i tidsenheter för temporala data. Varje post ska tillhöra en tidsenhet för en tillgång _och bör ge distinkta information_. Tidsenheter definieras baserat på affärsbehov i multiplar av sekunder, minuter, timmar, dagar, månader, och så vidare. Tidsenheten _behöver inte vara samma som frekvensen för insamling av_. Om frekvensen är högt kan visa data kan inte betydande skillnader från en enhet till en annan. Anta exempelvis att temperatur samlades in var 10: e sekund. Med samma tid för träningsdata endast blåses upp antal exempel utan att ange ytterligare information. För det här fallet är en strategi för bättre att använda medelvärde data över 10 minuter eller en timme baserat på affärsjustering.

För statiska data
- _Underhåll poster_: Raw Underhåll data har en identifierare för tillgångsinformation och tidsstämpel med information om av underhållsaktiviteter som har utförts vid en viss tidpunkt. Transformera underhållsaktiviteter i _kategoriska_ kolumner, där varje kategori descriptor unikt mappar till en specifik underhållsåtgärd. Schemat för Underhåll poster omfattar tillgången identifierare, tid och åtgärden för platsunderhåll.

- _Fel poster_: fel eller orsaker till felet kan registreras som specifika felkoder eller misslyckade händelser som definierats av specifika villkor. I fall där utrustningen har flera felkoder att domänen expert identifiera de som är relevant för en målvariabel. Använda återstående felkoder eller villkor för att konstruera _ge prognoser_ funktioner som stämmer med dessa fel. Schemat för fel poster omfattar tillgången identifierare, tid, fel eller felorsak - om det är tillgängligt.

- _Metadata för datorn och operatorn_: Koppla datorn och operatorn data till ett schema för att associera en tillgång med dess operator, tillsammans med deras respektive attribut. Schemat för datorn villkor omfattar tillgången identifierare, tillgångsinformation funktioner, operatorn identifierare och operatorn funktioner.

Andra data förbearbetning steg innehålla _hanterar saknade värden_ och _normalisering_ över attributvärden. En detaljerad beskrivning är utanför omfattningen för den här guiden - finns i nästa avsnitt för vissa användbara referenser.

Med ovanstående meddelandefilsposter datakällor på plats, sista omvandlingen innan funktionen tekniker är att ansluta till ovanstående tabeller baserat på tillgången identifierare och tidsstämpel. Den resulterande tabellen skulle ha null-värden för kolumnen fel när datorn är normal drift. Null-värden kan tillräknade genom en indikator för normal drift. Använd den här fel-kolumnen för att skapa _etiketter för förutsägelsemodellen_. Mer information finns i avsnittet på [modeling tekniker för förebyggande underhåll](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Funktionstekniker
Funktionen tekniker är det första steget innan modeling data. Roll i datavetenskap processen [som beskrivs här](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). En _funktionen_ är en förebyggande attribut för modellen – till exempel temperatur, tryck, vibrationer och så vidare. För PdM innebär att funktionen tekniker abstrahera en dators hälsotillstånd över historiska data som samlas in via en varaktighet som kan ändras. Som skiljer det sig från dess peer-datorer som fjärrövervaknings, avvikelseidentifiering och upptäcka fel. 

### <a name="time-windows"></a>Tidsfönster
Fjärråtkomst övervakning innefattar rapporteringshändelser hända med _tidpunkter_. Avvikelseidentifiering identifiering modeller utvärdera (poäng) inkommande dataströmmar till flaggan avvikelser från och med punkter i tiden. Felidentifiering klassificerar fel ska särskilda typer när de inträffar punkter i tiden. Däremot PdM innebär att förutsäga fel under en _framtida tidsperiod_, baserat på funktioner som representerar datorn beter över _historisk tidsperiod_. För PdM är funktionsdata från enskilda punkter tid för mycket brus ska förutsägbara. Så data för varje funktion måste vara _smoothened_ genom att sammanställa datapunkter över tidsfönster.

### <a name="lag-features"></a>Lag funktioner
Affärsbehoven definierar hur långt modellen har att förutsäga i framtiden. I sin tur varaktigheten bidrar till att definiera 'hur långt tillbaka modellen måste söka ”att göra förutsägelser för dessa. Den här 'söker tillbaka' tidsperiod kallas den _fördröjning_, och funktioner som utvecklats under den här fördröjningen kallas _lag funktioner_. Det här avsnittet beskrivs fördröjning funktioner som kan konstrueras från datakällor med tidsstämplar och skapande av funktionen från statisk datakällor. Fördröjning funktionerna finns vanligtvis _numeriska_ till sin natur.

> [!IMPORTANT]
> Fönstrets storlek bestäms via undersökningar och bör slutföras med hjälp av en expert domän. Samma villkor gäller för urvalet och definitionen av fördröjning funktioner, deras aggregeringar och vilken typ av windows.

#### <a name="rolling-aggregates"></a>Rullande mängder
För varje post för en tillgång valt ett rullande fönster storlek ”W” som antalet enheter tid att beräkna aggregat. Fördröjning funktioner sedan beräknas med W punkter _före_ för den posten. I bild 1 visar blå linjer sensor värden för varje tidsenhet registrerats för en tillgång. De anger en rullande medelvärdet för funktionen värden över ett fönster med storlek W = 3. Det rullande medelvärdet beräknas över alla poster med tidsstämplar som är inom räckhåll t<sub>1</sub> (i orange) till t<sub>2</sub> (i grönt). Värdet för W är vanligtvis i minuter eller timmar beroende på typen av uppgift. Men för vissa problem, väljer ett stort W (Säg 12 månader) kan tillhandahålla hela historiken för en tillgång tills posten.

![Bild 1. Rullande sammanställd funktioner](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) bild 1. Rullande sammanställd funktioner

Exempel på rullande mängder över ett tidsfönster är antal, medel, CUMESUM (kumulativa summan) åtgärder, minsta/högsta värden. Dessutom används ofta varians, standardavvikelse och antal avvikare utöver N standardavvikelser. Exempel på mängder som kan användas för den [användningsfall](#Sample-PdM-use-cases) i den här handboken visas nedan. 
- _Flight fördröjning_: antal felkoder under föregående dag/vecka.
- _Flygplan motorn del fel_: rullande innebär och standardavvikelsen summan under den senaste dagen vecka osv. Det här måttet fastställas tillsammans med domänen business expert.
- _ATM-fel_: rullande innebär, median, intervall, standardavvikelser, antal avvikare utöver tre standardavvikelser övre och nedre CUMESUM.
- _Språng train dörren fel_: antalet händelser under föregående dag, vecka, två veckor osv.
- _Strömbrytare fel_: fel antal över senaste veckan, år, tre år osv.

En annan användbar metod i PdM är att samla in trend ändringar, toppar och ändras med hjälp av algoritmer som identifierar avvikelser i data.

#### <a name="tumbling-aggregates"></a>Rullande mängder
Etikett för varje post för en tillgång, ett fönster med storleken _W -<sub>k</sub>_  definieras, där _k_ är antalet windows storlek _W_. Aggregeringar skapas sedan över _k_ _rullande windows_ _W-k, V -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  för perioder innan en post tidsstämpel. _k_ kan vara ett litet antal att avbilda kortsiktig effekter eller ett stort antal att avbilda långsiktiga försämring mönster. (se bild 2).

![Figur 2. Rullande sammanställd funktioner](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) bild 2. Rullande sammanställd funktioner

Till exempel lag funktioner för användningsfall för vind turbiner kan skapas med W = 1 och k = 3. De innebär fördröjning för var och en av de tre senaste månaderna med övre och nedre avvikare.

### <a name="static-features"></a>Statiska funktioner

Tekniska specifikationer utrustning, till exempel datum till tillverkning, modellnummer, plats, är några exempel på funktioner som statisk. De behandlas som _kategoriska_ variabler för förutsägelsemodellering. Några exempel på användningsfall strömbrytare är spänning, aktuella, Strömkapaciteten, transformatortypen och kraftkälla. För hjul fel är typ av däck hjul (legerat vs stål) ett exempel.

På arbetet för förberedelse av data beskrivs hittills bör leda till de data som är ordnade enligt nedan. Utbildning, testa och validering data ska ha den här logiska schemat (det här exemplet visar tid i antal dagar).

| Tillgångs-ID | Tid | <Feature Columns> | Etikett |
| ---- | ---- | --- | --- |
| A123 |Dag 1 | . . . | . |
| A123 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dag 1 | . . . | . |
| B234 |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

Det sista steget i funktionen tekniker är den **etiketter** på mål-variabeln. Den här processen är beroende av modellering-teknik. I sin tur beror modellering tekniken på affärsproblem och arten av de tillgängliga data. Etiketter beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Förberedelse av data och funktionen tekniker är lika viktig som modeling tekniker för att komma fram till framgångsrika PdM lösningar. Domän-expert och läkaren ska investera betydande tid i anländer till rätt funktioner och data för modellen. Nedan visas ett litet exempel från många böcker i funktionen tekniker:
> - Pyle D. dataförberedelse för datautvinning (Morgan Kaufmann serien i Data Management Systems), 1999
> - Zheng A., Casari A. funktionen tekniker för Machine Learning: principer och metoder för Data forskare, O'Reilly, 2018.
> - Dong G. Liu H. (Redigerare) funktionen tekniker för Machine Learning och dataanalys (Chapman & Hall/CRC datautvinning och kunskap Discovery-serien) CRC tryck 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Tekniker för förutsägelsemodellering för förebyggande underhåll

Det här avsnittet beskrivs huvudsakliga modellering tekniker för PdM problem, tillsammans med deras specifika etikett konstruktionen metoder. Observera att du kan använda en enda modellering teknik mellan olika branscher. Modellering tekniken har länkats till vetenskap dataproblem i stället för kontexten av data till hands.

> [!IMPORTANT]
> Valet av etiketter för fel fall på och strategin för märkning  
> fastställas i samråd med experter domänen.

### <a name="binary-classification"></a>Binär klassificering
Binär klassificering används för att _förutsäga sannolikheten att utrustning misslyckas i en framtida tidsperiod_ – kallas den _framtida horizon period X_. X bestäms av problem i verksamheten och data till hands i samråd med experter domänen. Exempel är:
- _minsta ledtid_ krävs för att ersätta komponenter, distribuera Underhåll resurser, utföra underhåll för att undvika problem som kan uppstå under denna tid.
- _minsta antal händelser_ som kan inträffa innan ett problem uppstår.

Den här tekniken identifieras två typer av utbildning exempel. Ett positivt exempel _vilket betyder att_, med etiketten = 1. En negativ exempel som visar normal drift, med etiketten = 0. Mål-variabeln och finns därför etikettsvärdena _kategoriska_. Modellen ska identifiera varje ny exempel som kan misslyckas eller fungera normalt under kommande X tidsenheter.

#### <a name="label-construction-for-binary-classification"></a>Etikett konstruktionen för binär klassificering
Den här frågan är ”: Vad är sannolikheten att tillgången misslyckas under kommande X tidsenheter”? Besvara den här frågan, etikett X poster innan felet inträffade för en tillgång som ”väg till misslyckas” (etikett = 1), och alla poster som ”normal” en etikett (etikett = 0). (se bild 3).

![Bild 3. Etiketter för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) bild 3. Etiketter för binär klassificering

Exempel på etiketter strategi för några av användningsområden som anges nedan.
- _Flight fördröjningar_: X kan väljas som 1 dag att förutsäga fördröjningar i nästkommande 24 timmar. Sedan alla flygplan inom 24 timmar innan fel är märkta som 1.
- _ATM kontanter överflödiga fel_: ett mål kan vara att fastställa sannolikheten för fel för en transaktion i nästa en timme. I så fall är alla transaktioner som har inträffat i den senaste timmen till felet märkta som 1. För att förutsäga sannolikheten för fel under nästa N valuta märkta anteckningar vara, alla anteckningar vara inom de sista N anteckningarna för ett fel som 1.
- _Strömbrytare fel_: målet vara att förutsäga nästa strömbrytare-kommandot misslyckades. I så fall väljs X ska vara ett framtida kommando.
- _Träna dörren fel_: X kan väljas som två dagar.
- _Vindkraft turbinen fel_: X kan väljas som två månader.

### <a name="regression-for-predictive-maintenance"></a>Regression för förebyggande underhåll
Regression modeller används för att _beräkna återstående livslängd (RUL) en tillgång_. RUL definieras som hur lång tid som en tillgång fungerar innan nästa fel inträffar. Varje utbildning exempel är en post som tillhör en tidsenhet _nY_ för en tillgång, där _n_ är en multipel. Modellen kan beräkna RUL för varje ny exempel som en _kontinuerligt nummer_. Det här numret anger tidsperioden som återstår innan felet inträffade.

#### <a name="label-construction-for-regression"></a>Etikett konstruktionen för regression
Den här frågan är: ”Vad är återstående livslängd (RUL) utrustningen”? Beräkna etiketten som antalet enheter återstående tid innan nästa fel för varje post innan felet inträffade. I den här metoden är etiketter kontinuerlig variabler. (Se figur 4)

![Bild 4. Etiketter för regression](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) bild 4. Etiketter för regression

Etiketter är klar för regression, med en felpunkt. Beräkningen är inte möjligt utan att känna till hur länge tillgången har fortfarande varit i livet innan ett fel uppstod. Så kan i motsats till binär klassificering tillgångar utan några fel i data inte användas för modellering. Det här problemet är bäst med ett annat statistiska teknik som kallas [överlevnad Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Men potentiella problem kan uppstå när du använder den här tekniken till PdM användningsområden som avser tiden varierande data med återkommande intervall. Mer information om överlevnad analys finns [detta en personsökare](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Flera klassen klassificering för förebyggande underhåll
Tekniker för flera klassen klassificering kan användas i PdM lösningar för två scenarier:
- Förutsäga _två framtida resultat_: första resultatet är _ett tidsintervall fel_ för en tillgång. Tillgången är tilldelad till en av flera möjliga tidsperioder. Andra resultatet är risken för fel i en framtida period på grund av att _gör att en av flera roten_. Den här förutsägelse kan Underhåll-teamet att bevaka symptom och scheman för programvaruunderhåll av planen.
- Förutsäga _mest sannolikt grundorsaken_ av ett angivet fel. Det här resultatet rekommenderar rätt uppsättning underhållsåtgärder för att åtgärda ett fel. En rankningslista över rotorsaker och rekommenderade reparationer hjälper tekniker prioritera sina reparationsåtgärder efter ett fel.

#### <a name="label-construction-for-multi-class-classification"></a>Etikett konstruktionen för flera klassen klassificering
Den här frågan är ”: Vad är sannolikheten att en tillgång misslyckas under kommande _nZ_ tidsenheter där _n_ är antalet perioder”? Etikett nZ poster innan felet inträffade för en tillgång med buckets tid (3Z 2Z Z) för att besvara den här frågan. Etikett alla andra registrerar ”normal” (etikett = 0). I den här metoden innehåller en målvariabel _kategoriska_ värden. (Se bild 5).

![Bild 5. Etiketter för multiklass-baserad klassificering för fel tid förutsägelse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) bild 5. Etiketter för klassificering av flera klass för fel tid förutsägelse

Den här frågan är ”: Vad är sannolikheten att tillgången misslyckas under kommande X tidsenheter på grund av orsak/rotproblemet _P<sub>jag</sub>_”? där _jag_ är antalet möjliga underliggande orsaker. Besvara den här frågan, etikett X poster innan felet inträffade för en tillgång som ”kommer att misslyckas på grund av rotorsaken _P<sub>jag</sub>_” (etikett = _P<sub>jag</sub>_). Namnge alla poster som ”normal” (etikett = 0). I den här metoden är etiketter också kategoriska (se bild 6).

![Bild 6. Etiketter för multiklass-baserad klassificering för rot-orsaken förutsägelse](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) bild 6. Etiketter för klassificering av flera klass för rot-orsaken förutsägelse

Modellen tilldelar en sannolikheten för fel på grund av varje _P<sub>jag</sub>_  samt sannolikheten för fel. Dessa sannolikhet kan beställas storlek så att förutsägelser av de problem som är mest sannolikt att i framtiden.

Den här frågan är: ”vilka underhållsåtgärder rekommenderar du efter en misslyckad”? Den här frågan besvaras etiketter _kräver inte en framtida horizon tas ut_eftersom modellen inte förutsäga fel i framtiden. Det är bara att förutsäga det troligen rotorsaken _när felet uppstod redan_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Utbildning, verifiering och tester metoder för förebyggande underhåll
Den [Team datavetenskap Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) ger en fullständig täckning för modellen train-test-Validera cykel. Det här avsnittet beskrivs aspekter som är unika för PdM.

### <a name="cross-validation"></a>Mellan verifiering
Målet med [mellan validering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) är att definiera en datauppsättning till ”test” modellen i utbildning-fasen. Den här datamängden kallas den _validering set_. Den här tekniken hjälper gränsen problem angående _overfitting_ och ger en analys på hur modellen kommer generalisera till en fristående uppsättning data. Det vill säga en okänd data har angetts som kan komma från en verkliga problemet. Rutinen utbildning och tester för PdM måste ta hänsyn till tid olika aspekter att generalisera bättre på överblivna framtida data.

Många maskininlärningsalgoritmer beror på ett antal justeringsmodeller som kan ändra modellen prestanda avsevärt. Optimala värdena för dessa justeringsmodeller beräknas inte automatiskt vid inlärning av modellen. De bör anges av forskare data. Det finns flera sätt att hitta bra värdena för justeringsmodeller.

De vanligaste en är _k-vikning korsvalidering_ som delar upp exempel slumpmässigt i _k_ vikningar. För varje uppsättning värden för justeringsmodeller, kör du Inlärningsalgoritmen _k_ gånger. Vid varje iteration använder du exemplen i den aktuella vikningen som en uppsättning validering och resten av exemplen som en träningsmängden. Träna algoritmen över utbildning exempel och beräkna validering exempel prestandamåtten. I slutet av denna loop beräkna genomsnitt av _k_ prestandamått. Välj de som har bästa genomsnittliga prestanda för varje uppsättning hyperparameter värden. Aktiviteten för att välja justeringsmodeller är ofta försök till sin natur.

Data registreras i PdM problem, som en tidsserie händelser som kommer från flera datakällor. Dessa poster kan sorteras efter tiden för etiketter. Därför, om datamängden delas _slumpmässigt_ till utbildning och validering uppsättning _vissa av utbildning exempel kan vara senare än några av verifieringen exempel_. Framtida prestanda för hyperparameter värden beräknas baserat på vissa data som anlänt _innan_ modellen har tränats. Dessa beräkningar kanske alltför optimistisk, särskilt om tidsserien inte är stilla och utvecklas över tid. Valda hyperparameter värden kanske därför.

Det rekommenderade sättet är att dela exemplen i utbildnings- och verifiering som angetts i en _tid beroende_ sätt, där alla validering exemplen är senare än alla utbildning exempel. Träna algoritmen över datauppsättning för träning för varje uppsättning hyperparameter värden. Mät modellens prestanda över samma verifiering. Välj hyperparameter som visar att uppnå bästa prestanda. Hyperparameter värdena som väljs av train-verifiering dela resulterar i bättre framtida modellen prestanda än med värden som väljs slumpmässigt av korsvalidering.

Den slutliga modellen kan genereras av utbildning en inlärningsalgoritm över hela utbildningsdata med hjälp av bästa hyperparameter värden.

### <a name="testing-for-model-performance"></a>Testa prestanda för modellen
När en modell skapas, krävs en uppskattning av dess framtida prestanda på nya data. En bra beräkna prestanda mått för hyperparameter värden beräknas över verifieringen, eller ett mått för genomsnittliga prestanda som beräknats från korsvalidering. Dessa beräkningar är ofta alltför optimistisk. Företaget kanske ofta vissa ytterligare riktlinjer för hur de vill testa modellen.

Det rekommenderade sättet för PdM är att dela exemplen i utbildning, verifiering, och testdata en _tid beroende_ sätt. Alla test exempel ska vara senare än alla utbildning och validering exempel. När delning, generera modellen och mäta dess prestanda som tidigare beskrivits.

När-tidsserier är stilla och enkelt att förutsäga kan generera slumpmässigt och tid beroende metoder liknande uppskattningar av framtida prestanda. Men när tidsserie ej stationära och/eller svårt att förutse, tid beroende metoden genererar mer realistisk uppskattningar av framtida prestanda.

### <a name="time-dependent-split"></a>Tid beroende delning
Det här avsnittet beskrivs bästa praxis för att implementera tid beroende delning. En dubbelriktad delning tid beroende mellan utbildnings- och testuppsättningar som beskrivs nedan.

Anta en dataström med tidsstämplad händelser, t.ex mått från olika sensorer. Definiera funktioner och etiketter och träna och testa exempel över tidsramar som innehåller flera händelser. Till exempel för binär klassificering, skapa funktioner baserat på tidigare händelser och skapa etiketter som baseras på framtida händelser inom ”X” tidsenheter i framtiden (se avsnitt på [egenskapsval](#Feature-engineering) och [modellering tekniker](#Modeling-techniques-applied-to-PdM-use-cases)). Därför kommer märkning tidsintervall för ett exempel senare än tid som dess funktioner.

Tid beroende delning, Välj en _utbildning klara tiden T<sub>c</sub>_  som tränar en modell med justeringsmodeller justerade med historiska data upp till T<sub>c</sub>. För att förhindra läckor av framtida etiketter som ligger utanför T<sub>c</sub> i utbildning-data väljer du den senaste tidpunkten etikett utbildning exempel ska X enheter innan T<sub>c</sub>. I exemplet som visas på bild 7 representerar varje ruta en post i datauppsättningen där funktioner och etiketter beräknas enligt beskrivningen ovan. Bilden visar de poster som ska gå till träning och testning uppsättningar för X = 2 och W = 3:

![Bild 7. Tid beroende dela för binär klassificering](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) bild 7. Tid beroende dela för binär klassificering

Grön rutor representerar poster som hör till enheterna som kan användas för utbildning. Varje utbildning exempel genereras genom att beakta tidigare perioderna för generering av funktionen och två framtida perioder för etikettering innan T<sub>c</sub>. När någon del av de kommande två perioderna ligger utanför T<sub>c</sub>, utesluta datauppsättning för träning som exempel eftersom ingen synlighet antas utöver T<sub>c</sub>.

De svarta rutorna representerar poster av slutliga märkt datamängden som inte ska användas i datauppsättning för träning, får den ovanstående begränsningen. Dessa poster också används inte vid testning av data, eftersom de är innan T<sub>c</sub>. Dessutom beror deras märkning tidsramar delvis på tidsramen utbildning som inte är idealiskt. Träning och testning data bör ha separata märkning tidsramar för att förhindra etikett information sprids.

Den metod som beskrivs hittills kan för ett överlapp mellan träning och testning exemplen som har tidsstämplar nära T<sub>c</sub>. En lösning för att uppnå högre uppdelning är att undanta exempel som ligger inom W tidsenheter t<sub>c</sub> från testet. Men ett aggressivt dela beror på gott om datatillgänglighet.

Regression modeller som används för att förutsäga RUL störs mer av läckage av problemet. Med metoden slumpmässiga dela leder till extrema över passning. Regression problem delningen ska vara så att de poster som hör till tillgångar med fel innan T<sub>c</sub> gå till träningsmängden. Register över tillgångar med fel efter brytfrekvens går du till test-uppsättning.

En annan bästa praxis för att dela data för träning och testning är att använda en delning av tillgångsinformation-ID. Delningen ska vara så att ingen av de resurser som används i träningsmängden används i testa modellen. En modell har med den här metoden större möjlighet att ge mer realistisk resultat nya tillgångar.

### <a name="handling-imbalanced-data"></a>Hantering av imbalanced data
Klassificering problem om det finns fler exempel på en klass än de andra datauppsättningen anses vara _imbalanced_. Vi rekommenderar tillräckligt med representanter för varje klass i av att träningsinformationen är att föredra framför aktivera skillnad mellan olika klasser. Om en klass är mindre än 10% av data, anses data som imbalanced. Underrepresented klassen kallas ett _minoritet klassen_.

Många PdM problem står inför sådana imbalanced datauppsättningar, där en klass kraftigt underrepresented jämfört med de andra klass eller klasser. I vissa situationer kan klassen minoritet utgöra Endast 0,001% av totalt antal datapunkter. Klassen obalans är inte unikt för PdM. Andra domäner där fel och inkonsekvenser är ovanligt förekomster står inför ett liknande problem, till exempel, att upptäcka bedrägerier och intrång. Dessa fel utgör minoritet klassen exemplen.

Med klassen obalans i data komprometteras prestanda för de flesta standard algoritmer för maskininlärning, eftersom de syftar till att minimera den övergripande Felfrekvensen. För en datauppsättning med 99% negativt och 1% positivt exempel kan en modell visas att ha 99% noggrannhet genom etiketter alla instanser som negativt. Men modellen kommer felaktigt klassificera alla positivt exempel; Så även om dess noggrannhet är hög, algoritmen inte är ett användbart. Följaktligen konventionella utvärdering mått som _övergripande noggrannhet på Felfrekvens_ är inte tillräckliga för imbalanced learning. Andra mått används för utvärdering av modellen vid inför imbalanced datauppsättningar:
- Precision
- Återkalla
- F1-resultat
- Kostnad justeras ROC (mottagare operativa egenskaper)

Mer information om de här måtten finns [modell utvärdering](#Model-evaluation).

Det finns dock några metoder som hjälper avhjälpa klassen obalans problem. De två viktigaste som är _provtagning tekniker_ och _kostnad känsliga learning_.

#### <a name="sampling-methods"></a>Provtagning
Imbalanced learning omfattar prover metoder för att ändra datauppsättning för träning till en belastningsutjämnade datauppsättning. Sampling metoder får inte tillämpas på uppsättningen test. Även om det finns flera metoder för provtagning, de flesta direkt vidarebefordra som är _slumpmässiga oversampling_ och _under provtagning_.

_Slumpmässiga oversampling_ innebär att välja ett slumpmässigt prov minoritet klass, replikera exemplen och lägger till dem datauppsättning för träning. Följaktligen antalet exempel i minoritet klass ökas och slutligen balansera antalet exempel på olika klasser. En oversampling Nackdelen är att flera instanser av vissa exempel kan orsaka klassificeraren ska bli för specifika leda till överdrivet passning. Modellen kan visa hög utbildning Precision, men dess prestanda på överblivna testdata kan vara något sämre.

Däremot _slumpmässiga under provtagning_ är att välja ett slumpmässigt prov från en majoritet klass och ta bort dessa exempel från datauppsättning för träning. Tar bort exempel från majoritet klass kanske dock klassificeraren att missa viktiga begrepp som rör majoritet klassen. _Hybrid provtagning_ där minoritet klass är över provade och majoritet klassen är under-prov på samma tid är ett annat användbart sätt.

Det finns många avancerade provtagningar. Tekniken valt är beroende av egenskaper för data och resultatet av iterativ experiment av forskare data.

#### <a name="cost-sensitive-learning"></a>Kostnad känsliga learning
I PdM är fel som utgör klassen minoritet mer intressant än vanliga exempel. Så fokuserar huvudsakligen på den algoritm prestanda vid fel. Felaktigt förutsäga en positiv klass som en negativ klass kan kostar mer än vice versa. Den här situationen kallas ofta olika förlust eller asymmetriska kostnaden för felaktigt klassificera element till olika klasser. Idealiskt klassificeraren bör ge hög förutsägelsefunktionen jämfört klassen minoritet utan att kompromissa med precisionen för klassen majoritet.

Det finns flera sätt att uppnå detta saldo. Tilldela felaktig klassificering av klassen minoritet en hög kostnad för att åtgärda problemet med olika förlust, och försök att minska den sammanlagda kostnaden. Algoritmer som _SVMs (Support Vector datorer)_ antar den här metoden natur, genom att tillåta kostnaden för positiva och negativa exempel anges vid träning. På samma sätt som förstärkning metoder _tvåklassförhöjt beslutsträd_ vanligtvis visa goda prestanda med imbalanced data.

## <a name="model-evaluation"></a>Modellen utvärdering
Felaktig klassificering är ett betydande problem för PdM scenarier där kostnaden för falsklarm till verksamheten är hög. Till exempel ett beslut att bakgrund ett flygplan baserat på en felaktig förutsägelser av motorn fel störa scheman och reser planer. Tar en dator offline från en sammansättning rad kan leda till förlust av intäkter. Utvärdering av modellen med rätt prestandamåtten mot nya testdata är därför viktigt.

Vanliga prestandamått som används för att utvärdera PdM modeller beskrivs nedan:

- [Precision](https://en.wikipedia.org/wiki/Accuracy_and_precision) är den mest populära mått som används för att beskriva en klassificerare prestanda. Men noggrannhet är känsligt för distributioner av data och är en ineffektiv mått för scenarier med imbalanced datauppsättningar. Andra mått används i stället. Verktyg som [förvirring matrisen](https://en.wikipedia.org/wiki/Confusion_matrix) används för att beräkna och skäl om korrektheten i modellen.
- [Precision](https://en.wikipedia.org/wiki/Precision_and_recall) av PdM modeller som relaterar till mängden falsklarm. Lägre precision av modellen motsvarar vanligtvis en högre andel falsklarm.
- [Återkalla](https://en.wikipedia.org/wiki/Precision_and_recall) frekvensen anger hur många fel i uppsättningen test identifierades korrekt av modellen. Högre återkallning priser betyder modellen är lyckas identifiera true felen.
- [F1 poäng](https://en.wikipedia.org/wiki/F1_score) är övertonsutsläpp medelvärdet av precision och återkallande, med dess värde mellan 0 (sämsta) till 1 (bäst).

För binär klassificering
- [Mottagaren operativsystem kurvor (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) är också ett populärt mått. I ROC kurvor tolkas modellen prestanda baserat på en fast operativa platsen på ROC.
- Men för PdM problem _decile tabeller_ och _lyfter diagram_ är mer informativ. De fokus ligger endast på klassen positivt (fel) och ger en mer komplex bild av algoritmen prestanda än ROC kurvor.
  - _Decile tabeller_ skapas med hjälp av test-exempel i en fallande fel sannolikhet. Sorterad exempel grupperas sedan i deciles (10% av prover med största sannolikhet sedan 20%, 30% och så vidare). Förhållandet mellan (true positiva satsen) /(random baseline) för varje decile hjälper beräkna algoritmen prestanda vid varje decile. Slumpmässiga baslinjen tar på värden 0.1, 0,2 och så vidare.
  - [Lyfter diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) Rita decile SANT positiva satsen jämfört med slumpmässiga true positivt hastighet för alla deciles. De första deciles är vanligtvis fokus för resultat, eftersom de visar de största vinsterna. Första deciles kan också ses som representativa för ”oskyddad”, när den används för PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modellen operationalization för förebyggande underhåll

Fördelen datavetenskap Övning är insåg endast när den tränade modellen är operationella. Det vill säga måste modellen distribueras till affärssystem att göra förutsägelser baserat på nya och tidigare överblivna data.  Nya data exakt måste motsvara den _modellen signatur_ av den tränade modellen på två sätt:
- alla funktioner måste finnas i varje logiska instans (t.ex en rad i en tabell) av nya data.
- nya data måste bearbetas före och var och en av funktionerna för bakåtkompilerade, exakt på samma sätt som utbildning-data.

Ovanstående procedur anges på många olika sätt i akademisk och dess dokumentation. Men alla följande instruktioner betyda samma sak:
- _Samla in nya data_ med hjälp av modellen
- _Tillämpar modellen_ för nya data
- _Operationalisera_ modellen
- _Distribuera_ modellen
- _Kör modellen_ mot nya data

Som tidigare nämnts kan skiljer modellen operationalization för PdM sig från dess peer-datorer. Scenarier som omfattar avvikelseidentifiering och felidentifiering vanligtvis implementera _online bedömningen_ (kallas även _realtid bedömningen_). Här, modellen _poäng_ posterna inkommande, och returnerar en förutsägelse. För identifiering av avvikelse förutsägelser är en indikation på att ett fel uppstod (exempel: en klass SVM). Det vore för felidentifiering av typ eller klass av fel.

Däremot PdM omfattar _batchbedömningen_. För att uppfylla modellen signatur, vara funktionerna i den nya informationen utformad på samma sätt som utbildning-data. För stora datauppsättningar som är typiskt för nya data visar det sammanlagda resultatet tidsfönster och bedömas i batch funktioner. Batchbedömningen görs vanligtvis i distribuerade system som [Spark](http://spark.apache.org/) eller [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Det finns ett par alternativ - både något sämre:
- Strömmande data motorer som stöder sammanställning över windows i minnet. Så det kan vara gällande de stöder online bedömningen. Men dessa system är lämpliga för kompakta data i smala windows tid eller sparse element via bredare windows. De kan inte skalas bra för kompakta data över bredare tidsfönster som visas i PdM scenarier.
- Lösningen är att anpassa online bedömningen för att hantera nya data i små batchar samtidigt om batchbedömningen inte är tillgänglig.

## <a name="solution-templates-for-predictive-maintenance"></a>Lösningsmallar för förebyggande underhåll

Den sista delen av den här guiden innehåller en lista över PdM lösningsmallar, självstudier och experiment som implementeras i Azure. Programmen PdM kan distribueras till en Azure-prenumeration i vissa fall: e minut. De kan användas som proof of concept demonstrationer, sandboxar att experimentera med alternativ eller acceleratorer för implementeringar av faktiska produktionen. Dessa mallar finns i den [Azure AI-galleriet](http://gallery.azure.ai) eller [Azure GitHub](https://github.com/Azure). Exemplen olika kommer att ingå i den här lösningen mallen över tid.

| # | Titel | Beskrivning |
|--:|:------|-------------|
| 1 | [Azure förutsägande Underhåll Machine Learning-exempel](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance) |PdM exempel att förutsäga fel under nästa N enheterna. Det här exemplet skrivs som ett projekt för Azure ML-arbetsstationen och är idealisk för nybörjare till PdM. [Ytterligare dokumentation](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-predictive-maintenance) relaterade till det här exemplet.|
| 2 | [Azure förutsägande Underhåll Lösningsmall](https://github.com/Azure/AI-PredictiveMaintenance) | En slutpunkt till slutpunkt framework att demonstrera scenarier med flera PdM. Den här mallen innehåller två scenarier: först är ett nytt användningsfall i realtid fel villkoret klassificering. Det andra scenariot är helt enkelt en integrering av lösningen [1] i den här lösningen mallen. Den visar hur du kan återanvända samma distribuerade infrastruktur för att lägga till andra nya eller befintliga scenarier.|
| 3 | [Djup Learning för förebyggande underhåll](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure bärbar dator med en demo lösningen med LSTM (lång kortvariga minne) nätverk (en klass återkommande Neurala nätverk) för förebyggande underhåll med en [blogginlägget på det här exemplet](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Guide för Hotmodellering i förutsägande Underhåll i R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Guide för hotmodellering i PdM med skript i R.|
| 5 | [Azure förutsägande Underhåll för Aerospace](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | En av de första PdM lösningsmallar baserat på Azure ML v1.0 för normalt underhåll. Den här guiden kommer från det här projektet. |
| 6 | [Azure AI Toolkit för IoT kant](https://github.com/Azure/ai-toolkit-iot-edge) | AI i IoT kanten med TensorFlow; Toolkit paket djup learning-modeller i Azure IoT kant-kompatibel Docker-behållare och exponera dessa modeller som REST API: er.
| 7 | [Azure IoT förutsägande Underhåll](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite datorer - förkonfigurerade lösningen. Flygplan Underhåll PdM mall med IoT Suite. [Ett annat dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) och [genomgången](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) relaterade till samma projekt. |
| 8 | [Den förutsägande Underhåll mallen med hjälp av SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstration av återstående livslängd scenario som baseras på R-tjänster. |
| 9 | [Guide för Hotmodellering i förutsägande Underhåll](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Flygplan dataset funktionen utvecklats med R med [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) och [datauppsättningar](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) och [Azure anteckningsboken](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) och [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)i AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Utbildningsresurser för förebyggande underhåll

Den [Azure AI Utbildningsväg för förebyggande underhåll](https://github.com/Azure/AI-PredictiveMaintenance/blob/master/docs/azure-ai-learning-path-for-predictive-maintenance.md) ger utbildningsmaterial för en bättre förståelse för begrepp och math bakom algoritmer och tekniker som används i PdM problem. 

Microsoft Azure erbjuder kostnadsfri innehåll och utbildning på allmänna AI-begrepp och praxis.

| Utbildning resurs  | Tillgänglighet |
|:-------------------|--------------|
| [AI Developer på Azure](http://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Offentligt |
| [Microsoft AI skola](http://aischool.microsoft.com/learning-paths) | Offentligt |
| [Azure AI Learning från GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Offentligt |
| [LinkedIn-utbildning](http://www.linkedin.com/learning) | Offentligt |
| [Microsoft AI Youtube Webbseminarier](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Offentligt |
| [Microsoft AI visa](http://channel9.msdn.com/Shows/AI-Show) | Offentligt |
| [LearnAI@MS](http://learnanalytics.microsoft.com) | För Microsoft-partner |
| [Microsoft Partner Network](http://learningportal.microsoft.com) | För Microsoft-partner |

Dessutom är ledigt MOOCS (massiv öppna online kurser) på AI erbjudna online av academic institutioner som Stanford och MIT och andra utbildningssyfte företag.