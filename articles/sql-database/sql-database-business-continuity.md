---
title: Affärskontinuitet i molnet – databasåterställning – SQL Database | Microsoft Docs
description: Lär dig hur Azure SQL Database stöder databasåterställning och affärskontinuitet i molnet och hur du kan köra verksamhetskritiska molnprogram.
keywords: affärskontinuitet, molnaffärskontinuitet, databashaveriberedskap, databasåterställning
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: On Demand
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 46ab4a177cc7d86e5d967ff8e219dae96f82a0dc
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263154"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Översikt över affärskontinuitet med Azure SQL Database

Den här översikten beskriver de funktioner som Azure SQL Database tillhandahåller för affärskontinuitet och haveriberedskap. Läs mer om alternativ, rekommendationer och självstudier för att återställa avbrottshändelser som kan leda till dataförlust eller göra databasen och programmet otillgängliga. Lär dig vad du ska göra när en användare eller ett program fel påverkar dataintegriteten, uppstår ett avbrott i en Azure-region eller ditt program kräver underhåll.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-funktioner som du kan använda för att upprätthålla affärskontinuitet

SQL Database tillhandahåller flera funktioner för affärskontinuitet, inklusive automatiserade säkerhetskopieringar och valfri databasreplikering. Den uppskattade återställningstiden (ERT) och den potentiella dataförlusten för de senaste transaktionerna skiljer sig mellan de olika metoderna. Om du förstår de olika alternativen blir det enklare att välja mellan dem, och i de flesta fall kan du använda dem tillsammans för olika scenarier. När du utvecklar din affärskontinuitetsplan är det viktigt att du tittar på den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse – detta är ditt mål för återställningstid (RTO). Du måste också att förstå längsta senaste data uppdateringar (tidsintervall) som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse – detta är ditt mål för återställningspunkt (RPO).

I följande tabell jämförs ERT och RPO för varje tjänstnivå för de tre vanligaste scenarierna.

| Funktion | Basic | Standard | Premium  | Generellt syfte | Affärskritisk
| --- | --- | --- | --- |--- |--- |
| Återställning till tidpunkt från säkerhetskopia |En återställningspunkt inom 7 dagar |En återställningspunkt inom 35 dagar |En återställningspunkt inom 35 dagar |En återställningspunkt inom konfigurerade period (upp till 35 dagar)|En återställningspunkt inom konfigurerade period (upp till 35 dagar)|
| GEO-återställning från geo-replikerade säkerhetskopior |ERT < 12 timme, RPO < 1 timme |ERT < 12 timme, RPO < 1 timme |ERT < 12 timme, RPO < 1 timme |ERT < 12 timme, RPO < 1 timme|ERT < 12 timme, RPO < 1 timme|
| Återställa från långsiktig kvarhållning i SQL |ERT < 12 timme, RPO < 1 vecka |ERT < 12 timme, RPO < 1 vecka |ERT < 12 timme, RPO < 1 vecka |ERT < 12 timme, RPO < 1 vecka|ERT < 12 timme, RPO < 1 vecka|
| Aktiv geo-replikering |ERT < 30 sekunder, RPO < 5 sekunder |ERT < 30 sekunder, RPO < 5 sekunder |ERT < 30 sekunder, RPO < 5 sekunder |ERT < 30 sekunder, RPO < 5 sekunder|ERT < 30 sekunder, RPO < 5 sekunder|

### <a name="use-point-in-time-restore-to-recover-a-database"></a>Använd point-in-time-återställning för att återställa en databas

SQL Database utför automatiskt en kombination av fullständiga databassäkerhetskopieringar varje vecka, differentiella säkerhetskopieringar varje timme och transaktionen loggsäkerhetskopior var femte - tio minuter för att skydda ditt företag mot dataförlust. Om du använder den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md), och sedan dessa säkerhetskopior lagras i RA-GRS-lagring i 35 dagar för databaser på Standard och Premium-servicenivåerna och 7 dagar för databaser på Basic-tjänstnivån. Om kvarhållningsperioden för din tjänstenivå inte uppfyller dina verksamhetskrav kan du öka kvarhållningsperioden genom att [byta tjänstnivå](sql-database-single-database-scale.md). Om du använder den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md), kvarhållning för säkerhetskopior är Konfigurerbart upp till 35 dagar i generell användning och företag kritiska nivåer. De fullständiga och differentiella säkerhetskopieringarna replikeras också till ett [kopplat datacenter](../best-practices-availability-paired-regions.md) för skydd mot avbrott på datacentret. Mer information finns i [automatiska databassäkerhetskopieringar](sql-database-automated-backups.md).

