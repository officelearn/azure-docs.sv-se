---
title: "Affärskontinuitet i molnet – databasåterställning – SQL Database | Microsoft Docs"
description: "Lär dig hur Azure SQL Database stöder databasåterställning och affärskontinuitet i molnet och hur du kan köra verksamhetskritiska molnprogram."
keywords: business continuity,cloud business continuity,database disaster recovery,database recovery
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab;sashan
translationtype: Human Translation
ms.sourcegitcommit: 747f6ca642a33c4ce9bcaacad4976e8eaed8fa44
ms.openlocfilehash: f642cfade2369f5c758ab45994c7cf3f37b6d4c5


---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Översikt över affärskontinuitet med Azure SQL Database
Den här översikten beskriver de funktioner som Azure SQL Database tillhandahåller för affärskontinuitet och haveriberedskap. Den innehåller alternativ, rekommendationer och självstudier som hjälper dig att återställa data efter avbrottshändelser som kan leda till dataförlust eller göra databasen och programmet otillgängliga. Du lär dig bland annat vad du kan göra när ett användar- eller programfel påverkar dataintegriteten, när det uppstår ett avbrott i Azure-regionen eller när ett program kräver underhåll. 

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-funktioner som du kan använda för att upprätthålla affärskontinuitet
SQL Database tillhandahåller flera funktioner för affärskontinuitet, inklusive automatiserade säkerhetskopieringar och valfri databasreplikering. Den uppskattade återställningstiden (ERT) och den potentiella dataförlusten för de senaste transaktionerna skiljer sig mellan de olika metoderna. Om du förstår de olika alternativen blir det enklare att välja mellan dem, och i de flesta fall kan du använda dem tillsammans för olika scenarier. När du utvecklar din affärskontinuitetsplan är det viktigt att du tittar på den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse – detta är ditt mål för återställningstid (RTO). Du måste också att fastställa det högsta antalet senaste datauppdateringar (tidsintervall) som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse – mål för återställningspunkt (RPO). 

I följande tabell jämförs ERT och RPO för de tre vanligaste scenarierna.

| Funktion | Basic-nivå | Standard-nivå | Premiumnivå |
| --- | --- | --- | --- |
| Återställning till tidpunkt från säkerhetskopia |En återställningspunkt inom 7 dagar |En återställningspunkt inom 35 dagar |En återställningspunkt inom 35 dagar |
| Geo-återställning från geo-replikerade säkerhetskopior |ERT < 12 timme, RPO < 1 timme |ERT < 12 timme, RPO < 1 timme |ERT < 12 timme, RPO < 1 timme |
| Återställning från Azure Backup Vault |ERT < 12 timme, RPO < 1 vecka |ERT < 12 timme, RPO < 1 vecka |ERT < 12 timme, RPO < 1 vecka |
| Aktiv geo-replikering |ERT < 30 sekunder, RPO < 5 sekunder |ERT < 30 sekunder, RPO < 5 sekunder |ERT < 30 sekunder, RPO < 5 sekunder |

### <a name="use-database-backups-to-recover-a-database"></a>Använda databassäkerhetskopior för att återställa en databas
SQL Database utför automatiskt en kombination av fullständiga databassäkerhetskopieringar varje vecka, differentiella databassäkerhetskopieringar varje timme och säkerhetskopieringar av transaktionsloggar var femte minut för att skydda ditt företag mot dataförlust. Dessa säkerhetskopior lagras i lokalt redundant lagring i 35 dagar för databaser på Standard- och Premium-servicenivåerna och i 7 dagar för databaser på Basic-tjänstnivån. Mer information om tjänstnivåer finns i avsnittet om [tjänstnivåer](sql-database-service-tiers.md). Om kvarhållningsperioden för din tjänstenivå inte uppfyller dina verksamhetskrav kan du öka kvarhållningsperioden genom att [byta tjänstnivå](sql-database-scale-up.md). De fullständiga och differentiella säkerhetskopieringarna replikeras också till ett [kopplat datacenter](../best-practices-availability-paired-regions.md) för skydd mot avbrott på datacentret. Mer information finns i avsnittet om [automatiska databassäkerhetskopieringar](sql-database-automated-backups.md).

Om den inbyggda kvarhållningsperioden inte är tillräcklig för ditt program kan du utöka den genom att konfigurera principen för långsiktig kvarhållning för dina databaser. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md). 

