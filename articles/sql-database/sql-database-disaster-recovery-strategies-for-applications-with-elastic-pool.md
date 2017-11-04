---
title: "Utforma lösningar för katastrofåterställning - Azure SQL Database | Microsoft Docs"
description: "Lär dig att utforma din molnlösning för katastrofåterställning genom att välja rätt failover-mönster."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2db99057-0c79-4fb0-a7f1-d1c057ec787f
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 04/07/2017
ms.author: sashan;carlrab
ms.openlocfilehash: 03bc991d5c1f644b439e9ebfa0d750cbf0c56764
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Strategi för katastrofåterställning för program som använder SQL-databas elastiska pooler
Vi har lärt dig att molntjänster inte felsäker och oåterkalleligt incidenter inträffa under åren. SQL-databasen innehåller flera funktioner som kan ge för Företagskontinuitet för programmet när dessa händelser inträffar. [Elastiska pooler](sql-database-elastic-pool.md) och enskilda databaser stöd för samma typ av funktioner för katastrofåterställning. Den här artikeln beskriver flera DR strategier för elastiska pooler som utnyttjar funktionerna för verksamhetskontinuitet dessa SQL-databas.

Den här artikeln använder följande kanoniska SaaS ISV programmet mönster:

<i>En modern molnbaserad webbprogrammet etablerar en SQL-databas för varje slutanvändare. ISV: er har många kunder och därför använder många databaser, kallas även klient databaser. Eftersom klient-databaser har vanligtvis oförutsägbart aktivitet mönster, använder ISV en elastisk pool för att göra databasen kostar mycket förutsägbar under en längre tid. Den elastiska poolen också förenklas prestanda när användaraktiviteten ger spikar i diagrammet. Förutom databaserna som klienten använder programmet också flera databaser att hantera användarprofiler, säkerhet, samla in användningsmönster osv. Tillgängligheten för enskilda klienter påverkar inte programmets tillgänglighet som helhet. Dock tillgänglighet och prestanda för hantering av databaser är avgörande för programmets funktionen och om hantering av databaser är offline hela programmet är offline.</i>  

Den här artikeln beskrivs DR strategier som omfattar ett antal scenarier från kostnaden känsliga Start program till sådana som har stränga tillgänglighet.

## <a name="scenario-1-cost-sensitive-startup"></a>Scenario 1. Kostnad känsliga Start
<i>Jag är en start-företag och är mycket kostar känslig.  Jag vill förenkla distribution och hantering av programmet och det kan ha en begränsad SLA för enskilda kunder. Men jag vill se till att programmet som helhet aldrig är offline.</i>

För att uppfylla kravet på enkelhet, distribuera alla klient-databaser i en elastisk pool i Azure-region önskat och distribuera management databaser som georeplikerad enskilda databaser. Använd geo-återställning som levereras utan extra kostnad för katastrofåterställning av klienter. För att säkerställa tillgängligheten för hantering av databaser, geo-replikering dem till en annan region med hjälp av en automatisk redundans gruppera (i förhandsvisning) (steg 1). Pågående kostnaden för katastrofberedskapskonfigurationen i det här scenariot är lika med den totala kostnaden för de sekundära databaserna. Den här konfigurationen visas i nästa diagrammet.