Om den maximala stöds point-in-time-återställning (PITR) kvarhållningsperioden är inte tillräcklig för ditt program, kan du utöka den genom att konfigurera en långsiktig kvarhållning av säkerhetskopior (LTR)-princip för databaserna. Mer information finns i [automatiserade säkerhetskopieringar](sql-database-automated-backups.md) och [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).

Du kan använda dessa automatiska databassäkerhetskopior för att återställa en databas från olika avbrottshändelser, både i ditt datacenter och till ett annat datacenter. Om du använder automatiska databassäkerhetskopieringar beror den beräknade återställningstiden på flera faktorer, inklusive det totala antalet databaser som återställs i samma region vid samma tidpunkt, databasens storlek, transaktionsloggarnas storlek och nätverksbandbredden. Tiden för återställning är vanligtvis mindre än 12 timmar. Det kan ta längre tid att återställa en databas med mycket stora eller active. Mer information om tiden för återställning finns i [databasen återställningstid](sql-database-recovery-using-backups.md#recovery-time). När du återställer till en annan dataregion är den potentiella dataförlusten begränsad till 1 timme med geo-redundant lagring med differentiella säkerhetskopieringar varje timme.

> [!IMPORTANT]
> Om du återställer med hjälp av automatisk säkerhetskopior måste du vara medlem i SQL Server-rollen Deltagare eller vara prenumerationsägaren. Mer information finns i [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (Rollbaserad åtkomstkontroll: Inbyggda roller). Du kan återställa med hjälp av Azure Portal, PowerShell eller REST-API:et. Du kan inte använda Transact-SQL.
>

Använd automatiska säkerhetskopieringar som din affärskontinuitets- och återställningsmetod om ditt program:

* Inte betraktas som verksamhetskritiskt.
* Har ett bindande SLA - ett driftavbrott på 24 timmar eller inte resulterar längre i ekonomiska ansvarsskyldigheter.
* Har en låg frekvens av dataändringar (lågt antal transaktioner per timme) och en förlust på upp till en timmes ändringar är en acceptabel dataförlust.
* Är kostnadskänsligt.

Om du behöver snabbare återställning använder [aktiv geo-replikering](sql-database-geo-replication-overview.md) (beskrivs nedan). Om du behöver för att kunna återställa data från en period som är äldre än 35 dagar eller använda [långsiktig kvarhållning](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Använda aktiv geo-replikering och automatisk redundans grupper för att minska återställningstiden och begränsa dataförlusten som är associerad med en återställning

Förutom att använda databassäkerhetskopior för databasåterställning om det uppstår ett verksamhetsavbrott kan du använda [aktiv geo-replikering](sql-database-geo-replication-overview.md) du konfigurerar en databas om du vill ha upp till fyra läsbara sekundära databaser i regionerna som du önskar. Dessa sekundära databaser synkroniseras med den primära databasen med hjälp av en asynkron replikeringsmekanism. Den här funktionen används för att skydda mot verksamhetsavbrott om det uppstår ett avbrott på datacentret eller under en uppgradering av programmet. Aktiv geo-replikering kan också användas för att ge bättre frågeprestanda för skrivskyddade frågor till geografiskt spridda användare.

Aktivera automatisk och transparent redundans bör du organisera dina geo-replikerade databaser i grupper med hjälp av den [automatisk redundans grupp](sql-database-geo-replication-overview.md) funktionen i SQL Database.

Om den primära databasen oväntat kopplas från eller om du behöver ta den offline för underhållsaktiviteter, kan du snabbt uppgradera en sekundär för att bli den primära (även kallat redundansväxling) och konfigurera program ska ansluta till det uppgraderade primärt. Om programmet ansluter till databaser med hjälp av lyssnare för växling vid fel, behöver du inte ändra SQL-sträng konfigurationen efter en redundansväxling. Vid en planerad redundansväxling förekommer ingen dataförlust. Vid en oplanerad redundansväxling kan en mindre mängd data gå förlorade för de senaste transaktionerna på grund av hur den asynkrona replikeringen fungerar. Med automatisk redundans grupper kan anpassa du princip för redundansväxling för att minimera potentiell dataförlust. Efter en redundansväxling kan du senare växla tillbaka igen, antingen enligt en plan eller när datacentret är online igen. I samtliga fall upplever användarna ett kort avbrott och måste återansluta.

> [!IMPORTANT]
> Om du vill använda aktiv geo-replikering och automatisk redundans grupper, måste du vara prenumerationsägaren eller ha administrativ behörighet i SQL Server. Du kan konfigurera och redundansväxla med Azure portal, PowerShell eller REST-API med hjälp av Azure-Prenumerationsbehörigheter eller med hjälp av Transact-SQL med SQL Server-behörigheter.
> 

Använda aktiv geo-replikering och automatisk redundans grupper om programmet uppfyller något av dessa villkor:

* Är verksamhetskritiskt.
* Har ett serviceavtal (SLA) som inte tillåter driftavbrott på 24 timmar eller mer.
* Avbrott kan resultera i ekonomiska ansvarsskyldigheter.
* Har en hög frekvens av dataändringar och en timmes dataförlust är inte acceptabelt.
* Den extra kostnaden för aktiv geo-replikering är lägre än de potentiella ekonomiska skyldigheterna och den associerade affärsförlusten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Återställa en databas efter ett användar- eller programfel

Ingen är perfekt! En användare kan oavsiktligt ta bort vissa data eller av misstag radera en viktig tabell eller till och med en hel databas. Och ett program kan av misstag skriva över värdefulla data med felaktiga data på grund av ett fel i programmet.

I det här scenariot har du följande återställningsalternativ.

### <a name="perform-a-point-in-time-restore"></a>Utföra en återställning till en viss tidpunkt
Du kan använda automatiska säkerhetskopior för att återställa en kopia av databasen till en tidigare tidpunkt, förutsatt att tiden ligger inom databasens kvarhållningsperiod. När databasen har återställts kan du antingen ersätta den ursprungliga databasen med den återställda databasen eller kopiera nödvändiga data från den återställda informationen till den ursprungliga databasen. Om databasen använder aktiv geo-replikering, rekommenderar vi att du kopierar nödvändiga data från den återställda kopian till den ursprungliga databasen. Om du ersätter den ursprungliga databasen med den återställda databasen måste du konfigurera om och omsynkronisera aktiv geo-replikering (vilket kan ta ganska lite tid innan en stor databas). Medan detta återställer en databas till den senaste stöds tillgängliga tidpunkt, återställa geo-secondary till valfri punkt i tiden för närvarande inte.

Mer information och detaljerade anvisningar som beskriver hur du återställer en databas till en viss tidpunkt med hjälp av Azure Portal eller PowerShell finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore). Du kan inte återställa med hjälp av Transact-SQL.

### <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
Om databasen tas bort, men den logiska servern inte har tagits bort, kan du återställa den borttagna databasen till den tidpunkt då den togs bort. Om du gör det återställs en säkerhetskopia av databasen till samma logiska SQL-server som den togs bort från. Du kan återställa den genom att använda det ursprungliga namnet eller ange ett nytt namn eller den återställda databasen.

Mer information och detaljerade anvisningar som beskriver hur du återställer en borttagen databas med hjälp av Azure Portal eller PowerShell finns i avsnittet om hur du [återställer en borttagen databas](sql-database-recovery-using-backups.md#deleted-database-restore). Du kan inte återställa med hjälp av Transact-SQL.

> [!IMPORTANT]
> Om den logiska servern tas bort kan du inte återställa en borttagen databas.


### <a name="restore-backups-from-long-term-retention"></a>Återställa säkerhetskopior från långsiktig kvarhållning

Om dataförlusten inträffade utanför den aktuella kvarhållningsperioden för automatiska säkerhetskopior och databasen har konfigurerats för långsiktig kvarhållning av säkerhetskopior med Azure blob-lagring, kan du återställa från en fullständig säkerhetskopiering i Azure blob storage till en ny databas. I detta läge kan du antingen ersätta den ursprungliga databasen med den återställda databasen eller kopiera nödvändiga data från den återställda databasen till den ursprungliga databasen. Om du vill hämta en äldre version av databasen innan en stor uppgradering, uppfylla en begäran från revisorer eller juridiska krav, kan du skapa en databas med en fullständig säkerhetskopia som sparats i Azure blob storage.  Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Återställa en databas till en annan region från ett avbrott på ett regionalt Azure-datacenter
<!-- Explain this scenario -->

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar.

* Ett alternativ är att vänta tills databasen är tillbaka online när avbrottet på datadatacentret är över. Det här fungerar för program som har råd att ha databasen offline. Till exempel ett utvecklingsprojekt eller en kostnadsfri utvärderingsversion som du inte behöver arbeta med hela tiden. När det uppstår ett avbrott i ett datacenter, vet inte du hur länge driftstörningarna kan vara, så det här alternativet fungerar bara om du inte behöver databasen på ett tag.
* Ett annat alternativ är att antingen misslyckas över till en annan dataregion om du använder aktiv geo-replikering eller att återställa en databas med geo-redundanta databassäkerhetskopior (geo-återställning). Redundansväxlingar tar bara några sekunder medan databasåterställning från säkerhetskopior tar timmar.

När du utför en åtgärd, hur lång tid det tar att återställa och hur mycket data går förlorade beror på hur du väljer att använda dessa funktioner för affärskontinuitet i ditt program. Verkligen, kan du välja att använda en kombination av databassäkerhetskopior och aktiv geo-replikering beroende på dina programkrav. Information om designöverväganden för fristående databaser och för elastiska pooler när dessa funktioner för affärskontinuitet används finns i [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Utforma ett program för haveriberedskap i molnet) och [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Haveriberedskapsstrategier med en elastisk pool).

Följande avsnitt innehåller en översikt över stegen för att återställa med hjälp av databassäkerhetskopior eller aktiv geo-replikering. Detaljerade anvisningar för planering av krav, steg efter återställning och information om hur du simulerar ett avbrott för att utföra ett programåterställningstest, i [återställa en SQL-databas från ett avbrott](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Förbereda för ett avbrott
Oavsett vilken funktion för affärskontinuitet du använder, måste du:

* Identifiera och förbered målservern, inklusive brandväggsregler på servernivå, inloggningar och behörigheter på huvuddatabasnivå.
* Bestämma hur du ska omdirigera klienter och klientprogram till den nya servern.
* Dokumentera andra beroenden, till exempel granskningsinställningar och aviseringar.

Om du inte framställer korrekt, att dina program efter en redundansväxling eller en databasåterställning tar extra tid och förmodligen även kräva felsökning i taget stress - ingen bra kombination.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Växla över till en geo-replikerad sekundär databas
Om du använder aktiv geo-replikering och automatisk redundans grupper som återställningsmetod, du kan konfigurera en princip för automatisk redundans eller använda [manuell redundans](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). När åtgärden har startat orsakar växling vid fel sekundärt du blir den nya primärt och är redo att registrera nya transaktioner och svara på frågor – med minimal dataförlust för de data som inte har replikerats. Information om hur du utformar redundansprocessen finns i [utforma ett program för katastrofåterställning i molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> När datacentret är online igen återansluta till den nya primärt gamla USA: s presidentval automatiskt och blir sekundära databaser. Om du vill flytta den primära tillbaka till den ursprungliga regionen kan du initiera en planerad redundans manuellt (återställning). 
> 

### <a name="perform-a-geo-restore"></a>Utföra en geo-återställning
Om du använder automatiska säkerhetskopieringar med geo-redundant lagringsreplikering som återställningsmetod [initiera en databasåterställning med geo-återställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Återställningen sker vanligtvis inom 12 timmar – med en dataförlust på upp till en timme beroende på när den senaste differentiella säkerhetskopieringen per timme gjordes och replikerades. Databasen kan inte registrera några transaktioner eller svara på frågor förrän återställningen har slutförts. Medan detta återställer en databas till den senaste stöds tillgängliga tidpunkt, återställa geo-secondary till valfri punkt i tiden för närvarande inte.

> [!NOTE]
> Om datacentret är online igen innan du växlar över programmet till den återställda databasen, kan du avbryta återställningen.  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>Utföra åtgärder efter en redundansväxling eller återställning
Efter återställningen från endera återställningsmetod måste du utföra följande ytterligare uppgifter innan dina användare och program kan komma igång igen:

* Omdirigera klienter och klientprogram till den nya servern och återställda databasen
* Kontrollera att lämpliga brandväggsregler på servernivå är på plats så att användarna kan ansluta (eller använd [brandväggsregler på databasnivå](sql-database-firewall-configure.md#creating-and-managing-firewall-rules))
* Se till att rätt inloggningar och behörigheter på huvuddatabasnivå är på plats (eller använd [inneslutna användare](https://msdn.microsoft.com/library/ff929188.aspx))
* Konfigurera granskning efter behov
* Konfigurera aviseringar efter behov

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uppgradera ett program med minimal avbrottstid
Ibland måste ett program kopplas från på grund av planerat underhåll, till exempel en uppgradering av programmet. [Hantera programuppgraderingar](sql-database-manage-application-rolling-upgrade.md) beskriver hur du använder aktiv geo-replikering för att möjliggöra löpande uppgraderingar av ditt molnprogram minimal avbrottstid under uppgraderingarna och etablera en återställningsväg om något går fel. 

## <a name="next-steps"></a>Nästa steg
En beskrivning av designöverväganden för fristående databaser och för elastiska pooler finns i [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Utforma ett program för haveriberedskap i molnet) och [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Haveriberedskapsstrategier med en elastisk pool).