Du kan använda dessa automatiska databassäkerhetskopior för att återställa en databas från olika avbrottshändelser, både i ditt datacenter och till ett annat datacenter. Om du använder automatiska databassäkerhetskopieringar beror den beräknade återställningstiden på flera faktorer, inklusive det totala antalet databaser som återställs i samma region vid samma tidpunkt, databasens storlek, transaktionsloggarnas storlek och nätverksbandbredden. I de flesta fall är återställningstiden mindre än 12 timmar. När du återställer till en annan dataregion är den potentiella dataförlusten begränsad till 1 timme med geo-redundant lagring med differentiella säkerhetskopieringar varje timme. 

> [!IMPORTANT]
> Om du återställer med hjälp av automatisk säkerhetskopior måste du vara medlem i SQL Server-rollen Deltagare eller vara prenumerationsägaren. Mer information finns i [RBAC: Built-in roles](../active-directory/role-based-access-built-in-roles.md) (Rollbaserad åtkomstkontroll: Inbyggda roller). Du kan återställa med hjälp av Azure Portal, PowerShell eller REST-API:et. Du kan inte använda Transact-SQL.
> 
> 

Använd automatiska säkerhetskopieringar som din affärskontinuitets- och återställningsmetod om ditt program:

* Inte betraktas som verksamhetskritiskt.
* Inte har ett bindande SLA så att driftavbrott på 24 timmar eller längre inte resulterar i ekonomiska ansvarsskyldigheter.
* Har en låg frekvens av dataändringar (lågt antal transaktioner per timme) och en förlust på upp till en timmes ändringar är en acceptabel dataförlust. 
* Är kostnadskänsligt. 

Om du behöver snabbare återställning använder du [aktiv geo-replikering](sql-database-geo-replication-overview.md) (beskrivs nedan). Om du behöver kunna återställa data från en period som är äldre än 35 dagar bör du överväga att regelbundet arkivera databasen till en BACPAC-fil (en komprimerad fil som innehåller ditt databasschema och associerade data) som lagras i Azure Blob Storage eller på en annan valfri plats. Mer information om hur du skapar ett transaktionellt konsekvent databasarkiv finns i [Skapa en databaskopia](sql-database-copy.md) och [Exportera databaskopian](sql-database-export.md). 

### <a name="use-active-geo-replication-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Använda aktiv geo-replikering för att minska återställningstiden och begränsa dataförlusten som är associerad med en återställning
Förutom att använda databassäkerhetskopior för databasåterställning i händelse av ett verksamhetsavbrott kan du använda [aktiv geo-replikering](sql-database-geo-replication-overview.md) för att konfigurera en databas så att den har upp till fyra läsbara sekundära databaser i de regioner som du väljer. Dessa sekundära databaser synkroniseras med den primära databasen med hjälp av en asynkron replikeringsmekanism. Den här funktionen används för att skydda mot verksamhetsavbrott i händelse av ett avbrott på ett datacenter eller i samband med en programuppgradering. Aktiv geo-replikering kan också användas för att ge bättre frågeprestanda för skrivskyddade frågor till geografiskt spridda användare.

Om den primära databasen oväntat kopplas från eller om du behöver ta den offline för underhållsaktiviteter, kan du snabbt uppgradera en sekundär databas så att den fungerar som den primära databasen (även kallat redundansväxling) och konfigurera program så att de ansluter till den nyligen uppgraderade primära databasen. Vid en planerad redundansväxling förekommer ingen dataförlust. Vid en oplanerad redundansväxling kan en mindre mängd data gå förlorade för de senaste transaktionerna på grund av hur den asynkrona replikeringen fungerar. Efter en redundansväxling kan du senare växla tillbaka igen, antingen enligt en plan eller när datacentret är online igen. I samtliga fall upplever användarna ett kort avbrott och måste återansluta. 

> [!IMPORTANT]
> Om du vill använda aktiv geo-replikering måste du vara prenumerationsägaren eller ha administrativ behörighet i SQL Server. Du kan konfigurera och växla över med hjälp av Azure Portal, PowerShell eller med REST-API:et genom att använda behörigheter i prenumerationen eller genom att använda Transact-SQL med behörigheter i SQL Server.
> 
> 

Använd aktiv Geo-replikering om programmet uppfyller något av dessa villkor:

* Är verksamhetskritiskt.
* Har ett serviceavtal (SLA) som inte tillåter driftavbrott på 24 timmar eller mer.
* Avbrottstid leder till ekonomiska ansvarsskyldigheter.
* Har en hög frekvens av dataändringar och en timmes dataförlust är inte acceptabelt.
* Den extra kostnaden för aktiv geo-replikering är lägre än de potentiella ekonomiska skyldigheterna och den associerade affärsförlusten.

