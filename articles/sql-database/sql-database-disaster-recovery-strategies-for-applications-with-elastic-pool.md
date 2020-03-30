---
title: Utforma lösningar för katastrofåterställning
description: Lär dig hur du utformar din molnlösning för haveriberedskap genom att välja rätt redundansmönster.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 4eeaa187142a6d0d97b12f685ebc455f3844606f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825862"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategier för haveriberedskap för program som använder elastiska SQL Database-pooler

Under årens lopp har vi lärt oss att molntjänster inte är idiotsäkra och katastrofala incidenter inträffar. SQL Database innehåller flera funktioner för att ge affärskontinuiteten för ditt program när dessa incidenter inträffar. [Elastiska pooler](sql-database-elastic-pool.md) och enskilda databaser stöder samma typ av katastrofåterställning (DR) funktioner. I den här artikeln beskrivs flera DR-strategier för elastiska pooler som utnyttjar dessa funktioner för affärskontinuitet i SQL Database.

I den här artikeln används följande kanoniska SaaS ISV-programmönster:

Ett modernt molnbaserat webbprogram etablerar en SQL-databas för varje slutanvändare. ISV har många kunder och använder därför många databaser, så kallade klientdatabaser. Eftersom klientdatabaserna vanligtvis har oförutsägbara aktivitetsmönster använder ISV en elastisk pool för att göra databaskostnaden mycket förutsägbar under längre tidsperioder. Den elastiska poolen förenklar också prestandahanteringen när användaraktiviteten ökar. Förutom klientdatabaserna använder programmet också flera databaser för att hantera användarprofiler, säkerhet, samla in användningsmönster etc. Tillgängligheten för de enskilda klienterna påverkar inte programmets tillgänglighet som helhet. Tillgängligheten och prestanda för hanteringsdatabaser är dock avgörande för programmets funktion och om hanteringsdatabaserna är offline är hela programmet offline.

I den här artikeln beskrivs DR-strategier som täcker en rad scenarier från kostnadskänsliga startprogram till program med stränga tillgänglighetskrav.

