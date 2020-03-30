---
title: Azure AI-guide för lösningar för förutsägande underhåll – Team Data Science Process
description: En omfattande beskrivning av datavetenskap som driver prediktiva underhållslösningar i flera vertikala branscher.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 5cf3f02284777a54a9d26cad8a7f3b5b4fa6b335
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087810"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Azure AI-guide för lösningar för förebyggande underhåll

## <a name="summary"></a>Sammanfattning

Prediktivt underhåll (**PdM**) är en populär tillämpning av prediktiva analyser som kan hjälpa företag i flera branscher att uppnå hög tillgångsutnyttjande och besparingar i driftskostnader. Den här guiden samlar affärs- och analysriktlinjer och bästa praxis för att framgångsrikt utveckla och distribuera PdM-lösningar med hjälp av [Microsoft Azure AI-plattformstekniken.](https://azure.microsoft.com/overview/ai-platform)

Till att börja med introducerar den här guiden branschspecifika affärsscenarier och processen att kvalificera dessa scenarier för PdM. Datakraven och modelleringsteknikerna för att bygga PdM-lösningar tillhandahålls också. Huvudinnehållet i guiden är på datascience processen - inklusive stegen för dataförberedelse, funktionsteknik, modellskapande och modell operationalization. Som ett komplement till dessa nyckelbegrepp listar den här guiden en uppsättning lösningsmallar som hjälper till att påskynda utveckling av PdM-program. Guiden pekar också på användbara utbildningsresurser för utövaren att lära sig mer om AI bakom datavetenskapen. 

### <a name="data-science-guide-overview-and-target-audience"></a>Översikt över och målgrupp för datavetenskapsguide
Den första halvan av den här guiden beskriver typiska affärsproblem, fördelarna med att implementera PdM för att lösa dessa problem och listar några vanliga användningsfall. Beslutsfattare (BDM) kommer att dra nytta av detta innehåll. Den andra halvan förklarar datavetenskapen bakom PdM och innehåller en lista över PdM-lösningar som skapats med de principer som beskrivs i den här guiden. Det ger också inlärningsvägar och pekare till utbildningsmaterial. Tekniska beslutsfattare (TDMs) kommer att finna detta innehåll användbart.

| Börja med ... | Om du är ... |
|:---------------|:---------------|
| [Business case för prediktivt underhåll](#business-case-for-predictive-maintenance) |en beslutsfattare (BDM) som vill minska driftstopp och driftskostnader och förbättra utnyttjandet av utrustning |
| [Datavetenskap för prediktivt underhåll](#data-science-for-predictive-maintenance) |en teknisk beslutsfattare (TDM) som utvärderar PdM-teknik för att förstå de unika databehandlings- och AI-kraven för prediktivt underhåll |
| [Lösningsmallar för förebyggande underhåll](#solution-templates-for-predictive-maintenance)|en mjukvaruarkitekt eller AI-utvecklare som vill snabbt stå upp för en demo eller ett proof-of-concept |
| [Utbildningsresurser för prediktivt underhåll](#training-resources-for-predictive-maintenance) | något av ovanstående, och vill lära sig de grundläggande begreppen bakom datavetenskap, verktyg och tekniker.

### <a name="prerequisite-knowledge"></a>Förhandskunskaper
BDM-innehållet förväntar sig inte att läsaren ska ha några tidigare datavetenskapliga kunskaper. För TDM-innehållet är grundläggande kunskaper i statistik och datavetenskap till hjälp. Kunskap om Azure Data- och AI-tjänster, Python, R, XML och JSON rekommenderas. AI-tekniker implementeras i Python- och R-paket. Lösningsmallar implementeras med Azure-tjänster, utvecklingsverktyg och SDK:er.

## <a name="business-case-for-predictive-maintenance"></a>Business case för prediktivt underhåll

Företag kräver kritisk utrustning för att köra på topp effektivitet och utnyttjande för att förverkliga sin avkastning på kapitalinvesteringar. Dessa tillgångar kan vara allt från flygplansmotorer, turbiner, hissar eller industriella kylaggregat - som kostar miljoner - ner till vardagliga apparater som kopiatorer, kaffemaskiner eller vattenkylare.
- Som standard förlitar sig de flesta företag på _korrigerande underhåll_, där delar byts ut om och när de misslyckas. Korrigerande underhåll säkerställer att delar används helt (därför inte slösar komponentlivslängd), men kostar verksamheten i driftstopp, arbete och oplanerade underhållskrav (off timmar, eller obekväma platser).
- På nästa nivå, företag praxis _förebyggande underhåll_, där de bestämmer livslängden för en del, och underhålla eller ersätta den innan ett misslyckande. Förebyggande underhåll undviker oplanerade och katastrofala fel. Men de höga kostnaderna för schemalagda driftstopp, underutnyttjande av komponenten under dess användbara livstid, och arbete kvarstår.
- Målet med _prediktivt underhåll_ är att optimera balansen mellan korrigerande och förebyggande underhåll, genom att möjliggöra _precis i tid_ utbyte av komponenter. Den här metoden ersätter endast dessa komponenter när de är nära ett fel. Genom att förlänga komponentens livslängd (jämfört med förebyggande underhåll) och minska oplanerade underhålls- och arbetskostnader (över korrigerande underhåll) kan företag få kostnadsbesparingar och konkurrensfördelar.

## <a name="business-problems-in-pdm"></a>Affärsproblem i PdM
Företag står inför hög operativ risk på grund av oväntade fel och har begränsad insikt i orsaken till problem i komplexa system. Några av de viktigaste affärsfrågorna är:

- Identifiera avvikelser i utrustningens eller systemets prestanda eller funktionalitet.
- Förutsäg om en tillgång kan misslyckas inom en snar framtid.
- Uppskatta en tillgångs återstående nyttjandeperiod.
- Identifiera de främsta orsakerna till fel på en tillgång.
- Identifiera vilka underhållsåtgärder som behöver göras, när, på en tillgång.

Typiska målsatser från PdM är:

- Minska driftrisken för verksamhetskritisk utrustning.
- Öka avkastningen på tillgångar genom att förutsäga fel innan de inträffar.
- Kontrollera underhållskostnaden genom att möjliggöra just-in-time-underhållsåtgärder.
- Lägre kundavgång, förbättra varumärkesimage och förlorad försäljning.
- Lägre lagerkostnader genom att minska lagernivåerna genom att förutsäga beställningspunkten.
- Upptäck mönster som är kopplade till olika underhållsproblem.
- Ange KPI:er (nyckeltal) till exempel hälsopoäng för tillgångsvillkor.
- Uppskatta tillgångarnas återstående livslängd.
- Rekommendera underhållsaktiviteter i tid.
- Aktivera precis i tidslager genom att uppskatta orderdatum för utbyte av delar.

Dessa mål uttalanden är utgångspunkterna för:

- _dataforskare_ för att analysera och lösa specifika prediktiva problem.
- _molnarkitekter och utvecklare_ för att sätta ihop en lösning från på den.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Kvalificerade problem för prediktivt underhåll
Det är viktigt att betona att inte alla användningsfall eller affärsproblem effektivt kan lösas med PdM. Det finns tre viktiga kvalificeringskriterier som måste beaktas vid problemval:

- Problemet måste vara prediktivt till sin natur. det vill säga, det bör finnas ett mål eller ett resultat att förutsäga. Problemet bör också ha en tydlig väg för åtgärder för att förhindra fel när de upptäcks.
- Problemet bör ha ett register över den operativa historien om den utrustning som innehåller _både bra och dåliga resultat_. Den uppsättning åtgärder som vidtas för att minska dåliga resultat bör också vara tillgängliga som en del av dessa poster. Felrapporter, underhållsloggar för prestandaförsämring, reparation och ersättningsloggar är också viktiga. Dessutom reparationer som utförs för att förbättra dem, och ersättningsposter är också användbara.
- Den registrerade historiken bör återspeglas i _relevanta_ uppgifter som är tillräckligt _bra_ för att stödja användningsfallet. Mer information om datareleva och tillräcklighet finns i [Datakrav för prediktivt underhåll](#data-requirements-for-predictive-maintenance).
- Slutligen bör verksamheten ha domän experter som har en klar förståelse för problemet. De bör vara medvetna om de interna processer och metoder för att kunna hjälpa analytikern att förstå och tolka data. De bör också kunna göra nödvändiga ändringar i befintliga affärsprocesser för att vid behov samla in rätt data för problemen.

## <a name="sample-pdm-use-cases"></a>Exempel på pdm-användningsfall
Det här avsnittet fokuserar på en samling pdm-användningsfall från flera branscher som flyg- och rymdindustrin, verktyg och transport. Varje avsnitt börjar med ett affärsproblem och diskuterar fördelarna med PdM, relevanta data kring affärsproblemet och slutligen fördelarna med en PdM-lösning.

| Affärsproblem | Fördelar med PdM |
|:-----------------|-------------------|
|**Flygbranschen**      |                   |
|_Flygförsening och inställda flyg_ på grund av mekaniska problem. Fel som inte kan repareras i tid kan orsaka att flygningar ställs in och störa schemaläggning och åtgärder. |PdM-lösningar kan förutsäga sannolikheten för att ett flygplan försenas eller avbryts på grund av mekaniska fel.|
|_Fel på flygplansmotordelar_: Byte av flygplansmotordel är bland de vanligaste underhållsuppgifterna inom flygindustrin. Underhållslösningar kräver noggrann hantering av komponentlagertillgänglighet, leverans och planering|Att kunna samla in information om komponenttillförlitlighet leder till betydande minskning av investeringskostnaderna.|
|**Ekonomi** |                         |
|_ATM misslyckande_ är ett vanligt problem inom banksektorn. Problemet här är att rapportera sannolikheten för att en bankomat kontantuttag transaktion avbryts på grund av ett pappersstopp eller del fel i uttagsautomaten. Baserat på förutsägelser om transaktionsfel kan bankomater servas proaktivt för att förhindra att fel inträffar.| I stället för att låta datorn misslyckas halvvägs genom en transaktion, är det önskvärda alternativet att programmera datorn att neka service baserat på förutsägelsen.|
|**Energi** |                          |
|_Vindkraftverk misslyckanden:_ Vindkraftverk är den viktigaste energikällan i miljömässigt ansvarsfulla länder / regioner, och innebär höga kapitalkostnader. En viktig komponent i vindkraftverk är generatormotorn, vars fel gör turbinen ineffektiv. Det är också mycket dyrt att fixa.|Att förutsäga KPI:er som MTTF (tid till fel) kan hjälpa energiföretagen att förhindra turbinfel och säkerställa minimal stilleståndstid. Sannolikheter för fel kommer att informera tekniker att övervaka turbiner som sannolikt kommer att misslyckas snart, och schemalägga tidsbaserade underhållssystem. Prediktiva modeller ger insikter i olika faktorer som bidrar till felet, vilket hjälper tekniker att bättre förstå de grundläggande orsakerna till problem.|
|_Fel på kretsbrytaren_: Distribution av el till bostäder och företag kräver att kraftledningar alltid fungerar för att garantera energileverans. Brytare hjälper till att begränsa eller undvika skador på kraftledningar under överbelastning eller ogynnsamma väderförhållanden. Affärsproblemet här är att förutsäga fel på kretsbrytaren.| PdM-lösningar bidrar till att minska reparationskostnaderna och öka livslängden på utrustning som brytare. De bidrar till att förbättra kvaliteten på elnätet genom att minska oväntade fel och avbrott i tjänsten.|
|**Transport och logistik** |    |
|_Hissdörrfel:_ Stora hissföretag ger en full stackservice för miljontals funktionella hissar runt om i världen. Hisssäkerhet, tillförlitlighet och drifttid är de viktigaste frågorna för deras kunder. Dessa företag spårar dessa och olika andra attribut via sensorer, för att hjälpa dem med korrigerande och förebyggande underhåll. I en hiss är det mest framträdande kundproblemet fel på hissdörrar. Affärsproblemet i det här fallet är att tillhandahålla en kunskapsbas prediktiva program som förutsäger de potentiella orsakerna till dörrfel.| Hissar är kapitalinvesteringar för potentiellt en 20-30 års livslängd. Så varje potentiell försäljning kan vara mycket konkurrenskraftig; därför är förväntningarna på service och support höga. Prediktivt underhåll kan ge dessa företag en fördel gentemot sina konkurrenter i sina produkt- och tjänsteerbjudanden.|
|_Hjulfel_: Hjulfel står för hälften av alla tågurspårningar och kostar miljarder till den globala järnvägsindustrin. Hjulfel gör också att rälsen försämras, vilket ibland får skenan att gå sönder i förtid. Järnvägsavbrott leder till katastrofala händelser som urspårningar. För att undvika sådana fall övervakar järnvägarna hjulens prestanda och ersätter dem på ett förebyggande sätt. Affärsproblemet här är förutsägelsen av hjulfel.| Prediktivt underhåll av hjul kommer att hjälpa till med just-in-time byte av hjul |
|_Tunnelbanetåg dörrfel_: En stor orsak till förseningar i tunnelbanan verksamhet är dörrfel av tågvagnar. Affärsproblemet här är att förutsäga tågdörrsfel.|Tidig medvetenhet om en dörr misslyckande, eller antalet dagar tills en dörr misslyckande, kommer att hjälpa verksamheten optimera tåg dörr service scheman.|

Nästa avsnitt får in information om hur man kan förverkliga PdM fördelar diskuteras ovan.

## <a name="data-science-for-predictive-maintenance"></a>Datavetenskap för prediktivt underhåll

Det här avsnittet innehåller allmänna riktlinjer för principer för datavetenskap och praxis för pdm. Den är avsedd att hjälpa en TDM, lösningsarkitekt eller en utvecklare att förstå förutsättningarna och processen för att bygga end-to-end AI-program för PdM. Du kan läsa det här avsnittet tillsammans med en genomgång av demos och proof-of-concept mallar som anges i [Lösningsmallar för prediktivt underhåll](#solution-templates-for-predictive-maintenance). Du kan sedan använda dessa principer och metodtips för att implementera din PdM-lösning i Azure.

> [!NOTE]
> Denna guide är inte avsedd att lära läsaren Data Science. Flera användbara källor tillhandahålls för vidare läsning i avsnittet för [utbildningsresurser för prediktivt underhåll](#training-resources-for-predictive-maintenance). [Lösningsmallarna](#solution-templates-for-predictive-maintenance) som anges i guiden visar några av dessa AI-tekniker för specifika PdM-problem.

## <a name="data-requirements-for-predictive-maintenance"></a>Datakrav för prediktivt underhåll

Framgången för ett lärande beror på (a) kvaliteten på vad som lärs ut, och (b) förmågan hos eleven. Prediktiva modeller lär sig mönster från historiska data och förutsäga framtida utfall med viss sannolikhet baserat på dessa observerade mönster. En modells prediktiva noggrannhet beror på relevansen, tillräckligheten och kvaliteten på tränings- och testdata. De nya data som "poängsättas" med den här modellen bör ha samma funktioner och schema som tränings-/testdata. Funktionsegenskaperna (typ, densitet, distribution och så vidare) för nya data bör matcha de nya tränings- och testdatauppsättningarna. Fokus för detta avsnitt ligger på sådana datakrav.

### <a name="relevant-data"></a>Relevanta uppgifter

För det första måste uppgifterna vara _relevanta för problemet_. Tänk på _det användningsfall för hjulfel_ som beskrivs ovan - träningsdata bör innehålla funktioner som är relaterade till hjuloperationerna. Om problemet var att förutsäga fel i _traktionssystemet,_ utbildningsdata måste omfatta alla olika komponenter för traktionssystemet. Det första fallet är inriktat på en viss komponent medan det andra fallet är inriktat på fel på ett större delsystem. Den allmänna rekommendationen är att utforma förutsägelsesystem om specifika komponenter snarare än större delsystem, eftersom de senare kommer att ha mer spridda data. Domänexperten (se [Kvalificerande problem för prediktivt underhåll)](#qualifying-problems-for-predictive-maintenance)bör hjälpa till att välja de mest relevanta delmängderna av data för analysen. De relevanta datakällorna diskuteras mer i detalj i [Dataförberedelser för prediktivt underhåll](#data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Tillräckliga data
Två frågor ställs ofta när det gäller felhistorikdata: (1) "Hur många felhändelser krävs för att träna en modell?" (2) "Hur många poster anses vara "nog"?" Det finns inga definitiva svar, men bara tumregler. För (1), mer antalet felhändelser, bättre modell. För (2) och det exakta antalet felhändelser beror på vilka data och sambandet för problemet som löses. Men på baksidan, om en maskin misslyckas för ofta då verksamheten kommer att ersätta den, vilket kommer att minska fel instanser. Även här är vägledningen från domänexperten viktig. Det finns dock metoder för att hantera frågan om _sällsynta händelser_. De diskuteras i avsnittet [Hantering av obalanserade data](#handling-imbalanced-data).

### <a name="quality-data"></a>Kvalitetsdata
Datans kvalitet är kritisk - varje prediktorattributvärde måste vara _korrekt_ tillsammans med värdet för målvariabeln. Datakvalitet är ett väl studerat område inom statistik och datahantering, och därmed utanför ramen för denna guide.

> [!NOTE]
> Det finns flera resurser och företagsprodukter för att leverera kvalitetsdata. Nedan finns ett urval av referenser:
> - Dasu T, Johnson, T., Exploratory Data Mining and Data Cleaning, Wiley, 2003.
> - [Erfarenhetsdataanalys, Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, kvantitativ datarengöring för stora databaser](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Introduktion till datarengöring med R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Förberedelse av data för prediktivt underhåll

### <a name="data-sources"></a>Datakällor

De relevanta datakällorna för prediktivt underhåll omfattar, men är inte begränsade till:
- Felhistorik
- Historik för underhåll/reparation
- Maskinens driftsförhållanden
- Metadata för utrustning

#### <a name="failure-history"></a>Felhistorik
Felhändelser är sällsynta i PdM-program. Men när du bygger förutsägelsemodeller måste algoritmen lära sig om en komponents normala driftmönster, liksom dess felmönster. Utbildningsuppgifterna bör därför innehålla tillräckligt många exempel från båda kategorierna. Underhållsposter och reservdelsersättningshistorik är bra källor för att hitta felhändelser. Med hjälp av viss domänkunskap kan avvikelser i träningsdata också definieras som fel.

#### <a name="maintenancerepair-history"></a>Historik för underhåll/reparation
Underhållshistorik för en tillgång innehåller information om komponenter som byts ut, reparationsaktiviteter som utförs etc. Dessa händelser registrerar nedbrytningsmönster. Avsaknad av denna viktiga information i utbildningsdata kan leda till missvisande modellresultat. Felhistorik kan också hittas i underhållshistoriken som särskilda felkoder eller orderdatum för delar. Ytterligare datakällor som påverkar felmönster bör undersökas och tillhandahållas av domänexperter.

#### <a name="machine-operating-conditions"></a>Maskinens driftsförhållanden
Sensorbaserade (eller andra) strömmande data för den utrustning som är i drift är en viktig datakälla. Ett viktigt antagande i PdM är att en maskins hälsotillstånd försämras med tiden under dess rutinoperation. Data förväntas innehålla tidsvarierande funktioner som fångar det här åldersmönstret och eventuella avvikelser som leder till nedbrytning. Den tidsmässiga aspekten av data krävs för att algoritmen ska lära sig fel- och icke-felmönster över tiden. Baserat på dessa datapunkter lär sig algoritmen att förutsäga hur många fler tidsenheter en maskin kan fortsätta att fungera innan den misslyckas.

#### <a name="static-feature-data"></a>Statiska funktionsdata
Statiska funktioner är metadata om utrustningen. Exempel är utrustningens märke, modell, tillverkningsdatum, startdatum för tjänsten, placering av systemet och andra tekniska specifikationer.

Exempel på relevanta data för [exempelformaten PdM](#sample-pdm-use-cases) är tabellformade nedan:

| Användningsfall | Exempel på relevanta uppgifter |
|:---------|---------------------------|
|_Flygförseningar och inställda flyg_ | Flygruttsinformation i form av flygben och sidloggar. Flygbensdata omfattar ruttinformation såsom avgångs-/ankomstdatum, tid, flygplats, layovers etc. Sidloggen innehåller en serie fel- och underhållskoder som registrerats av markunderhållspersonalen.|
|_Fel på flygplansmotordelar_ | Data som samlats in från sensorer i flygplanet som ger information om de olika delarnas skick. Underhållsposter hjälper till att identifiera när komponentfel inträffade och när de ersattes.|
|_ATM-fel_ | Sensoravläsningar för varje transaktion (insättning av kontanter/check) och utbetalning av kontanter. Information om gap mätning mellan anteckningar, notera tjocklek, notera ankomst avstånd, kontrollera attribut etc. Underhållsposter som tillhandahåller felkoder, reparationsinformation, sista gången uttagsautomaten fylldes på.|
|_Vindkraftverk misslyckande_ | Sensorer övervakar turbinförhållanden som temperatur, vindriktning, genererad effekt, generatorhastighet etc. Data samlas in från flera vindkraftverk från vindkraftparker i olika regioner. Vanligtvis kommer varje turbin har flera sensoravläsningar återutläggning mätningar med ett fast tidsintervall.|
|_Fel på kretsbrytaren_ | Underhållsloggar som innehåller korrigerande, förebyggande och systematiska åtgärder. Driftdata som innehåller automatiska och manuella kommandon som skickas till kretsbrytare, till exempel för öppna och nära åtgärder. Enhetsmetadata som tillverkningsdatum, plats, modell, etc. Specifikationer för kretsbrytare såsom spänningsnivåer, geolokalisering, omgivningsförhållanden.|
|_Hissdörrfel_| Hissmetadata som typ av hiss, tillverkningsdatum, underhållsfrekvens, byggnadstyp och så vidare. Operativ information såsom antal dörrcykler, genomsnittlig dörrstj.på. Felhistorik med orsaker.|
|_Hjulfel_ | Sensordata som mäter hjulacceleration, bromsförekomster, körsträcka, hastighet etc. Statisk information på hjul som tillverkare, tillverkat datum. Feldata härleddes från delorderdatabas som spårar orderdatum och kvantiteter.|
|_Fel på tunnelbanetågsdörren_ | Dörröppnings- och stängningstider, andra driftsdata såsom tågdörrars aktuella skick. Statiska data skulle innehålla kolumner för tillgångsidentifierare, tid och villkorsvärde.|

### <a name="data-types"></a>Datatyper
Med tanke på ovanstående datakällor är de två viktigaste datatyperna som observerats i PdM-domänen:

- _Temporaldata_: Drifttelemetri, maskinförhållanden, arbetsordertyper, prioritetskoder som har tidsstämplar vid tidpunkten för registreringen. Fel, underhåll/reparation och användningshistorik har också tidsstämplar som är associerade med varje händelse.
- _Statiska data:_ Maskinfunktioner och operatörsfunktioner i allmänhet är statiska eftersom de beskriver de tekniska specifikationerna för maskiner eller operatörsattribut. Om dessa funktioner kan ändras med tiden bör de också ha tidsstämplar associerade med dem.

Prediktor- och målvariabler bör förbearbetas/omvandlas till [numeriska, kategoriska och andra datatyper](https://www.statsdirect.com/help/basics/measurement_scales.htm) beroende på vilken algoritm som används.

### <a name="data-preprocessing"></a>Förbehandling av data
Som en förutsättning för _funktionsteknik_kan du förbereda data från olika strömmar för att skapa ett schema från vilket det är enkelt att skapa funktioner. Visualisera data först som en postförteckning. Varje rad i tabellen representerar en träningsförekomst och kolumnerna representerar _prediktorfunktioner_ (kallas även oberoende attribut eller variabler). Ordna data så att den sista kolumnen/kolumnerna är _målet_ (beroende variabel). För varje träningsinstans tilldelar du en _etikett_ som värdet för den här kolumnen.

För tidsdata delar du upp sensordatas varaktighet i tidsenheterna. Varje post ska tillhöra en tidsenhet för en tillgång _och erbjuda tydlig information_. Tidsenheter definieras baserat på affärsbehov i flera sekunder, minuter, timmar, dagar, månader och så vidare. _Tidsenheten behöver inte vara densamma som datainsamlingsfrekvensen_. Om frekvensen är hög kan det hända att data inte visar någon signifikant skillnad från den ena enheten till den andra. Anta till exempel att omgivningstemperaturen samlades in var 10:e sekund. Om du använder samma intervall för träningsdata ökar bara antalet exempel utan att ge någon ytterligare information. I det här fallet skulle en bättre strategi vara att använda medelvärdet av data över 10 minuter, eller en timme baserat på affärsmotiveringen.

För statiska data
- _Underhållsposter_: Råunderhållsdata har en tillgångsidentifierare och tidsstämpel med information om underhållsaktiviteter som har utförts vid en viss tidpunkt. Omvandla underhållsaktiviteter till _kategoriska_ kolumner, där varje kategoribeskrivning unikt mappar till en viss underhållsåtgärd. Schemat för underhållsposter skulle innehålla åtgärder för tillgångsidentifierare, tid och underhåll.

- _Felposter_: Fel- eller felorsaker kan registreras som specifika felkoder eller felhändelser som definieras av specifika affärsvillkor. I de fall utrustningen har flera felkoder bör domänexperten hjälpa till att identifiera de som är relevanta för målvariabeln. Använd de återstående felkoderna eller villkoren för att konstruera _prediktorfunktioner_ som korrelerar med dessa fel. Schemat för felposter skulle innehålla tillgångsidentifierare, tid, fel eller felorsak - om tillgängligt.

- _Metadata för datorer och operatör:_ Slå samman dator- och operatörsdata till ett schema för att associera en tillgång med dess operatör, tillsammans med deras respektive attribut. Schemat för datorvillkor skulle omfatta tillgångsidentifierare, tillgångsfunktioner, operatörsidentifierare och operatörsfunktioner.

Andra förbearbetningssteg för data omfattar _hantering av saknade värden_ och _normalisering_ av attributvärden. En detaljerad diskussion ligger utanför den här handbokens räckvidd - se nästa avsnitt för några användbara referenser.

Med ovanstående förbearbetade datakällor på plats är den slutliga omvandlingen innan funktionsteknik att ansluta till ovanstående tabeller baserat på tillgångsidentifieraren och tidsstämpeln. Den resulterande tabellen skulle ha nollvärden för felkolumnen när datorn är i normal drift. Dessa null-värden kan imputeras av en indikator för normal drift. Använd den här felkolumnen om du vill skapa _etiketter för den prediktiva modellen_. Mer information finns i avsnittet om [modelleringstekniker för prediktivt underhåll](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Funktionstekniker
Funktionsteknik är det första steget innan du modellerar data. Dess roll i datavetenskapsprocessen [beskrivs här](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). En _funktion_ är ett prediktivt attribut för modellen - till exempel temperatur, tryck, vibrationer och så vidare. För PdM innebär funktionsteknik att en maskins hälsa kan tas med över historiska data som samlats in under en ansenlig varaktighet. I den meningen skiljer det sig från sina peer-datorer som fjärrövervakning, avvikelseidentifiering och felidentifiering. 

### <a name="time-windows"></a>Tidsfönster
Fjärrövervakning innebär rapportering av händelser som inträffar _från och med tidpunkter_. Avvikelseidentifieringsmodeller utvärderar (poäng) inkommande dataströmmar för att flagga avvikelser från och med tidpunkter. Felidentifiering klassificerar fel som ska vara av specifika typer när de inträffar tidpunkter. PdM innebär däremot att fel förutspås under en _framtida tidsperiod_, baserat på funktioner som representerar maskinbeteende över _historisk tidsperiod_. För PdM är funktionsdata från enskilda tidpunkter för bullriga för att vara prediktiva. Så data för varje funktion måste _jämnas ut_ genom att samla datapunkter över tidsfönster.

### <a name="lag-features"></a>Lag funktioner
Affärskraven definierar hur långt modellen har att förutsäga i framtiden. I sin tur hjälper denna varaktighet definiera "hur långt tillbaka modellen måste se" för att göra dessa förutsägelser. Denna "ser tillbaka" period kallas _eftersläpning_, och funktioner konstruerade under denna fördröjning kallas _eftersläpning funktioner_. I det här avsnittet beskrivs fördröjningsfunktioner som kan konstrueras från datakällor med tidsstämplar och funktionsskapande från statiska datakällor. Lag funktioner är vanligtvis _numeriska_ karaktär.

> [!IMPORTANT]
> Fönstrets storlek bestäms via experiment, och bör slutföras med hjälp av en domän expert. Samma varning gäller för val och definition av fördröjningsfunktioner, deras aggregeringar och typ av fönster.

#### <a name="rolling-aggregates"></a>Rullande aggregat
För varje post för en tillgång väljs ett rullande fönster med storlek "W" som antalet tidsenheter för att beräkna aggregaten. Lag-funktioner beräknas sedan med W-perioderna _före datumet för posten._ I figur 1 visar de blå linjerna sensorvärden som registrerats för en tillgång för varje tidsenhet. De anger ett rullande medelvärde av funktionsvärden över ett fönster med storlek W=3. Det rullande medelvärdet beräknas över alla poster med tidsstämplar i intervallet t<sub>1</sub> (i orange) till t<sub>2</sub> (i grönt). Värdet för W är vanligtvis i minuter eller timmar beroende på vilken typ av data. Men för vissa problem, plocka en stor W (säg 12 månader) kan ge hela historien om en tillgång fram till tidpunkten för posten.

![Bild 1. Rullande aggregatfunktioner](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Bild 1. Rullande aggregatfunktioner

Exempel på rullande aggregat över ett tidsfönster är antal, medelvärde, CUMESUM (kumulativ summa) mått, min/max-värden. Dessutom används ofta varians, standardavvikelse och antal extremvärden utöver N-standardavvikelser. Exempel på aggregat som kan användas för [användningsfallen](#sample-pdm-use-cases) i den här guiden visas nedan. 
- _Flygförsening_: antal felkoder under den senaste dagen/veckan.
- _Fel på flygplansmotordel:_ rullande medel, standardavvikelse och summa under den senaste dagen, veckan etc. Det här måttet bör bestämmas tillsammans med experten för affärsdomäner.
- _ATM-fel:_ rullande medel, median, intervall, standardavvikelser, antal extremvärden utöver tre standardavvikelser, övre och nedre CUMESUM.
- _Subway tåg dörr misslyckanden:_ Räkna av händelser under senaste dagen, vecka, två veckor etc.
- _Felbrytare_: Fel räknas under den senaste veckan, år, tre år etc.

En annan användbar teknik i PdM är att fånga trendförändringar, toppar och nivåändringar med hjälp av algoritmer som identifierar avvikelser i data.

#### <a name="tumbling-aggregates"></a>Tumlande aggregat
För varje märkt post för en tillgång definieras ett fönster med storlek _W-<sub>k,</sub> _ där _k_ är antalet fönster av storlek _W_. Aggregat skapas sedan över _k_ _tumlande fönster_ _W-k, W-<sub>(k-1)</sub>, ..., W-<sub>2</sub>, W-<sub>1</sub> _ för perioderna före en post tidsstämpel. _k_ kan vara ett litet antal för att fånga kortsiktiga effekter, eller ett stort antal för att fånga långsiktiga nedbrytningsmönster. (se figur 2).

![Figur 2. Fallande aggregatfunktioner](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Figur 2. Fallande aggregatfunktioner

Till exempel kan fördröjningsfunktioner för vindkraftverkens användningsfall skapas med W=1 och k=3. De innebär eftersläpning för var och en av de senaste tre månaderna med topp och botten extremvärden.

### <a name="static-features"></a>Statiska funktioner

Tekniska specifikationer för utrustningen såsom tillverkningsdatum, modellnummer, plats, är några exempel på statiska funktioner. De behandlas som _kategoriska_ variabler för modellering. Några exempel på kretsbrytarnytte är spänning, ström, effektkapacitet, transformatortyp och strömkälla. För hjulfel är typen av däckhjul (legering vs stål) ett exempel.

De insatser för förberedelse av uppgifter som hittills diskuterats bör leda till att uppgifterna organiseras enligt nedan. Tränings-, test- och valideringsdata bör ha det här logiska schemat (det här exemplet visar tid i enheter av dagar).

| Tillgångs-ID | Tid | \<Funktionskolumner> | Label (Etikett) |
| ---- | ---- | --- | --- |
| A123 (på andra sätt) |Dag 1 | . . . | . |
| A123 (på andra sätt) |Dag 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 (på andra sätt) |Dag 1 | . . . | . |
| B234 (på andra sätt) |Dag 2 | . . . | . |
| ...  |...   | . . . | . |

Det sista steget i funktionsteknik är **märkningen** av målvariabeln. Den här processen är beroende av modelleringstekniken. Modelleringstekniken beror i sin tur på affärsproblemet och arten av tillgängliga data. Märkning beskrivs i nästa avsnitt.

> [!IMPORTANT]
> Databeredning och funktionsteknik är lika viktiga som modelleringstekniker för att komma fram till framgångsrika PdM-lösningar. Domänexperten och utövaren bör ägna sig åt att komma fram till rätt funktioner och data för modellen. Ett litet urval från många böcker om funktionsteknik listas nedan:
> - Pyle, D. Data förberedelse för data mining (Morgan Kaufmann-serien i datahanteringssystem), 1999
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principer och tekniker för dataforskare, O'Reilly, 2018.
> - Dong, G. Liu, H. (redaktörer), Funktionsteknik för maskininlärning och dataanalys (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Modelleringstekniker för prediktivt underhåll

I det här avsnittet beskrivs de viktigaste modelleringsteknikerna för PdM-problem, tillsammans med deras specifika etikettkonstruktionsmetoder. Observera att en enda modelleringsteknik kan användas i olika branscher. Modelleringstekniken är kopplad till datascience-problemet, snarare än sammanhanget för de senaste uppgifterna.

> [!IMPORTANT]
> Valet av etiketter för felfall och märkningsstrategin  
> bör fastställas i samråd med domänexperten.

### <a name="binary-classification"></a>Binär klassificering
Binär klassificering används för att _förutsäga sannolikheten för att en utrustning misslyckas inom en framtida tidsperiod_ - kallad den framtida _horisontperioden X_. X bestäms av affärsproblemet och de senaste uppgifterna, i samråd med domänexperten. Några exempel:
- _Minsta ledtid_ som krävs för att ersätta komponenter, distribuera underhållsresurser, utföra underhåll för att undvika ett problem som sannolikt kommer att uppstå under den perioden.
- _minsta antal händelser_ som kan inträffa innan ett problem uppstår.

I denna teknik identifieras två typer av träningsexempel. Ett positivt _exempel, som indikerar ett fel_, med etikett = 1. Ett negativt exempel, som anger normala operationer, med etikett = 0. Målvariabeln, och därmed etikettvärdena, är _kategoriska_. Modellen bör identifiera varje nytt exempel som kan misslyckas eller fungera normalt under nästa X-tidsenheter.

#### <a name="label-construction-for-binary-classification"></a>Etikettkonstruktion för binär klassificering
Frågan här är: "Vad är sannolikheten att tillgången kommer att misslyckas i nästa X-enheter i tid?" För att besvara den här frågan, etikett X poster före fel på en tillgång som "på väg att misslyckas" (etikett = 1), och etikett alla andra poster som "normal" (etikett = 0). (se figur 3).

![Bild 3. Märkning för binär klassificering](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Bild 3. Märkning för binär klassificering

Exempel på märkningsstrategi för vissa användningsfall visas nedan.
- _Flygförseningar_: X kan väljas som en dag för att förutsäga förseningar inom de närmaste 24 timmarna. Då alla flygningar som är inom 24 timmar innan misslyckanden är märkta som 1.
- _ATM cash dispense fel_: Ett mål kan vara att fastställa fel sannolikhet för en transaktion inom en timme. I så fall är alla transaktioner som inträffade under den senaste timmen av felet märkta som 1. För att förutsäga sannolikhet för fel under nästa N-sedlar som lämnas ut, är alla anteckningar som lämnas ut i de senaste N-anteckningarna för ett fel märkta som 1.
- _Fel på kretsbrytaren_: Målet kan vara att förutsäga nästa fel på kretsbrytarens kommandofel. I så fall väljs X till ett framtida kommando.
- _Tågdörrsfel_: X kan väljas som två dagar.
- _Vindkraftverk fel:_ X kan väljas som två månader.

### <a name="regression-for-predictive-maintenance"></a>Regression för prediktivt underhåll
Regressionsmodeller används för att _beräkna den återstående nyttjandetiden (RUL) för en tillgång_. RUL definieras som den tid som en tillgång är i drift innan nästa fel inträffar. Varje utbildningsexempel är en post som tillhör en tidsenhet _nY_ för en tillgång, där _n_ är multipeln. Modellen ska beräkna RUL för varje nytt exempel som ett _kontinuerligt tal_. Det här numret anger den tid som återstår före felet.

#### <a name="label-construction-for-regression"></a>Etikettkonstruktion för regression
Frågan här är: "Vad är den återstående nyttjandetiden (RUL) av utrustningen?" För varje post före felet beräknar du etiketten så att den är antalet enheter som återstår före nästa fel. I den här metoden är etiketter kontinuerliga variabler. (Se bild 4)

![Bild 4. Märkning för regression](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Bild 4. Märkning för regression

För regression görs märkning med hänvisning till en felpunkt. Dess beräkning är inte möjlig utan att veta hur länge tillgången har överlevt före ett misslyckande. Så i motsats till binär klassificering, tillgångar utan fel i data kan inte användas för modellering. Denna fråga är bäst behandlas av en annan statistisk teknik som kallas [Survival Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Men potentiella komplikationer kan uppstå vid tillämpning av denna teknik till PdM användningsfall som innebär tidsvarierande data med täta intervall. Mer information om överlevnadsanalys finns i [den här ensökaren](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klassificering av flera klasser för prediktivt underhåll
Klassificeringstekniker i flera klasser kan användas i PdM-lösningar för två scenarier:
- _Förutsäg två framtida utfall:_ Det första resultatet är _en rad olika tid att misslyckas_ för en tillgång. Tillgången tilldelas en av flera möjliga tidsperioder. Det andra resultatet är sannolikheten för fel i en framtida period på grund _av en av de många grundorsakerna_. Denna förutsägelse gör det möjligt för underhållspersonalen att hålla utkik efter symptom och planera underhållsscheman.
- _Förutsäg den mest sannolika orsaken till_ ett visst fel. Det här resultatet rekommenderar rätt uppsättning underhållsåtgärder för att åtgärda ett fel. En rangordnad lista över grundorsaker och rekommenderade reparationer kan hjälpa tekniker att prioritera sina reparationsåtgärder efter ett fel.

#### <a name="label-construction-for-multi-class-classification"></a>Etikettkonstruktion för klassificering av flera klasser
Frågan här är: "Vad är sannolikheten för att en tillgång kommer att misslyckas i nästa _nZ_ enheter tid där _n_ är antalet perioder?" För att besvara denna fråga, etikett nZ poster innan fel på en tillgång med hjälp av hinkar med tid (3Z, 2Z, Z). Märk alla andra poster som "normala" (etikett = 0). I den här metoden innehåller målvariabeln _kategoriska_ värden. (se figur 5).

![Bild 5. Förutsägelseetiketter för feltid för klassificering av fleraklasser](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Bild 5. Märkning för klassificering av flera klasser för feltidsförutsägels

Frågan här är: "Vad är sannolikheten att tillgången kommer att misslyckas i nästa X-enheter tid på grund av grundorsak / problem _P<sub>i?"</sub>_ där _jag_ är antalet möjliga bakomliggande orsaker. För att besvara denna fråga, etikett X poster före misslyckandet av en tillgång som "om att misslyckas på grund av grundorsak _P<sub>i</sub>_" (etikett = _P<sub>i</sub>_). Märk alla andra poster som "normala" (etikett = 0). I denna metod är etiketterna också kategoriska (se figur 6).

![Bild 6. Förutsägelseetiketter för grundorsak för klassificering av fleraklasser](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Bild 6. Märkning för klassificering av flera klasser för förutsägelse av grundorsak

Modellen tilldelar en misslyckandesannolikhet på grund av varje _P<sub>i</sub> _ samt sannolikheten för inget fel. Dessa sannolikheter kan beställas efter storlek för att möjliggöra förutsägelse av de problem som är mest sannolikt att uppstå i framtiden.

Frågan här är: "Vilka underhållsåtgärder rekommenderar ni efter ett fel?" För att besvara den här frågan kräver märkning _inte en framtida horisont som ska väljas,_ eftersom modellen inte förutspår fel i framtiden. Det är bara förutsäga den mest sannolika orsaken _när felet redan har hänt_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Utbildning, validering och testmetoder för prediktivt underhåll
[Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) ger en fullständig täckning av modellen tåg-test-validera cykel. I det här avsnittet beskrivs aspekter som är unika för PdM.

### <a name="cross-validation"></a>Korsvalidering
Målet med [korsvalidering](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) är att definiera en datauppsättning för att "testa" modellen i utbildningsfasen. Den här datauppsättningen kallas _valideringsuppsättningen_. Den här tekniken hjälper till att begränsa problem som _övermontering_ och ger en inblick i hur modellen kommer att generalisera till en oberoende datauppsättning. Det vill säga en okänd datauppsättning, som kan vara från ett verkligt problem. Utbildningen och testning rutin för PdM måste ta hänsyn till den tid varierande aspekter för att bättre generalisera på osynliga framtida data.

Många maskininlärningsalgoritmer är beroende av ett antal hyperparametrar som kan ändra modellens prestanda avsevärt. De optimala värdena för dessa hyperparametrar beräknas inte automatiskt när modellen tränas. De bör specificeras av dataforskaren. Det finns flera sätt att hitta bra värden av hyperparametrar.

Den vanligaste är _k-fold cross-validering_ som delar exemplen slumpmässigt i _k_ veck. För varje uppsättning hyperparametrarvärden kör du inlärningsalgoritmen _k_ gånger. Vid varje iteration använder du exemplen i den aktuella vecket som en valideringsuppsättning och resten av exemplen som en träningsuppsättning. Träna algoritmen över utbildningsexemplen och beräkna prestandamåtten över valideringsexempel. I slutet av den här loopen beräknar du medelvärdet för k-prestandamått. _k_ För varje uppsättning hyperparametervärden väljer du de som har bäst medelprestanda. Uppgiften att välja hyperparametrar är ofta experimentell till sin natur.

I PdM-problem registreras data som en tidsserie över händelser som kommer från flera datakällor. Dessa register kan beställas efter tidpunkten för märkningen. Om datauppsättningen delas upp _slumpmässigt_ i utbildnings- och valideringsuppsättningen _kan vissa träningsexemplen därför vara senare i tid än några av valideringsexempel ._ Framtida prestanda för hyperparametervärden kommer att uppskattas baserat på vissa data som kom _innan_ modellen tränades. Dessa uppskattningar kan vara alltför optimistiska, särskilt om tidsserien inte är stationär och utvecklas med tiden. Som ett resultat kan de valda hyperparametervärdena vara suboptimala.

Det rekommenderade sättet är att dela upp exemplen i utbildnings- och valideringsuppsättningen på ett _tidsberoende_ sätt, där alla valideringsexempel är senare i tiden än alla utbildningsexempel. För varje uppsättning hyperparametervärden tränar du algoritmen över träningsdatauppsättningen. Mät modellens prestanda över samma valideringsuppsättning. Välj hyperparametervärden som visar bästa prestanda. Hyperparametervärden som väljs av splitt för tåg/validering resulterar i bättre framtida modellprestanda än med de värden som väljs slumpmässigt genom korsvalidering.

Den slutliga modellen kan genereras genom att träna en inlärningsalgoritm över hela träningsdata med hjälp av de bästa hyperparametervärdena.

### <a name="testing-for-model-performance"></a>Testning för modellprestanda
När en modell har skapats krävs en uppskattning av dess framtida prestanda på nya data. En bra uppskattning är prestandamåttet för hyperparametervärden som beräknats under valideringsuppsättningen, eller ett genomsnittligt prestandamått som beräknas från korsvalidering. Dessa uppskattningar är ofta alltför optimistiska. Verksamheten kan ofta ha några ytterligare riktlinjer för hur de skulle vilja testa modellen.

Det rekommenderade sättet för PdM är att dela upp exemplen i utbildnings-, validerings- och testdatauppsättningar på ett _tidsberoende_ sätt. Alla testexempel bör vara senare i tid än alla exempel på utbildning och validering. Efter delningen genererar du modellen och mäter dess prestanda enligt beskrivningen ovan.

När tidsserierna är stationära och lätta att förutsäga genererar både slumpmässiga och tidsberoende metoder liknande uppskattningar av framtida prestanda. Men när tidsserierna är icke-stationära och/eller svåra att förutsäga kommer den tidsberoende metoden att generera mer realistiska uppskattningar av framtida resultat.

### <a name="time-dependent-split"></a>Tidsberoende delning
I det här avsnittet beskrivs metodtips för att implementera tidsberoende delning. En tidsberoende tvåvägsuppdelning mellan tränings- och testuppsättningar beskrivs nedan.

Anta en ström av tidsstämplade händelser, till exempel mätningar från olika sensorer. Definiera funktioner och etiketter för utbildning och testexempel över tidsramar som innehåller flera händelser. För binär klassificering, skapa funktioner baserat på tidigare händelser och skapa etiketter baserat på framtida händelser inom "X" tidsenheter i framtiden (se avsnitten om [funktionsteknik](#feature-engineering) och modelleringstekniker). Således kommer märkning tidsramen för ett exempel senare än tidsramen för dess funktioner.

För tidsberoende split väljer du en _träningsavstjusningstid T<sub>c</sub> _ där en modell kan tränas, med hyperparametrar inställda med historiska data upp till T<sub>c</sub>. För att förhindra läckage av framtida etiketter som ligger utanför T<sub>c</sub> i träningsdata väljer du den senaste tiden för att märka utbildningsexempel till X-enheter före T<sub>c</sub>. I exemplet som visas i figur 7 representerar varje ruta en post i datauppsättningen där funktioner och etiketter beräknas enligt beskrivningen ovan. Figuren visar de poster som ska gå till tränings- och testuppsättningar för X=2 och W=3:

![Bild 7. Tidsberoende delning för binär klassificering](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Bild 7. Tidsberoende delning för binär klassificering

De gröna rutorna representerar poster som hör till de tidsenheter som kan användas för utbildning. Varje utbildningsexempel genereras genom att hänsyn tas till de tre senaste perioderna för funktionsgenerering och två framtida perioder för märkning före T<sub>c</sub>. När någon del av de två framtida perioderna ligger utanför T<sub>c</sub>utesluter du det exemplet från utbildningsdatauppsättningen eftersom ingen synlighet antas utöver T<sub>c</sub>.

De svarta rutorna representerar posterna för den slutliga märkta datauppsättningen som inte ska användas i utbildningsdatauppsättningen, med tanke på ovanstående begränsning. Dessa poster kommer inte heller att användas i testdata, eftersom de är före T<sub>c</sub>. Dessutom beror deras märkning tidsramar delvis på träningstid, vilket inte är idealiskt. Tränings- och testdata bör ha separata tidsramar för märkning för att förhindra läckage av etikettinformation.

Den teknik som hittills diskuterats möjliggör överlappning mellan utbildning och testning exempel som har tidsstämplar nära T<sub>c</sub>. En lösning för att uppnå större separation är att utesluta exempel som finns inom W-tidsenheter av T<sub>c</sub> från testuppsättningen. Men en sådan aggressiv uppdelning beror på gott om datatillgänglighet.

Regressionsmodeller som används för att förutsäga RUL påverkas allvarligare av läckageproblemet. Att använda den slumpmässiga delningsmetoden leder till extrem övermontering. För regressionsproblem bör uppdelningen vara sådan att de poster som tillhör tillgångar med fel innan T<sub>c</sub> går in i utbildningsuppsättningen. Register över tillgångar som har fel efter brytpunkten går in i testuppsättningen.

En annan bästa praxis för att dela upp data för utbildning och testning är att använda en uppdelning efter tillgångs-ID. Uppdelningen bör vara sådan att ingen av de tillgångar som används i utbildningsuppsättningen används för att testa modellens prestanda. Med hjälp av detta tillvägagångssätt har en modell en bättre chans att ge mer realistiska resultat med nya tillgångar.

### <a name="handling-imbalanced-data"></a>Hantering av obalanserade data
I klassificeringsproblem, om det finns fler exempel på en klass än för de andra, sägs datauppsättningen vara _obalanserad_. Helst föredras tillräckligt många representanter för varje klass i träningsdata för att möjliggöra differentiering mellan olika klasser. Om en klass är mindre än 10 % av uppgifterna anses uppgifterna vara obalanserade. Den underrepresenterade klassen kallas _minoritetsklass_.

Många PdM-problem står inför sådana obalanserade datauppsättningar, där en klass är kraftigt underrepresenterad jämfört med den andra klassen, eller klasser. I vissa situationer får minoritetsklassen endast utgöra 0,001 % av de totala datapunkterna. Klassobalans är inte unikt för PdM. Andra domäner där fel och avvikelser är sällsynta händelser står inför ett liknande problem, till exempel bedrägeriidentifiering och nätverksintrång. Dessa misslyckanden utgör exempel på minoritetsklass.

Med klassobalans i data äventyras prestanda för de flesta standardinlärningsalgoritmer, eftersom de syftar till att minimera den totala felfrekvensen. För en datauppsättning med 99 % negativa och 1 % positiva exempel kan en modell visas ha 99 % noggrannhet genom att alla instanser betecknas som negativa. Men modellen kommer att felaktigt klassificera alla positiva exempel; så även om dess noggrannhet är hög, är algoritmen inte en användbar. Följaktligen är konventionella utvärderingsmått som _övergripande noggrannhet på felprocenten_ otillräckliga för obalanserad inlärning. När andra mått ställs inför obalanserade datauppsättningar används de för modellutvärdering:
- Precision
- Återkalla
- F1-poäng
- Kostnadsjusterad ROC (mottagarens driftsegenskaper)

Mer information om dessa mått finns i [modellutvärdering](#model-evaluation).

Det finns dock några metoder som hjälper till att åtgärda problemet med klasobalans. De två stora är _provtagningstekniker_ och _kostnadskänsligt lärande._

#### <a name="sampling-methods"></a>Provtagningsmetoder
Obalanserad inlärning innebär användning av provtagningsmetoder för att ändra utbildningsdatauppsättningen till en balanserad datauppsättning. Provtagningsmetoder skall inte användas på testuppsättningen. Även om det finns flera provtagningstekniker, de flesta rakt fram är _slumpmässiga översampling_ och _under provtagning_.

_Slumpmässig översampling_ innebär att välja ett slumpmässigt urval från minoritetsklass, replikera dessa exempel och lägga till dem i utbildningsdatauppsättningen. Följaktligen ökar antalet exempel i minoritetsklass och balanserar så småningom antalet exempel på olika klasser. En nackdel med översampling är att flera instanser av vissa exempel kan orsaka att klassificeraren blir för specifik, vilket leder till övermontering. Modellen kan visa hög träningsnoggrannhet, men dess prestanda på osynliga testdata kan vara suboptimala.

Omvänt är _slumpmässigt under sampling_ att välja ett slumpmässigt urval från en majoritetsklass och ta bort dessa exempel från träningsdatauppsättningen. Om du tar bort exempel från majoritetsklassen kan det dock leda till att klassificeraren missar viktiga begrepp som rör majoritetsklassen. _Hybridprovtagning_ där minoritetsklassen är översmakad och majoritetsklassen undersmaka samtidigt är ett annat genomförbart tillvägagångssätt.

Det finns många sofistikerade provtagningstekniker. Vilken teknik som väljs beror på dataegenskaperna och resultaten av iterativa experiment av dataforskaren.

#### <a name="cost-sensitive-learning"></a>Kostnadskänslig inlärning
I PdM är misslyckanden som utgör minoritetsklassen av större intresse än normala exempel. Så fokus ligger främst på algoritmens prestanda på fel. Felaktigt förutsäga en positiv klass som en negativ klass kan kosta mer än vice versa. Denna situation kallas vanligen ojämlik förlust eller asymmetrisk kostnad för felklassande element till olika klasser. Den idealiska klassificeraren bör leverera hög förutsägelsenoggrannhet över minoritetsklassen, utan att kompromissa med noggrannheten för majoritetsklassen.

Det finns flera sätt att uppnå denna balans. Om du vill minska problemet med ojämlik förlust tilldelar du en hög kostnad till felklassificering av minoritetsklassen och försöker minimera den totala kostnaden. Algoritmer som _SVMs (Support Vector Machines)_ antar den här metoden i sig genom att låta kostnaden för positiva och negativa exempel anges under utbildningen. På samma sätt visar ökade metoder som _förstärkta beslutsträd_ vanligtvis goda resultat med obalanserade data.

## <a name="model-evaluation"></a>Modellutvärdering
Felaktig klassificering är ett betydande problem för PdM-scenarier där kostnaden för falsklarm till verksamheten är hög. Ett beslut att jorda ett flygplan baserat på en felaktig förutsägelse av motorbortfall kan till exempel störa tidtabeller och resplaner. Om du tar en maskin offline från en monteringslinje kan det leda till intäktsförluster. Så modellutvärdering med rätt prestandamått mot nya testdata är avgörande.

Typiska prestandamått som används för att utvärdera PdM-modeller beskrivs nedan:

- [Noggrannhet](https://en.wikipedia.org/wiki/Accuracy_and_precision) är det mest populära måttet som används för att beskriva en klassificerares prestanda. Men noggrannheten är känslig för datadistributioner och är ett ineffektivt mått för scenarier med obalanserade datauppsättningar. Andra mått används i stället. Verktyg som [förvirring matris](https://en.wikipedia.org/wiki/Confusion_matrix) används för att beräkna och resonera om riktigheten av modellen.
- [Precisionen](https://en.wikipedia.org/wiki/Precision_and_recall) i PdM-modeller relaterar till graden av falsklarm. Lägre precision i modellen motsvarar i allmänhet en högre frekvens av falsklarm.
- [Återkallningsfrekvensen](https://en.wikipedia.org/wiki/Precision_and_recall) anger hur många av felen i testuppsättningen som identifierades korrekt av modellen. Högre återkallningsfrekvens innebär att modellen lyckas identifiera de verkliga felen.
- [F1 poäng](https://en.wikipedia.org/wiki/F1_score) är det harmoniska genomsnittet av precision och återkallande, med dess värde mellan 0 (värsta) till 1 (bäst).

För binär klassificering,
- [Mottagare driftskurvor (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) är också ett populärt mått. I ROC-kurvor tolkas modellens prestanda baserat på en fast driftpunkt på ROC.
- Men för PdM problem, _decil tabeller_ och _diagram lyft_ är mer informativ. De fokuserar bara på den positiva klassen (fel) och ger en mer komplex bild av algoritmprestanda än ROC-kurvor.
  - _Deciltabeller_ skapas med hjälp av testexempel i en fallande ordning av sannolikheter för fel. De beställda proverna grupperas sedan i deciler (10% av proverna med högsta sannolikhet, sedan 20%, 30%, och så vidare). Förhållandet (sann positiv frekvens)/(slumpmässig baslinje) för varje decil hjälper till att uppskatta algoritmprestanda vid varje decil. Den slumpmässiga baslinjen tar på värdena 0.1, 0.2 och så vidare.
  - [Lyft diagram](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) rita decilen sann positiv ränta kontra slumpmässiga verkliga positiva kurs för alla deciler. De första decilerna är oftast i fokus för resultaten, eftersom de visar de största vinsterna. Första deciler kan också ses som representativa för "i riskzonen", när de används för PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Modell operationalisering för prediktivt underhåll

Fördelen som datavetenskapen övar realiseras endast, när den utbildade modellerar göras fungerande. Det vill säga, modellen måste distribueras i affärssystemen för att göra förutsägelser baserat på nya, tidigare osynliga, data.  De nya uppgifterna måste exakt överensstämma med _modellens signatur_ på två sätt:
- Alla funktioner måste finnas i varje logisk instans (säg en rad i en tabell) av de nya data.
- De nya uppgifterna måste bearbetas i förbehandlat, och var och en av de funktioner som är konstruerade, på exakt samma sätt som utbildningsdata.

Ovanstående process anges på många sätt i akademisk litteratur och branschlitteratur. Men alla följande uttalanden betyder samma sak:
- _Poängsätta nya data_ med hjälp av modellen
- _Använda modellen på_ nya data
- _Operationalize_ modellen
- _Distribuera_ modellen
- _Kör modellen_ mot nya data

Som tidigare nämnts skiljer sig modell operationalization för PdM från sina kamrater. Scenarier som involverar avvikelseidentifiering och felidentifiering implementerar vanligtvis _onlinebedömning_ (kallas även _bedömning i realtid)._ Här får modellen _varje_ inkommande post och returnerar en förutsägelse. För avvikelseidentifiering är förutsägelsen en indikation på att en avvikelse inträffade (Exempel: SvM i en klass). För felidentifiering, skulle det vara typ eller klass för fel.

PdM innebär däremot _batchbedömning_. För att överensstämma med modellsignaturen måste funktionerna i de nya data vara konstruerade på samma sätt som träningsdata. För de stora datauppsättningar som är typiska för nya data sammanställs funktioner över tidsfönster och poängsätts i batch. Batchbedömning görs vanligtvis i distribuerade system som [Spark](https://spark.apache.org/) eller [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Det finns ett par alternativ - både suboptimala:
- Strömmande datamotorer stöder aggregering via fönster i minnet. Så det kan hävdas att de stöder online scoring. Men dessa system är lämpliga för täta data i smala tidsfönster, eller glesa element över bredare fönster. De kanske inte skalas bra för de täta data över bredare tidsfönster, vilket kan ses i PdM-scenarier.
- Om batchbedömning inte är tillgänglig är lösningen att anpassa onlinebedömningen för att hantera nya data i små batchar åt gången.

## <a name="solution-templates-for-predictive-maintenance"></a>Lösningsmallar för förebyggande underhåll

Det sista avsnittet i den här guiden innehåller en lista över PdM-lösningsmallar, självstudier och experiment som implementerats i Azure. Dessa PdM-program kan distribueras till en Azure-prenumeration inom några minuter i vissa fall. De kan användas som proof-of-concept demos, sandlådor för att experimentera med alternativ eller acceleratorer för faktiska produktionsimplementeringar. Dessa mallar finns i [Azure AI Gallery](https://gallery.azure.ai) eller Azure [GitHub](https://github.com/Azure). Dessa olika exempel kommer att rullas in i den här lösningsmallen över tid.

| # | Titel | Beskrivning |
|--:|:------|-------------|
| 2 | [Azure-lösningslösningsmall för förutsägande underhåll](https://github.com/Azure/AI-PredictiveMaintenance) | En lösningsmall med öppen källkod som demonstrerar Azure ML-modellering och en komplett Azure-infrastruktur som kan stödja scenarier för förutsägande underhåll i samband med IoT-fjärrövervakning. |
| 3 | [Djupinlärning för förutsägande underhåll](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure Notebook med en demolösning för att använda LSTM-nätverk (Long Short-Term Memory) (en klass av återkommande neurala nätverk) för prediktivt underhåll, med ett [blogginlägg om det här exemplet](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Förarledning för förutsägande underhåll i R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | PdM-modelleringsguide med skript i R.|
| 5 | [Azure Prediktivt underhåll för flyg- och rymdindustrin](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | En av de första PdM-lösningsmallarna baserat på Azure ML v1.0 för flygplansunderhåll. Den här guiden härstammar från det här projektet. |
| 6 | [Azure AI-verktygslåda för IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | AI i IoT Edge med TensorFlow; verktygslåda paket djupinlärning modeller i Azure IoT Edge-kompatibla Docker behållare och exponera dessa modeller som REST API: er.
| 7 | [Azure IoT prediktivt underhåll](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS - Förkonfigurerad lösning. PdM-mall för flygplansunderhåll med IoT Suite. [Ett annat dokument](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) och [en genomgång](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) som är relaterad till samma projekt. |
| 8 | [Mall för förutsägande underhåll med SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo av återstående livslängd scenario baserat på R-tjänster. |
| 9 | [Förarledning för förutsägande underhåll](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funktionen för datauppsättning för flygplansunderhåll som utformats med R med experiment och [datauppsättningar](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) och [experiments](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) [Azure-anteckningsbok](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) och [experiment](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) i AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Utbildningsresurser för prediktivt underhåll

Microsoft Azure erbjuder utbildningsvägar för de grundläggande begreppen bakom PdM-tekniker, förutom innehåll och utbildning om allmänna AI-koncept och -metoder.

| Utbildningsresurs  | Tillgänglighet |
|:-------------------|--------------|
| [Inlärningsväg för pdm med träd och slumpmässig skog](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Offentlig | 
| [Inlärningsväg för PdM med deep learning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Offentlig |
| [AI-utvecklare på Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Offentlig |
| [Microsoft AI-skolan](https://aischool.microsoft.com/learning-paths) | Offentlig |
| [Azure AI Learning från GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Offentlig |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Offentlig |
| [Microsoft AI YouTube Webbseminarier](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Offentlig |
| [Microsoft AI-mässa](https://channel9.msdn.com/Shows/AI-Show) | Offentlig |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Partner |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Partner |

Dessutom erbjuds gratis MOOCS (massiva öppna online-kurser) på AI online av akademiska institutioner som Stanford och MIT, och andra utbildningsföretag.