## <a name="recover-a-database-after-a-user-or-application-error"></a>Återställa en databas efter ett användar- eller programfel
* Ingen är perfekt! En användare kan oavsiktligt ta bort vissa data eller av misstag radera en viktig tabell eller till och med en hel databas. Och ett program kan av misstag skriva över värdefulla data med felaktiga data på grund av ett fel i programmet. 

I det här scenariot har du följande återställningsalternativ.

### <a name="perform-a-point-in-time-restore"></a>Utföra en återställning till en viss tidpunkt
Du kan använda automatiska säkerhetskopior för att återställa en kopia av databasen till en tidigare tidpunkt, förutsatt att tiden ligger inom databasens kvarhållningsperiod. När databasen har återställts kan du antingen ersätta den ursprungliga databasen med den återställda databasen eller kopiera nödvändiga data från den återställda informationen till den ursprungliga databasen. Om databasen använder aktiv geo-replikering rekommenderar vi att du kopierar nödvändiga data från den återställda kopian till den ursprungliga databasen. Om du ersätter den ursprungliga databasen med den återställda databasen måste du konfigurera om och omsynkronisera funktionen Aktiv geo-replikering (vilket kan ta tid för en stor databas). 

Mer information och detaljerade anvisningar som beskriver hur du återställer en databas till en viss tidpunkt med hjälp av Azure Portal eller PowerShell finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore). Du kan inte återställa med hjälp av Transact-SQL.

### <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
Om databasen tas bort, men den logiska servern inte har tagits bort, kan du återställa den borttagna databasen till den tidpunkt då den togs bort. Om du gör det återställs en säkerhetskopia av databasen till samma logiska SQL-server som den togs bort från. Du kan återställa den genom att använda det ursprungliga namnet eller ange ett nytt namn eller den återställda databasen.

