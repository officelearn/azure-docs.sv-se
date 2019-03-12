---
title: Utforma lösningar för haveriberedskap – Azure SQL Database | Microsoft Docs
description: Lär dig hur du utformar din lösning för haveriberedskap i molnet genom att välja rätt redundans-mönster.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6a332ce265a4bb41a9ad3c0c3a29683187a0f0d4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760503"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategier för haveriberedskap för program som använder SQL Database elastiska pooler

Vi har lärt dig att molntjänster är inte felsäker och kritiska incidenter sker under åren. SQL Database tillhandahåller flera funktioner för att tillhandahålla för företagskontinuitet programmets när dessa inträffar. [Elastiska pooler](sql-database-elastic-pool.md) och enskilda databaser stöder samma typ av funktioner för katastrofåterställning (DR). Den här artikeln beskriver flera strategier för Haveriberedskap för elastiska pooler som utnyttjar dessa funktioner för affärskontinuitet SQL-databas.

Den här artikeln använder följande canonical SaaS ISV-programmönster:

Ett moderna molnbaserade webbprogram etablerar en SQL-databas för varje slutanvändare. ISV: er har många kunder och därför använder många databaser, kallas klientdatabaser. Eftersom klientdatabaserna vanligtvis har oförutsägbara aktivitet mönster, använder ISV en elastisk pool för att göra databasen kostnad förutsägbarhet under en längre tid. Den elastiska poolen förenklar också prestandahantering när användaraktivitet toppar. Förutom klientdatabaserna använder programmet även flera databaser att hantera användarprofiler, säkerhet, samla in användningsmönster osv. Tillgängligheten för enskilda klienter påverkar inte programmets tillgänglighet som helhet. Dock tillgänglighet och prestanda för hantering av databaser är viktigt för programmets funktionen och om hantering av databaserna är offline hela programmet är offline.

Den här artikeln beskrivs strategier för Haveriberedskap som täcker flera olika scenarier från kostnaden känsliga Start program till sådana med strikta tillgänglighetskrav.