> [!NOTE]
> Om du använder Premium- eller Affärskritiska databaser och elastiska pooler kan du göra dem motståndskraftiga mot regionala avbrott genom att konvertera dem till zon redundant distributionskonfiguration. Se [Zonundanta databaser](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenario 1. Kostnadskänslig start

Jag är en startup-verksamhet och är extremt kostnadskänslig.  Jag vill förenkla driftsättning och hantering av programmet och jag kan ha ett begränsat serviceavtal för enskilda kunder. Men jag vill se till att ansökan som helhet aldrig är offline.

För att uppfylla enkelhetskravet distribuerar du alla klientdatabaser till en elastisk pool i Azure-regionen efter eget val och distribuerar hanteringsdatabaser som geo-replikerade enskilda databaser. För haveriberedskap av klienter, använd geo-återställa, som kommer utan extra kostnad. För att säkerställa tillgängligheten för hanteringsdatabaserna, geo-replikera dem till en annan region med hjälp av en automatisk redundansgrupp (steg 1). Den pågående kostnaden för konfigurationen för haveriberedskap i det här scenariot är lika med den totala kostnaden för de sekundära databaserna. Den här konfigurationen illustreras i nästa diagram.

![Bild 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Om ett avbrott inträffar i den primära regionen illustreras återställningsstegen för att ansluta programmet till nästa diagram.

* Redundansgruppen initierar automatisk redundans för hanteringsdatabasen till DR-regionen. Programmet återansluts automatiskt till den nya primära och alla nya konton och klientdatabaser skapas i REGIONEN DR. De befintliga kunderna ser sina data som inte är tillgängliga för tillfället.
* Skapa den elastiska poolen med samma konfiguration som den ursprungliga poolen (2).
* Använd geo-återställning för att skapa kopior av klientdatabaserna (3). Du kan överväga att utlösa enskilda återställningar av slutanvändaranslutningarna eller använda något annat programspecifikt prioritetsschema.

Nu är ditt program online igen i DR-regionen, men vissa kunder upplever fördröjning när de kommer åt sina data.

![Bild 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Om avbrottet var tillfälligt är det möjligt att den primära regionen återställs av Azure innan alla databasåterställningar slutförs i DR-regionen. I det här fallet dirigerar vi tillbaka programmet till den primära regionen. Processen tar de steg som illustreras i nästa diagram.

* Avbryt alla utestående begäranden om geoåterställning.
* Växla över hanteringsdatabaserna till den primära regionen (5). Efter regionens återhämtning har de gamla primärvalen automatiskt blivit sekundärer. Nu byter de roller igen.
* Ändra programmets anslutningssträng för att peka tillbaka till det primära området. Nu skapas alla nya konton och klientdatabaser i den primära regionen. Vissa befintliga kunder ser sina data som inte är tillgängliga för tillfället.
* Ange att alla databaser i DR-poolen ska vara skrivskyddade för att säkerställa att de inte kan ändras i DR-regionen (6).
* För varje databas i DR-poolen som har ändrats sedan återställningen byter du namn på eller tar bort motsvarande databaser i den primära poolen (7).
* Kopiera de uppdaterade databaserna från DR-poolen till den primära poolen (8).
* Ta bort DR-poolen (9)

Nu är ditt program online i den primära regionen med alla klientdatabaser tillgängliga i den primära poolen.

![Bild 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Den viktigaste **fördelen med** den här strategin är låg löpande kostnad för redundans på datanivå. Säkerhetskopior görs automatiskt av SQL Database-tjänsten utan program omskrivning och utan extra kostnad.  Kostnaden uppstår endast när de elastiska databaserna återställs. **Avvägningen** är att fullständig återställning av alla klientdatabaser tar betydande tid. Hur lång tid det tar beror på det totala antalet återställningar som du initierar i REGIONEN DR och den totala storleken på klientdatabaserna. Även om du prioriterar vissa klienters återställningar framför andra, konkurrerar du med alla andra återställningar som initieras i samma region som service arbitrates och gasreglage för att minimera den totala påverkan på befintliga kunders databaser. Dessutom kan återställningen av klientdatabaserna inte starta förrän den nya elastiska poolen i DR-regionen har skapats.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenario 2. Äldre program med nivåindelningstjänst

Jag är en mogen SaaS-applikation med nivåindelade serviceerbjudanden och olika serviceavtal för utvärderingskunder och för betalande kunder. För testkunderna måste jag minska kostnaden så mycket som möjligt. Testkunder kan ta driftstopp men jag vill minska sannolikheten. För de betalande kunderna är eventuella driftstopp en flygrisk. Så jag vill se till att betalande kunder alltid kan komma åt sina data.

För att stödja det här scenariot, separera testklienter från betalda klienter genom att placera dem i separata elastiska pooler. Utvärderingskunderna har lägre eDTU eller virtuella kärnor per klient och lägre serviceavtal med längre återställningstid. De betalande kunderna är i en pool med högre eDTU eller virtuella kärnor per klient och ett högre serviceavtal. För att garantera den lägsta återställningstiden är de betalande kundernas klientdatabaser georepförtrade. Den här konfigurationen illustreras i nästa diagram.

![Bild 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Precis som i det första scenariot är hanteringsdatabaserna ganska aktiva så att du använder en enda georeplikerat databas för den (1). Detta säkerställer förutsägbara prestanda för nya kundprenumerationer, profiluppdateringar och andra hanteringsåtgärder. Den region där primärfärgerna för hanteringsdatabaserna finns är den primära regionen och den region där sekundärfilerna i hanteringsdatabaserna finns är DR-regionen.

De betalande kundernas klientdatabaser har aktiva databaser i den "betalda" pool som etablerats i den primära regionen. Etablera en sekundär pool med samma namn i DR-regionen. Varje klient är geo-replikerad till den sekundära poolen (2). Detta möjliggör snabb återställning av alla klientdatabaser med redundans.

Om ett avbrott inträffar i den primära regionen illustreras återställningsstegen för att ansluta programmet i nästa diagram:

![Bild 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Omedelbart växla över hanteringsdatabaserna till DR-regionen (3).
* Ändra programmets anslutningssträng så att den pekar på DR-regionen. Nu skapas alla nya konton och klientdatabaser i REGIONEN DR. De befintliga utvärderingskunderna ser att deras data inte är tillgängliga för tillfället.
* Växla över den betalda klientens databaser till poolen i DR-regionen för att omedelbart återställa deras tillgänglighet (4). Eftersom redundansen är en snabb ändring av metadatanivån bör du tänka på en optimering där de enskilda redundanserna utlöses på begäran av slutanvändaranslutningarna.
* Om din sekundära pool eDTU storlek eller vCore värde var lägre än den primära eftersom de sekundära databaserna endast krävs kapacitet att bearbeta ändringsloggar medan de var sekundärer, omedelbart öka poolen kapacitet nu för att rymma hela arbetsbelastningen av alla hyresgäster (5).
* Skapa den nya elastiska poolen med samma namn och samma konfiguration i DR-regionen för utvärderingskundernas databaser (6).
* När utvärderingsversionen av utvärderingsversionerna har skapats använder du geoåterställning för att återställa de enskilda utvärderingsklientdatabaserna i den nya poolen (7). Överväg att utlösa enskilda återställningar av slutanvändaranslutningarna eller använda något annat programspecifikt prioritetsschema.

Nu är ditt program online igen i DR-regionen. Alla betalande kunder har tillgång till sina data medan provkunderna upplever fördröjning när de får åtkomst till sina data.

När den primära regionen återställs av Azure *när* du har återställt programmet i DR-regionen kan du fortsätta köra programmet i den regionen eller så kan du välja att växla tillbaka till den primära regionen. Om den primära regionen återställs *innan* redundansprocessen slutförs bör du överväga att återställa direkt. Återställningen av återställningen tar de steg som visas i nästa diagram:

![Bild 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Avbryt alla utestående begäranden om geoåterställning.
* Växla över hanteringsdatabaserna (8). Efter regionens återställning blir den gamla primärt automatiskt den sekundära. Nu blir det den primära igen.  
* Växla över de betalda klientdatabaserna (9). På samma sätt, efter regionens återhämtning, blir de gamla primärvalen automatiskt sekundärerna. Nu blir de primärvalen igen.
* Ange de återställda utvärderingsdatabaserna som har ändrats i REGIONEN DR till skrivskyddade (10).
* För varje databas i utvärderingskundernas DR-pool som har ändrats sedan återställningen byter du namn på eller tar bort motsvarande databas i utvärderingskundernas primära pool (11).
* Kopiera de uppdaterade databaserna från DR-poolen till den primära poolen (12).
* Ta bort DR-poolen (13).

> [!NOTE]
> Redundansåtgärden är asynkron. För att minimera återställningstiden är det viktigt att du kör klientdatabasernas redundanskommando i batchar med minst 20 databaser.

Den viktigaste **fördelen med** denna strategi är att den ger det högsta serviceavtalet för betalande kunder. Det garanterar också att de nya försöken avblockeras så snart testversionen DR-poolen skapas. Kompromissen är att den här inställningen ökar den totala kostnaden för klientdatabaserna med kostnaden för den sekundära **DR-poolen** för betalda kunder. Om den sekundära poolen har en annan storlek får dessutom betalande kunder lägre prestanda efter redundans tills pooluppgraderingen i DR-regionen har slutförts.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Geografiskt distribuerat program med nivåindelningstjänst

Jag har en mogen SaaS-applikation med nivåindelade serviceerbjudanden. Jag vill erbjuda ett mycket aggressivt SLA till mina betalda kunder och minimera risken för påverkan när avbrott inträffar eftersom även korta avbrott kan orsaka kundmissnöje. Det är viktigt att de betalande kunderna alltid kan komma åt sina data. Testversionerna är kostnadsfria och ett serviceavtal erbjuds inte under provperioden.

Stöd för det här scenariot använder du tre separata elastiska pooler. Etablera två pooler av samma storlek med hög eDTUs eller virtuella kärnor per databas i två olika regioner för att innehålla de betalda kundernas klientdatabaser. Den tredje poolen som innehåller utvärderingsversionerna kan ha lägre eDTUs eller virtuella kärnor per databas och etableras i en av de två regionerna.

För att garantera den lägsta återställningstiden under avbrott, är de betalande kundernas klientdatabaser geo-replikeras med 50% av de primära databaserna i var och en av de två regionerna. På samma sätt har varje region 50 % av de sekundära databaserna. På så sätt påverkas endast 50 % av de betalda kundernas databaser och måste växla över om en region är offline. De andra databaserna förblir intakta. Den här konfigurationen illustreras i följande diagram:

![Bild 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Liksom i föregående scenarier är hanteringsdatabaserna ganska aktiva så konfigurera dem som enkla geore replikerade databaser (1). Detta säkerställer förutsägbara prestanda för de nya kundprenumerationerna, profiluppdateringar och andra hanteringsåtgärder. Region A är den primära regionen för hanteringsdatabaserna och region B används för återställning av hanteringsdatabaserna.

De betalande kundernas klientdatabaser är också geo-replikerade men med primärval och sekundärer delas mellan region A och region B (2). På så sätt kan klientens primära databaser som påverkas av avbrottet växla över till den andra regionen och bli tillgängliga. Den andra hälften av klientdatabaserna påverkas inte alls.

Nästa diagram illustrerar återställningsstegen att vidta om ett avbrott inträffar i region A.

![Bild 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Omedelbart växla över hanteringsdatabaserna till region B (3).
* Ändra programmets anslutningssträng för att peka på hanteringsdatabaserna i region B. Ändra hanteringsdatabaserna för att se till att de nya kontona och klientdatabaserna skapas i region B och att befintliga klientdatabaser också finns där. De befintliga utvärderingskunderna ser att deras data inte är tillgängliga för tillfället.
* Växla över den betalda klientens databaser till pool 2 i region B för att omedelbart återställa deras tillgänglighet (4). Eftersom redundansen är en snabb ändring av metadatanivån kan du överväga en optimering där de enskilda redundansen utlöses på begäran av slutanvändaranslutningarna.
* Eftersom nu pool 2 innehåller endast primära databaser, den totala arbetsbelastningen i poolen ökar och kan omedelbart öka sin eDTU storlek (5) eller antal virtuella kärnor.
* Skapa den nya elastiska poolen med samma namn och samma konfiguration i region B för utvärderingskundernas databaser (6).
* När poolen har skapats använd geoåterställning för att återställa den enskilda utvärderingsklientdatabasen i poolen (7). Du kan överväga att utlösa enskilda återställningar av slutanvändaranslutningarna eller använda något annat programspecifikt prioritetsschema.

> [!NOTE]
> Redundansåtgärden är asynkron. För att minimera återställningstiden är det viktigt att du kör klientdatabasernas redundanskommando i batchar med minst 20 databaser.

Nu är din ansökan online igen i region B. Alla betalande kunder har tillgång till sina data medan provkunderna upplever fördröjning när de får åtkomst till sina data.

När region A har återställts måste du bestämma om du vill använda region B för utvärderingskunder eller återställa utvärderingsversionen till att använda testversionen av poolen för utvärderingskunder i region A. Ett villkor kan vara % av testklientdatabaser som ändrats sedan återställningen. Oavsett detta beslut måste du balansera om de betalda klienterna mellan två pooler. Nästa diagram illustrerar processen när utvärderingsklientdatabaserna misslyckas tillbaka till region A.  

![Bild 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Avbryt alla utestående begäranden om geoåterställning till DR-testpoolen.
* Växla över hanteringsdatabasen (8). Efter regionens återhämtning blev den gamla primärt automatiskt den sekundära. Nu blir det den primära igen.  
* Välj vilka betalda klientdatabaser som inte kan återställas till pool 1 och initiera redundans till deras sekundära (9). Efter regionens återställning blev alla databaser i pool 1 automatiskt sekundära. Nu 50% av dem blir primärval igen.
* Minska storleken på pool 2 till den ursprungliga eDTU (10) eller antalet virtuella kärnor.
* Ange alla återställda utvärderingsdatabaser i region B till skrivskyddade (11).
* För varje databas i test-DR-poolen som har ändrats sedan återställningen byter du namn på eller tar bort motsvarande databas i den primära testpoolen (12).
* Kopiera de uppdaterade databaserna från DR-poolen till den primära poolen (13).
* Ta bort DR-poolen (14).

De viktigaste **fördelarna med** denna strategi är följande:

* Den stöder det mest aggressiva serviceavtalet för betalande kunder eftersom det säkerställer att ett avbrott inte kan påverka mer än 50 % av klientdatabaserna.
* Det garanterar att de nya försöken är avblockerade så snart trail DR-poolen skapas under återställningen.
* Det möjliggör en effektivare användning av poolkapaciteten eftersom 50 % av de sekundära databaserna i pool 1 och pool 2 garanteras vara mindre aktiva än de primära databaserna.

De viktigaste **kompromisserna** är:

* CRUD-åtgärderna mot hanteringsdatabaserna har lägre svarstid för slutanvändarna som är anslutna till region A än för slutanvändarna som är anslutna till region B när de körs mot huvuddatabaserna.
* Det kräver mer komplex design av hanteringsdatabasen. Varje klientpost har till exempel en platstagg som måste ändras under redundans och redundans.  
* De betalande kunderna kan uppleva lägre prestanda än vanligt tills pooluppgraderingen i region B är klar.

## <a name="summary"></a>Sammanfattning

Den här artikeln fokuserar på katastrofåterställningsstrategier för databasnivån som används av ett SaaS ISV-program med flera innehavare. Den strategi du väljer baseras på behoven hos programmet, till exempel affärsmodellen, det serviceavtal du vill erbjuda dina kunder, budgetbegränsning etc. Varje beskriven strategi beskriver fördelarna och avvägningen så att du kan fatta ett välgrundat beslut. Dessutom innehåller ditt specifika program sannolikt andra Azure-komponenter. Så du granskar deras kontinuitet vägledning och orkestrera återställningen av databasen nivå med dem. Mer information om hur du hanterar återställning av databasprogram i Azure finns i [Utforma molnlösningar för haveriberedskap](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Nästa steg

* Mer information om automatiska säkerhetskopior i Azure SQL Database finns i [automatiska säkerhetskopieringar i SQL Database](sql-database-automated-backups.md).
* En översikt över affärskontinuitet och scenarier finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md).
* Mer information om hur du använder automatiska säkerhetskopior för återställning finns i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
* Mer information om snabbare återställningsalternativ finns i [Aktiva geo-replikerings-](sql-database-active-geo-replication.md) och [automatisk redundansgrupper](sql-database-auto-failover-group.md).
* Mer information om hur du använder automatiska säkerhetskopior för arkivering finns i [databaskopia](sql-database-copy.md).