![Bild 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Om ett avbrott uppstår i den primära regionen kan illustreras återställningsstegen för att ta tillämpningsprogrammet online med nästa diagrammet.

* Gruppen redundans initierar automatisk redundans för av hanteringsdatabasen till DR-region. Programmet återansluta automatiskt till de nya primära och alla nya kontona och klient databaser skapas i området för Katastrofåterställning. Befintliga kunder finns data är för tillfället otillgänglig.
* Skapa den elastiska poolen med samma konfiguration som den ursprungliga poolen (2).
* Använd geo-återställning för att skapa kopior av klienten databaser (3). Du kan överväga utlösa enskilda återställning av slutanvändare anslutningar eller använda vissa andra programspecifika prioritet schema.


Då tillämpningsprogrammet är online igen i området för Katastrofåterställning, men vissa kunder uppstår fördröjning vid åtkomst till sina data.

![Bild 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Om avbrottet var temporära, är det möjligt att den primära regionen återställs med Azure innan alla återställningar för databasen har slutförts i DR-region. I det här fallet samordna flytta programmet till den primära regionen. Stegen visas i nästa diagrammet tar processen.

* Avbryt alla väntande geo-återställning begäranden.   
* Växla över hantering av databaser till den primära regionen (5). De gamla primärfärgerna har automatiskt blivit sekundärservrar efter återställning av den region. De växla roller igen. 
* Ändra programmets anslutningssträngen peka tillbaka till den primära regionen. Nu skapas alla nya konton och klient-databaser i den primära regionen. Vissa befintliga kunder finns data är för tillfället otillgänglig.   
* Ange alla databaser i poolen DR till skrivskyddat läge för att se till att de kan ändras i DR region (6). 
* Byt namn eller ta bort motsvarande databaser i poolen primära (7) för varje databas i DR-pool som har ändrats sedan återställningen. 
* Kopiera de uppdaterade databaserna från poolen DR till primära poolen (8). 
* Ta bort DR-poolen (9)

Tillämpningsprogrammet är nu online i den primära regionen med alla klient tillgängliga databaser i poolen primära.

![Bild 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

Nyckeln **dra** för den här strategin är låg pågående kostnad för dataredundans nivå. Säkerhetskopieringar vidtas automatiskt av tjänsten SQL-databas med inga program omarbetning och utan extra kostnad.  Kostnad uppkommer endast när de elastiska databaserna återställs. Den **kompromiss** är att slutföra återställningen av alla klient-databaser tar betydande tid. Hur lång tid är beroende av totalen antalet återställningar som du startar i DR region och totala storlek klient-databaser. Även om du prioritera vissa klienter återställningar framför andra konkurrerar med alla andra återställningar som startas i samma region som tjänsten hanterar och begränsar för att minimera den övergripande effekten på befintliga kunder databaser. Dessutom kan inte återställning av databaserna klient starta förrän ny elastisk pool i DR region har skapats.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scenario 2. Mogen program med nivåindelade service
<i>Jag är en mogen SaaS-program med nivåindelade tjänsten ger och annan serviceavtal för utvärderingsversion kunder och för att betala kunder. Jag har minska kostnaderna så mycket som möjligt för kunder som utvärderingsversion. Utvärderingsversion kunder kan ta avbrottstid men vill minska dess sannolikheten. För betalande kunder finns driftavbrott svarta risker. Kunder är alltid komma åt sina data så att jag vill vara säker på att betala.</i> 

För att stödja det här scenariot måste du avgränsa utvärderingsversioner för klienter från betald klienter genom att placera dem i separata elastiska pooler. Utvärderingsversion kunder har lägre eDTU per klient och lägre SLA med en längre återställningstid. Betalande kunder finns i en pool med högre eDTU per klient och en högre SLA. Om du vill garantera lägsta återställningstid är betalande kunder klient databaser georeplikerad. Den här konfigurationen visas i nästa diagrammet. 

![Bild 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Management-databaser är ganska active som i det första scenariot så att du använder en enda georeplikerad databas för det (1). Detta garanterar förutsägbar prestanda för nya kundprenumerationer, profiluppdateringar och andra hanteringsåtgärder. Den region där primärfärgerna management-databaser finns är den primära regionen och den region där sekundärservrar av management-databaserna finns är DR-region.

Betalande kunder klient databaser har aktiva databaser i poolen ”betald” etablerad på den primära regionen. Etablera en sekundär pool med samma namn i DR-region. Varje klient är georeplikerad till poolen sekundära (2). Detta gör att snabb återställning av alla klient-databaser med växling vid fel. 

Om ett avbrott uppstår i den primära regionen, visas återställningsstegen för att ta tillämpningsprogrammet online i nästa diagram:

![Bild 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Omedelbart växla över hantering av databaser till DR region (3).
* Ändra programmets anslutningssträngen så att den pekar till DR-region. Alla nya konton och klient databaser skapas nu i DR-region. De befintliga utvärderingsversion kunderna ser sina data är för tillfället otillgänglig.
* Växla över betald innehavaren databaser i poolen i DR region omedelbart återställa deras tillgänglighet (4). Eftersom växling vid fel är en snabb nivån metadataändring, överväga en optimering där enskilda redundans initieras på begäran av slutanvändaren anslutningar. 
* Om storleken på din sekundära pool-eDTU är lägre än primärt eftersom de sekundära databaserna krävs endast kapacitet att bearbeta ändra loggar när de hade sekundärservrar, omedelbart öka poolkapacitet nu för alla klienter (fullständig arbetsbelastning 5). 
* Skapa ny elastisk pool med samma namn och samma konfiguration i DR-region för utvärderingsversionen kundernas databaser (6). 
* När utvärderingsperioden kundernas poolen har skapats kan du använda geo-återställning för att återställa enskilda utvärderingsinnehavare databaserna till den nya poolen (7). Överväg att utlösa enskilda återställning av slutanvändare anslutningar eller använda vissa andra programspecifika prioritet schema.

Tillämpningsprogrammet är nu online igen i området för Katastrofåterställning. Alla betalande kunder har åtkomst till sina data medan utvärderingsversion kunder uppstår fördröjning vid åtkomst till sina data.

När den primära regionen återställs med Azure *när* du har återställt programmet i DR-region som du kan fortsätta att köra programmet i den regionen eller du kan välja att växla tillbaka till den primära regionen. Om den primära regionen återställs *innan* failover-processen är klar bör du överväga att misslyckas tillbaka direkt. Återställningen tar de steg som visas i nästa diagram: 

![Bild 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Avbryt alla väntande geo-återställning begäranden.   
* Växla över hantering av databaser (8). Den gamla primärt blir automatiskt sekundärt efter återställning av den region. Nu blir den primära servern igen.  
* Växla över klient betalda databaser (9). På samma sätt efter återställning av den region gamla primärfärgerna automatiskt till sekundära. Nu blir de primärfärgerna igen. 
* Ange de återställda utvärderingsversion databaser som har ändrats i området för DR-skrivskyddad (10).
* Byt namn eller ta bort motsvarande databas i poolen utvärderingsversion kunder primära (11) för varje databas i utvärderingsversion kunder DR-poolen som ändrats sedan återställningen. 
* Kopiera de uppdaterade databaserna från poolen DR till primära poolen (12). 
* Ta bort DR-poolen (13) 

> [!NOTE]
> Redundansåtgärden är asynkron. Det är viktigt att du köra redundanskommandot för klient-databaser i grupper med minst 20 databaser för att minimera tiden för återställning. 
> 
> 

Nyckeln **nytta** av den här strategin är att det ger högsta SLA för betalande kunder. Det garanterar att de nya försök är blockerad som utvärderingsversion DR-poolen har skapats. Den **kompromiss** är att den här installationen ökar den totala kostnaden för databaser för innehavare av kostnaden för den sekundära DR-poolen för betald kunder. Dessutom om sekundära poolen har olika storlekar, prestanda betalande kunder lägre efter redundans tills poolen i DR region uppgraderingen. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scenario 3. Geografiskt distribuerade program med nivåindelade service
<i>Jag har en mogen SaaS-program med nivåindelade service erbjudanden. Jag vill erbjuda ett mycket aggressiv SERVICENIVÅAVTAL till min betald kunder och minska risken för påverkan när avbrott inträffar eftersom även kort avbrott kan orsaka kunden klagomål. Det är viktigt att betalande kunder alltid kan komma åt sina data. Försök är gratis och ett SLA erbjuds inte under utvärderingsperioden.</i> 

Stöd för det här scenariot, använda tre separata elastiska pooler. Etablera två lika storlek pooler med hög edtu: er per databas i två olika områden ska innehålla betald kundernas klient databaser. Tredje poolen innehåller utvärderingsversioner för klienter kan ha lägre edtu: er per databas och tillhandahållas i någon av de två regionerna.

Om du vill garantera lägsta återställningstid under avbrott är betalande kunder klient databaser georeplikerad med 50% av de primära databaserna i var och en av de två regionerna. Dessutom har varje region 50% av de sekundära databaserna. På så sätt kan om en region är offline, högst 50% av betald kundernas databaser påverkas och måste växla över. Andra databaser påverkas inte. Den här konfigurationen visas i följande diagram:

![Bild 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Som i föregående fall management databaser är ganska aktiva så konfigurera dem som enskilda geo-replikerade databaser (1). Detta garanterar förutsägbar prestanda för den nya kunden prenumerationer, profiluppdateringar och andra hanteringsåtgärder. Region A är den primära regionen för management-databaser och region B används för återställning av databaser för hantering.

Betalande kunder klient databaser är också georeplikerad men med primärfärgerna och sekundärservrar delas upp mellan A och B (2)-region. På så sätt kan klienten primära databaserna påverkas av felet kan växla över till den andra regionen och blir tillgängliga. Den andra hälften av databaserna som klienten inte är påverkas alls. 

Nästa diagram illustrerar återställningsstegen för att göra om ett avbrott inträffar i region A.

![Bild 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Omedelbart växla över hantering av databaser till region B (3).
* Ändra programmets anslutningssträngen för att peka på management-databaser i region B. Ändra management-databaser för att kontrollera att nya konton och klient-databaser skapas i region B och befintlig klient-databaser finns det också. De befintliga utvärderingsversion kunderna ser sina data är för tillfället otillgänglig.
* Växla över betald innehavaren databaser i poolen 2 i region B omedelbart återställa deras tillgänglighet (4). Eftersom växling vid fel är en snabb nivån metadataändring, kan du en optimering där enskilda redundans initieras på begäran av slutanvändaren anslutningar. 
* Eftersom nu pool 2 innehåller endast primära databaser, den totala arbetsbelastningen i poolen ökar och omedelbart öka storleken eDTU (5). 
* Skapa ny elastisk pool med samma namn och samma konfiguration i region B för utvärderingsversion kundernas databaser (6). 
* När poolen har skapats kan du använda geo-återställning för att återställa enskilda utvärderingsinnehavare databasen i pool (7). Du kan överväga utlösa enskilda återställning av slutanvändare anslutningar eller använda vissa andra programspecifika prioritet schema.

> [!NOTE]
> Redundansåtgärden är asynkron. För att minimera tiden för återställning, är det viktigt att du köra redundanskommandot för klient-databaser i grupper med minst 20 databaser. 
> 

Nu är ditt program tillbaka online i region B. Alla betalande kunder har åtkomst till sina data medan utvärderingsversion kunder uppstår fördröjning vid åtkomst till sina data.

När region A återställs måste du bestämma om du vill använda region B för utvärderingsversion kunder eller återställning till använder utvärderingsversion kunder i region A. Ett villkor kan vara % utvärderingsinnehavare databaser har ändrats sedan återställningen. Du måste ombalansera betald hyresgäster mellan två pooler oavsett detta beslut. i nästa diagram illustrerar processen när utvärderingsinnehavare databaser växla tillbaka till region A.  

![Bild 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Avbryt alla väntande geo-återställning förfrågningar till DR testpoolen.   
* Växla över databasen för konfigurationshantering (8). Efter återställning av den region blev den gamla primärt automatiskt sekundärt. Nu blir den primära servern igen.  
* Välj vilka betald klient databaser växla tillbaka till poolen 1 och initiera redundans till deras sekundärservrar (9). Efter återställning av den region blev automatiskt sekundärservrar i alla databaser i pool 1. Nu blir 50% av dem primärfärgerna igen. 
* Minska storleken på poolen 2 till ursprungliga eDTU (10).
* Ange alla återställs utvärderingsversion databaser i regionen B till skrivskyddad (11).
* Byt namn eller ta bort motsvarande databasen i den primära testpoolen (12) för varje databas i den utvärderingsversion DR-pool som har ändrats sedan återställningen. 
* Kopiera de uppdaterade databaserna från poolen DR till primära poolen (13). 
* Ta bort DR-poolen (14) 

Nyckeln **fördelar** av den här strategin är:

* Det stöder mest SLA för betalande kunder eftersom det innebär att avbrott inte påverkar mer än 50% av klient-databaser. 
* Det garanterar att de nya försök är blockerad så snart spår DR-poolen har skapats under återställningen. 
* Det låter effektivare användning av pool-kapacitet som 50% av sekundära databaser i pool 1 och pool 2 är garanterat mindre aktiva primära databaser.

Huvudsakliga **avvägningarna** är:

* CRUD-åtgärder för hantering av databaser har kortare svarstid för slutanvändare som är anslutna till region A än för slutanvändare som är kopplat till region B som de körs mot primärt management-databaser.
* Det krävs mer komplexa utformning av av hanteringsdatabasen. Varje klient-post har till exempel en platstagg som behöver ändras under redundans och återställning efter fel.  
* Betalande kunder kan prestanda lägre än vanligt förrän uppgraderingen poolen i region B är klar. 

## <a name="summary"></a>Sammanfattning
Den här artikeln fokuserar på disaster recovery strategier för att databasnivå som används av ett SaaS ISV-program för flera innehavare. Strategin som du väljer baseras på dina behov för programmet, till exempel affärsmodell SLA som du vill erbjuda kunderna budget begränsningen osv. Varje beskrivs strategi beskriver fördelar och kompromiss så att du kan fatta ett välgrundat beslut. Dessutom innehåller ett visst program som sannolikt andra Azure-komponenter. Så att du kan granska deras business continuity vägledning och samordna återställning av databasnivå med dem. Mer information om hur du hanterar återställning av databasprogram i Azure avser [molnlösningar för utformning för katastrofåterställning](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om Azure SQL Database automatiserad säkerhetskopieringar, se [SQL-databas automatisk säkerhetskopiering](sql-database-automated-backups.md).
* En översikt över verksamhetskontinuitet och scenarier finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
* Läs om hur du använder automatisk säkerhetskopiering för återställning i [återställa en databas från säkerhetskopior service-initierad](sql-database-recovery-using-backups.md).
* Mer information om alternativ för snabbare återställning, se [aktiv geo-replikering](sql-database-geo-replication-overview.md).
* Läs om hur du använder automatisk säkerhetskopiering för arkivering i [databaskopieringen](sql-database-copy.md).