> [!NOTE]
> Om du använder Premium- eller affärskritiska databaser och elastiska pooler kan du dem elastiska regionala avbrott genom att konvertera dem till zonen redundant distributionskonfiguration. Se [redundantzonen databaser](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>Scenario 1. Kostnad känsliga Start

Jag är ett nystartat företag och är mycket kostnad känslig.  Jag vill förenkla distribution och hantering av programmet och jag kan ha en begränsad serviceavtalet för enskilda kunder. Men jag vill kontrollera programmet som helhet aldrig är offline.

Distribuera alla klientdatabaser i en elastisk pool i Azure-region du väljer för att uppfylla kravet på enkelhet, och distribuera hantering av databaser som geo-replikerade enskilda databaser. Använda geo-återställning, som ingår utan extra kostnad för haveriberedskap med klienter. För att säkerställa tillgängligheten för hantering av databaser, geo-replikera dem till en annan region med automatisk redundans gruppen (steg 1). Pågående kostnaden för haveriberedskapskonfiguration i det här scenariot är lika med den totala kostnaden för den sekundära databasen. Den här konfigurationen visas på i nästa diagram.

![Bild 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Om det inträffar avbrottet på den primära regionen, illustreras återställningsstegen för att ta med ditt program online genom att i nästa diagram.

* Redundansgruppen initierar automatisk redundans för av hanteringsdatabasen till regionen för Haveriberedskap. Programmet återansluta automatiskt till de nya primära och alla nya kontona och klientdatabaser skapas i regionen för Haveriberedskap. Befintliga kunder se sina data som är inte tillgänglig för tillfället.
* Skapa den elastiska poolen med samma konfiguration som den ursprungliga poolen (2).
* Använda geo-återställning för att skapa kopior av klienten databaser (3). Du kan överväga att utlösa enskilda återställningar av slutanvändaren anslutningar eller använda vissa andra programspecifika prioritet-schema.

Programmet är nu online igen i regionen för Haveriberedskap, men vissa kunder uppstår fördröjning vid åtkomst till sina data.

![Bild 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Om avbrottet var ett tillfälligt, är det möjligt att den primära regionen återställs med Azure innan alla återställningar för databasen har slutförts i regionen för Haveriberedskap. I det här fallet dirigera flytta programmet till den primära regionen. Processen tar de steg som visas på i nästa diagram.

* Avbryt alla utestående geo-återställning begäranden.
* Växla över management databaserna till den primära regionen (5). Efter den regionen återställningen blir automatiskt gamla USA: s presidentval har sekundära databaser. De växla roller igen.
* Ändra programmets anslutningssträngen så att den pekar till den primära regionen. Nu skapas alla nya konton och klientdatabaser i den primära regionen. Vissa befintliga kunder se sina data som är inte tillgänglig för tillfället.
* Ange alla databaser i poolen DR till skrivskyddat läge för att se till att de inte kan ändras i regionen för Haveriberedskap (6).
* För varje databas i poolen DR som har ändrats sedan återställningen, byta namn på eller ta bort motsvarande databaser i poolen primära (7).
* Kopiera de uppdaterade databaserna från poolen DR till primära poolen (8).
* Ta bort DR-poolen (9)

Programmet är nu online i den primära regionen med alla klientdatabaser som är tillgängliga i den primära poolen.

![Bild 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Nyckeln **dra** för den här strategin är låg pågående kostnad för nivån dataredundans. Säkerhetskopior tas automatiskt av tjänsten SQL Database med ingen omskrivning av program och utan extra kostnad.  Kostnaden uppkommer endast när de elastiska databaserna återställs. Den **kompromiss** är att slutföra återställningen av alla klientdatabaser tar mycket tid. Hur lång tid är beroende av totalt antal återställningar som du startar i regionen för Haveriberedskap och den totala storleken på klientdatabaserna. Även om du prioritera vissa klienter återställningar över andra konkurrerar med alla andra återställningar som startas i samma region som tjänsten hanterar och begränsar för att minimera den övergripande effekten på de befintliga kunderna databaser. Dessutom kan kan inte återställning av klientdatabaser starta förrän den nya elastiska poolen i regionen för Haveriberedskap har skapats.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenario 2. Mogen program med en nivåindelad tjänst

Jag är en mogen SaaS-program med nivåindelade service-erbjudanden och olika serviceavtal för utvärderingsversion kunder och för att betala kunder. Jag har minska kostnaden så mycket som möjligt för kunder som utvärderingsversion. Utvärderingsversion kunder kan dra avbrottstid men jag vill minska dess sannolikheten. För betalande kunder är driftavbrott flygning risk. Jag vill se till att betala kan kunder alltid komma åt sina data.

För det här scenariot måste du avgränsa utvärderingsversioner för klienter från betald klienter genom att sätta dem i separata elastiska pooler. Utvärderingsversion kunder har lägre edtu: er eller v-kärnor per klient och serviceavtal med lägre med en längre återställningstid. Betalande kunder finns i en pool med högre edtu: er eller v-kärnor per klient och ett högre serviceavtal. För att garantera lägsta återställningstiden är betalande kunder klientdatabaser geo-replikerade. Den här konfigurationen visas på i nästa diagram.

![Bild 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Hantering av databaserna är ganska aktiva som i det första scenariot så att du använder en enda geo-replikerad databas för den (1). Detta säkerställer den förutsägbara prestandan för nya kundprenumerationer, profiluppdateringar och andra hanteringsåtgärder. Den region där USA: s presidentval av management-databaserna finns är den primära regionen och den region där sekundärservrar av management-databaserna finns är DR-regionen.

Klientdatabaser för betalande kunder har aktiva databaser i poolen ”betald” etablerade i den primära regionen. Etablera en sekundär pool med samma namn i regionen för Haveriberedskap. Varje klient är geo-replikerad sekundär poolen (2). Detta gör det möjligt för snabb återställning av alla klientdatabaser med hjälp av redundans.

Om det inträffar avbrottet på den primära regionen, visas återställningsstegen för att ta med ditt program online i nästa diagram:

![Bild 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Redundansväxla direkt hantering av databaser till regionen för Haveriberedskap (3).
* Ändra programmets anslutningssträngen så att den pekar till regionen för Haveriberedskap. Alla nya konton och klientdatabaser skapas nu i regionen för Haveriberedskap. De befintliga utvärderingsversion kunderna se sina data som är tillfälligt otillgänglig.
* Växla över betald klientens databaser i poolen i regionen för Haveriberedskap att återställa omedelbart tillgängliga (4). Eftersom växling vid fel är en snabb nivå metadataändring, Överväg en optimering där enskilda redundansväxlingarna utlöses på begäran av slutanvändaren-anslutningar.
* Om ditt sekundära pool eDTU storlek eller vCore-värde är lägre än primärt eftersom den sekundära databasen krävs endast kapacitet för att bearbeta ändringsloggar när de hade sekundära databaser, omedelbart öka poolkapacitet nu att hantera arbetsbelastningen fullständig för alla klienter (5).
* Skapa ny elastisk pool med samma namn och samma konfiguration i regionen för Haveriberedskap för utvärderingsversion kundernas databaser (6).
* När utvärderingsversionen kundernas poolen har skapats kan du använda geo-återställning för att återställa enskilda utvärderingsinnehavare databaserna till den nya poolen (7). Överväg att utlösa enskilda återställningar av slutanvändaren anslutningar eller använda vissa andra programspecifika prioritet-schema.

Programmet är nu online igen i regionen för Haveriberedskap. Alla betalande kunder har åtkomst till sina data när utvärderingsversionen kunder uppstår fördröjning vid åtkomst till sina data.

När den primära regionen återställs med Azure *när* du har återställt programmet i DR-regionen som du kan fortsätta att köra programmet i den regionen eller du kan välja att växla tillbaka till den primära regionen. Om den primära regionen återställs *innan* redundans är klart bör du överväga att du växlar tillbaka direkt. Återställningen tar de steg som visas i nästa diagram:

![Bild 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Avbryt alla utestående geo-återställning begäranden.
* Växla över hantering av databaser (8). Efter den regionen återställningen blir den gamla primärt automatiskt sekundärt. Nu blir primärt igen.  
* Växla över betald klientdatabaser (9). På samma sätt, efter den regionen återställningen gamla USA: s presidentval blir automatiskt de sekundära databaser. Nu de blir USA: s presidentval.
* Ange de återställda utvärderingsversion databaser som har ändrats i regionen för Haveriberedskap till skrivskyddat läge (10).
* Byt namn på eller ta bort motsvarande databas i poolen utvärderingsversion kunder primära (11) för varje databas i poolen utvärderingsversion kunder DR som ändrats sedan återställningen.
* Kopiera de uppdaterade databaserna från poolen DR till primära poolen (12).
* Ta bort poolen DR (13).

> [!NOTE]
> Redundansåtgärden är asynkron. Det är viktigt att du köra redundanskommandot för klient-databaserna i batchar med minst 20 databaser för att minimera tiden för återställning.

Nyckeln **dra** av den här strategin är att det ger högsta serviceavtalet för betalande kunder. Det garanterar även att nya försök är blockerad när utvärderingsversionen DR-poolen har skapats. Den **kompromiss** är att den här installationen ökar den totala kostnaden för klientdatabaserna av kostnaden för den sekundära DR-adresspoolen för betalda kunder. Dessutom om sekundära poolen har en annan storlek, prestanda betalande kunder lägre efter en redundansväxling tills poolen uppgraderingen i regionen för Haveriberedskap har slutförts.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Geografiskt distribuerade program med en nivåindelad tjänst

Jag har en mogen SaaS-program med nivåindelade service-erbjudanden. Jag vill erbjuda ett serviceavtal på mycket aggressiva min betald kunder och minimera risken för påverkan när avbrott inträffar eftersom även korta avbrott kan orsaka kunden klagomål från slutanvändarna. Det är viktigt att betalande kunder alltid kan komma åt sina data. Försök är kostnadsfria och serviceavtal (SLA) erbjuds inte under utvärderingsperioden.

För det här scenariot måste du använda tre separata elastiska pooler. Etablera två lika med storleken pooler med hög edtu: er eller v-kärnor per databas i två olika regioner ska innehålla betald kundernas klientdatabaser. Den tredje pool som innehåller de utvärderingsversioner för klienterna kan ha lägre edtu: er eller v-kärnor per databas och etableras i två regioner.

För att garantera lägsta återställningstiden vid avbrott, är betalande kunder klientdatabaser geo-replikerade med 50% av de primära databaserna i var och en av de båda regionerna. På samma sätt kan har varje region 50% av den sekundära databasen. På så sätt kan om en region är offline, högst 50% av de betalda kunder databaser som påverkas och måste växla över. De andra databaserna förblir intakta. Den här konfigurationen illustreras i följande diagram:

![Bild 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Som i föregående scenarier, hantering av databaserna är ganska aktiva så konfigurera dem som enskilda geo-replikerade databaser (1). Detta säkerställer den förutsägbara prestandan för den nya kunden prenumerationer, profiluppdateringar och andra hanteringsåtgärder. Regionen A är den primära regionen för hantering av databaser och region B som ska användas för återställning av management-databaser.

Klientdatabaser för betalande kunder är också geo-replikerade men med USA: s presidentval och sekundärservrar delas upp mellan A och B (2)-region. På så sätt kan de primära klientdatabaser som påverkas av felet kan växla över till den andra regionen och blir tillgängliga. Den andra hälften av klient-databaserna inte är påverkas alls.

I nästa diagram visas recovery-åtgärder vid ett eventuellt strömavbrott i regionen A.

![Bild 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Redundansväxla direkt hantering av databaser till region B (3).
* Ändra programmets anslutningssträngen för att peka till hantering av databaser i regionen B. Ändra management databaser så att nya konton och klientdatabaser skapas i regionen B och befintlig klient-databaserna finns det också. De befintliga utvärderingsversion kunderna se sina data som är tillfälligt otillgänglig.
* Växla över den betalda klientdatabaser till pool 2 i region B för att återställa omedelbart tillgängliga (4). Eftersom växling vid fel är en snabb nivån har ändrats, kan du en optimering där enskilda redundansväxlingarna utlöses på begäran av slutanvändaren-anslutningar.
* Sedan nu pool 2 innehåller endast primära databaser, totalt antal arbetsbelastningen i poolen ökar och genast kan öka dess eDTU-storlek (5) eller antal virtuella kärnor.
* Skapa ny elastisk pool med samma namn och samma konfiguration i regionen B för utvärderingsversion kundernas databaser (6).
* När poolen har skapats kan du använda geo-återställning för att återställa enskilda utvärderingsversion klientdatabasen till pool (7). Du kan överväga att utlösa enskilda återställningar av slutanvändaren anslutningar eller använda vissa andra programspecifika prioritet-schema.

> [!NOTE]
> Redundansåtgärden är asynkron. För att minimera tiden för återställning, är det viktigt att du köra redundanskommandot för klient-databaserna i batchar med minst 20 databaser.

Nu är ditt program online igen i regionen B. Alla betalande kunder har åtkomst till sina data när utvärderingsversionen kunder uppstår fördröjning vid åtkomst till sina data.

När en region A återställs måste du bestämma om du vill använda region B för utvärderingsversion kunder eller återställning efter fel till att använda poolen utvärderingsversion kunder i regionen A. Ett villkor kan vara % av utvärderingsversion klientdatabaser som ändrats sedan återställningen. Oavsett detta beslut behöver du ombalansera de betalda innehavarna mellan två pooler. i nästa diagram illustrerar processen när utvärderingsversionen klientdatabaserna växla tillbaka till regionen A.  

![Bild 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Avbryt alla utestående geo-återställning förfrågningar till DR testpoolen.
* Växla över databasen för konfigurationshantering (8). Efter den regionen återställningen blev den gamla primärt automatiskt sekundärt. Nu blir primärt igen.  
* Välj vilken betald klientorganisation databaser växla tillbaka till pool 1 och initiera redundans till sina sekundärservrar (9). Efter den regionen återställningen blev alla databaser i pool 1 automatiskt sekundära databaser. Nu blir 50% av dem USA: s presidentval igen.
* Minska storleken på poolen 2 till den ursprungliga eDTU (10) eller antalet virtuella kärnor.
* Ange alla återställs utvärderingsversion databaser i regionen B till skrivskyddat läge (11).
* Byt namn på eller ta bort motsvarande databasen i den primära testpoolen (12) för varje databas i poolen för utvärderingsversion DR som har ändrats sedan återställningen.
* Kopiera de uppdaterade databaserna från poolen DR till primära poolen (13).
* Ta bort poolen DR (14).

Nyckeln **fördelar** av den här strategin är:

* Stöder som mest serviceavtalet för betalande kunder eftersom det garanterar att ett avbrott inte kan påverkar mer än 50% av klientdatabaser.
* Det garanterar att nya försök är blockerad när spårning DR-poolen har skapats under återställningen.
* Det möjliggör mer effektiv användning av pool-kapacitet som 50% av sekundära databaser i pool 1 och pool 2 garanterat aktiveras mindre än de primära databaserna.

Huvudsakliga **avvägningarna** är:

* CRUD-åtgärder mot management-databaser har kortare svarstider för slutanvändare som är anslutna till region A än för slutanvändare som är anslutna till region B som de körs mot primärt management-databaser.
* Det krävs mer komplexa utformningen av databasen för konfigurationshantering. Varje klient-post har exempelvis en plats-tagg som behöver ändras under redundans och återställning efter fel.  
* Betalande kunder kan prestanda lägre än vanligt tills poolen uppgraderingen i regionen B är klar.

## <a name="summary"></a>Sammanfattning

Den här artikeln fokuserar på strategierna för haveriberedskap för databasnivån som används av en SaaS ISV-program för flera innehavare. Vilken strategi du väljer baseras på dina behov för programmet, till exempel affärsmodell serviceavtal som du vill erbjuda dina kunder, budgetstatus begränsningen osv. Varje beskrivs strategi beskriver fördelar och kompromiss så att du kan fatta ett välgrundat beslut. Dessutom innehåller ett visst program som sannolikt andra Azure-komponenter. Så att du granskar sina business continuity vägledning och dirigera återställningen av databasnivån med dem. Mer information om hur du hanterar återställning av databasprogram i Azure finns att [designa molnlösningar för disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Nästa steg

* Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [SQL Database automatiska säkerhetskopior](sql-database-automated-backups.md).
* En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
* Läs om hur du använder automatiska säkerhetskopieringar för återställning i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
* Läs om hur du snabbare återställningsalternativ i [aktiv geo-replikering](sql-database-active-geo-replication.md) och [automatisk redundans grupper](sql-database-auto-failover-group.md).
* Läs om hur du använder automatiska säkerhetskopieringar för arkivering i [databaskopieringen](sql-database-copy.md).