Mer information och detaljerade anvisningar som beskriver hur du återställer en borttagen databas med hjälp av Azure Portal eller PowerShell finns i avsnittet om hur du [återställer en borttagen databas](sql-database-recovery-using-backups.md#deleted-database-restore). Du kan inte återställa med hjälp av Transact-SQL.

> [!IMPORTANT]
> Om den logiska servern tas bort kan du inte återställa en borttagen databas. 
> 
> 

### <a name="restore-from-azure-backup-vault"></a>Återställning från Azure Backup Vault
Om dataförlusten inträffade utanför den aktuella kvarhållningsperioden för automatiska säkerhetskopior och databasen har konfigurerats för långsiktig kvarhållning, kan du återställa från en veckovis säkerhetskopiering i Azure Backup Vault till en ny databas. I detta läge kan du antingen ersätta den ursprungliga databasen med den återställda databasen eller kopiera nödvändiga data från den återställda databasen till den ursprungliga databasen. Om du behöver hämta en äldre version av databasen innan en stor uppgradering, uppfylla en begäran från revisorer eller juridiska krav, kan du skapa en ny databas med hjälp av en fullständig säkerhetskopia som sparats i Azure Backup Vault.  Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Återställa en databas till en annan region från ett avbrott på ett regionalt Azure-datacenter
<!-- Explain this scenario -->

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar. 

* Ett alternativ är att vänta tills databasen är tillbaka online när avbrottet på datadatacentret är över. Det här fungerar för program som har råd att ha databasen offline. Till exempel ett utvecklingsprojekt eller en kostnadsfri utvärderingsversion som du inte behöver arbeta med hela tiden. När ett datacenter har ett avbrott vet du inte hur länge felet varar, så det här alternativet fungerar bara om du inte behöver databasen på ett tag.
* Ett annat alternativ är att växla över till en annan dataregion om du använder aktiv geo-replikering eller att återställa med geo-redundanta databassäkerhetskopior (geo-återställning). Redundansväxlingar tar bara några sekunder, medan återställningar från säkerhetskopior tar flera timmar.

Hur lång tid det tar att återställa data och hur mycket data som går förlorade i händelse av ett avbrott på datacentret beror på hur du väljer att använda funktionerna för affärskontinuitet som beskrivs ovan i ditt program. Du kan använda en kombination av databassäkerhetskopior och aktiv geo-replikering beroende på dina programkrav. Information om designöverväganden för fristående databaser och för elastiska pooler när dessa funktioner för affärskontinuitet används finns i [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Utforma ett program för haveriberedskap i molnet) och [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Haveriberedskapsstrategier med en elastisk pool).

Avsnittet nedan innehåller en översikt över de steg du följer när du återställer data med hjälp av databassäkerhetskopior eller aktiv geo-replikering. Detaljerade anvisningar, inklusive planeringskrav, steg som du utför efter återställningen samt information om hur du simulerar ett avbrott för att göra ett programåterställningstest, finns i [Recover a SQL Database from an outage](sql-database-disaster-recovery.md) (Återställa en SQL-databas från ett avbrott).

### <a name="prepare-for-an-outage"></a>Förbereda för ett avbrott
Oavsett vilken funktion för affärskontinuitet du använder, måste du:

* Identifiera och förbered målservern, inklusive brandväggsregler på servernivå, inloggningar och behörigheter på huvuddatabasnivå.
* Bestämma hur du ska omdirigera klienter och klientprogram till den nya servern.
* Dokumentera andra beroenden, till exempel granskningsinställningar och aviseringar. 

Om du inte planerar och förbereder dig väl tar det extra lång tid att ansluta dina program efter en redundansväxling eller återställning. Dessutom krävs antagligen felsökning i en redan stressig situation. Ingen bra kombination.

### <a name="failover-to-a-geo-replicated-secondary-database"></a>Växla över till en geo-replikerad sekundär databas
Om du använder aktiv geo-replikering som återställningsmetod [framtvingar du en redundansväxling till en sekundär geo-replikerad databas](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). Inom några sekunder uppgraderas den sekundära databasen så att den fungerar som den nya primära databasen och är redo att registrera nya transaktioner och svara på eventuella frågor – med bara några få sekunders dataförlust av data som inte har replikerats än. Information om hur du automatiserar redundansväxlingen finns i [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Utforma ett program för haveriberedskap i molnet).

> [!NOTE]
> När datacentret är online igen kan du växla tillbaka till den ursprungliga primära databasen (eller inte).
> 
> 

### <a name="perform-a-geo-restore"></a>Utföra en geo-återställning
Om du använder automatiska säkerhetskopieringar med geo-redundant lagringsreplikering som återställningsmetod [startar du en databasåterställning med hjälp av geo-återställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Återställningen sker vanligtvis inom 12 timmar – med en dataförlust på upp till en timme beroende på när den senaste differentiella säkerhetskopieringen per timme gjordes och replikerades. Databasen kan inte registrera några transaktioner eller svara på frågor förrän återställningen har slutförts. 

> [!NOTE]
> Om datacentret hinner komma online igen innan du växlar över programmet till den återställda databasen, kan du bara avbryta återställningen.  
> 
> 

### <a name="perform-post-failover--recovery-tasks"></a>Utföra åtgärder efter en redundansväxling eller återställning
Efter återställningen från endera återställningsmetod måste du utföra följande ytterligare uppgifter innan dina användare och program kan komma igång igen:

* Omdirigera klienter och klientprogram till den nya servern och återställda databasen
* Kontrollera att lämpliga brandväggsregler på servernivå är på plats så att användarna kan ansluta (eller använd [brandväggsregler på databasnivå](sql-database-firewall-configure.md#creating-database-level-firewall-rules))
* Se till att rätt inloggningar och behörigheter på huvuddatabasnivå är på plats (eller använd [inneslutna användare](https://msdn.microsoft.com/library/ff929188.aspx))
* Konfigurera granskning efter behov
* Konfigurera aviseringar efter behov

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uppgradera ett program med minimal avbrottstid
Ibland måste ett program tas offline på grund av planerat underhåll, till exempel i samband med en programuppgradering. [Manage application upgrades](sql-database-manage-application-rolling-upgrade.md) (Hantera programuppgraderingar) beskriver hur du använder aktiv geo-replikering för att möjliggöra löpande uppgraderingar av ditt molnprogram med minimal avbrottstid under uppgraderingarna samt för att etablera en återställningsväg i händelse av problem. I den här artikeln tittar vi på två olika metoder för att samordna uppgraderingsprocessen och beskriver fördelarna och nackdelarna med respektive alternativ.

## <a name="next-steps"></a>Nästa steg
En beskrivning av designöverväganden för fristående databaser och för elastiska pooler finns i [Design an application for cloud disaster recovery](sql-database-designing-cloud-solutions-for-disaster-recovery.md) (Utforma ett program för haveriberedskap i molnet) och [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Haveriberedskapsstrategier med en elastisk pool).




<!--HONumber=Dec16_HO2-->


