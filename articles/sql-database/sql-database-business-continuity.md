---
title: Affärskontinuitet i molnet – databasåterställning – SQL Database | Microsoft Docs
description: Lär dig hur Azure SQL Database stöder databasåterställning och affärskontinuitet i molnet och hur du kan köra verksamhetskritiska molnprogram.
keywords: affärskontinuitet, molnaffärskontinuitet, databashaveriberedskap, databasåterställning
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: b6708dac548db9e11d1092a6b84083d057401176
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237678"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Översikt över affärskontinuitet med Azure SQL Database

Azure SQL Database är en implementering av den senaste stabila SQL Server Database Engine konfigureras och optimerad för Azure-molnmiljö som ger [hög tillgänglighet](sql-database-high-availability.md) och återställning vid fel som kan påverka din affärsprocess. **Affärskontinuitet** refererar till de mekanismer, principer och procedurer som gör det möjligt för ett företag som även körs vid störningar, särskilt i sin infrastruktur för databehandling i Azure SQL Database.  I de flesta fall hanterar Azure SQL Database de störande händelser som kan inträffa i molnmiljön och hålla dina affärsprocesser som körs. Det finns dock vissa störande händelser som inte kan hanteras av SQL-databas som:
 - Användaren har tagits bort av misstag eller uppdatera en rad i en tabell.
 - Angripare är klar att ta bort data eller ta bort en databas.
 - Jordbävning orsakade ett strömavbrott och tillfälligt inaktiverad datacenter.
 
Dessa fall kan inte styras av Azure SQL Database, så du behöver du använda funktionerna för affärskontinuitet i SQL-databas som hjälper dig att återställa dina data och hålla igång dina program.

Den här översikten beskriver de funktioner som Azure SQL Database tillhandahåller för affärskontinuitet och haveriberedskap. Läs mer om alternativ, rekommendationer och självstudier för att återställa avbrottshändelser som kan leda till dataförlust eller göra databasen och programmet otillgängliga. Lär dig vad du ska göra när en användare eller ett program fel påverkar dataintegriteten, uppstår ett avbrott i en Azure-region eller ditt program kräver underhåll.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-funktioner som du kan använda för att upprätthålla affärskontinuitet

Databas ur finns det fyra viktiga potentiella avbrott scenarier:
- **Lokala maskinvaru- eller fel** påverkar databasnoden, till exempel en diskenhet fel.
- **Skadade data eller borttagning** – vanligtvis orsakats av en bugg i programmet eller mänskliga fel.  Sådana fel är är programspecifika och kan inte som en regel att har identifierats eller minskas automatiskt av infrastruktur.
- **Datacenter-avbrott**, troligtvis orsakats av en naturkatastrof.  Det här scenariot kräver någon form av geo-redundans med programmet redundans till ett alternativt datacenter.
- **Uppgradering eller underhåll fel** – oförutsedda problem som uppstår under planerade uppgraderingar eller Underhåll ett program eller en databas kan kräva snabb återställning till en tidigare databastillstånd.

SQL Database tillhandahåller flera funktioner för affärskontinuitet, inklusive automatiserade säkerhetskopieringar och valfri databasreplikering som kan minska dessa scenarier. Först måste du förstå hur SQL-databas [arkitektur med hög tillgänglighet](sql-database-high-availability.md) ger 99,99% tillgänglighet och återhämtning till vissa störande händelser som kan påverka dina affärsprocesser.
Sedan kan du lära dig om de ytterligare mekanismer som du kan använda för att återställa avbrottshändelser som inte kan hanteras av arkitektur för SQL-databas med hög tillgänglighet, till exempel:
 - [Temporala tabeller](sql-database-temporal-tables.md) gör det möjligt att återställa radversioner från valfri punkt i tiden.
 - [Inbyggda automatiska säkerhetskopior](sql-database-automated-backups.md) och [tidpunkt för återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore) gör det möjligt att återställa fullständig till någon gång under de senaste 35 dagarna.
 - Du kan [återställa en borttagen databas](sql-database-recovery-using-backups.md#deleted-database-restore) till den tidpunkt då den togs bort om den **logisk server inte har tagits bort**.
 - [Långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) gör det möjligt för dig att hålla säkerhetskopior till 10 år.
 - [Automatisk redundans grupp](sql-database-geo-replication-overview.md#auto-failover-group-capabilities) kan programmet automatiskt återhämtning vid avbrott på datacentret skala.

Den uppskattade återställningstiden (ERT) och den potentiella dataförlusten för de senaste transaktionerna skiljer sig mellan de olika metoderna. Om du förstår de olika alternativen blir det enklare att välja mellan dem, och i de flesta fall kan du använda dem tillsammans för olika scenarier. När du utvecklar din affärskontinuitetsplan måste du förstå den högsta acceptabla tiden innan programmet är helt återställt efter en avbrottshändelse. Den tid som krävs för programmet för att återställa kallas återställningstid (RTO). Du måste också att förstå den längsta tid för senaste datauppdateringar (tidsintervall) som programmet kan tolerera att förlora när det återställs efter en avbrottshändelse. Tid då uppdateringar som du kanske har råd att förlora kallas mål för återställningspunkt (RPO).

I följande tabell jämförs ERT och RPO för varje tjänstnivå för de tre vanligaste scenarierna.

| Funktion | Basic | Standard | Premium  | Generellt syfte | Affärskritisk
| --- | --- | --- | --- |--- |--- |
| Återställning till tidpunkt från säkerhetskopia |En återställningspunkt inom sju dagar |En återställningspunkt inom 35 dagar |En återställningspunkt inom 35 dagar |En återställningspunkt inom konfigurerade period (upp till 35 dagar)|En återställningspunkt inom konfigurerade period (upp till 35 dagar)|
| GEO-återställning från geo-replikerade säkerhetskopior |ERT < 12 timme<br> RPO < 1 timme |ERT < 12 timme<br>RPO < 1 timme |ERT < 12 timme<br>RPO < 1 timme |ERT < 12 timme<br>RPO < 1 timme|ERT < 12 timme<br>RPO < 1 timme|
| Automatisk redundans grupper |RTO = 1 timme<br>RPO < 5 SEK |RTO = 1 timme<br>RPO < 5 s |RTO = 1 timme<br>RPO < 5 s |RTO = 1 timme<br>RPO < 5 s|RTO = 1 timme<br>RPO < 5 s|

## <a name="recover-a-database-to-the-existing-server"></a>Återställa en databas på den befintliga servern

SQL Database utför automatiskt en kombination av fullständiga databassäkerhetskopieringar varje vecka, differentiella säkerhetskopieringar som är allmänt var tolfte timme och transaktionen loggsäkerhetskopior var 5 – 10 minuter för att skydda ditt företag mot dataförlust. Säkerhetskopiorna lagras i RA-GRS-lagring i 35 dagar för alla tjänstnivåer förutom grundläggande DTU tjänstnivåerna där säkerhetskopiorna lagras i 7 dagar. Mer information finns i [automatiska databassäkerhetskopieringar](sql-database-automated-backups.md). Du kan återställa ett befintligt databasformulär automatiska säkerhetskopior till en tidigare tidpunkt som en ny databas på samma logiska server med hjälp av Azure portal, PowerShell eller REST API. Mer information finns i [Point-in-time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore).

Om den maximala stöds point-in-time-återställning (PITR) kvarhållningsperioden är inte tillräcklig för ditt program, kan du utöka den genom att konfigurera en långsiktig kvarhållning av säkerhetskopior (LTR)-princip för databaserna. Mer information finns i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).

Du kan använda dessa automatiska databassäkerhetskopior för att återställa en databas från olika avbrottshändelser, både i ditt datacenter och till ett annat datacenter. Om du använder automatiska databassäkerhetskopieringar beror den beräknade återställningstiden på flera faktorer, inklusive det totala antalet databaser som återställs i samma region vid samma tidpunkt, databasens storlek, transaktionsloggarnas storlek och nätverksbandbredden. Tiden för återställning är vanligtvis mindre än 12 timmar. Det kan ta längre tid att återställa en databas med mycket stora eller active. Mer information om tiden för återställning finns i [databasen återställningstid](sql-database-recovery-using-backups.md#recovery-time). När du återställer till en annan dataregion är den potentiella dataförlusten begränsad till 1 timme med användning av geo-redundanta säkerhetskopieringar.

Använd automatiska säkerhetskopieringar och [point-in-time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore) som ditt företag affärskontinuitets- och återställningsmetod om ditt program:

* Inte betraktas som verksamhetskritiskt.
* Har ett bindande SLA - ett driftavbrott på 24 timmar eller inte resulterar längre i ekonomiska ansvarsskyldigheter.
* Har en låg frekvens av dataändringar (lågt antal transaktioner per timme) och en förlust på upp till en timmes ändringar är en acceptabel dataförlust.
* Är kostnadskänsligt.

Om du behöver snabbare återställning använder [redundansgrupper](sql-database-geo-replication-overview.md#auto-failover-group-capabilities
) (beskrivs nedan). Om du behöver för att kunna återställa data från en period som är äldre än 35 dagar eller använda [långsiktig kvarhållning](sql-database-long-term-retention.md). 

## <a name="recover-a-database-to-another-region"></a>Återställa en databas till en annan region
<!-- Explain this scenario -->

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar.

* Ett alternativ är att vänta tills databasen är tillbaka online när avbrottet på datadatacentret är över. Det här fungerar för program som har råd att ha databasen offline. Till exempel ett utvecklingsprojekt eller en kostnadsfri utvärderingsversion som du inte behöver arbeta med hela tiden. När det uppstår ett avbrott i ett datacenter, vet inte du hur länge driftstörningarna kan vara, så det här alternativet fungerar bara om du inte behöver databasen på ett tag.
* Ett annat alternativ är att återställa en databas på en server i alla Azure-region med [geo-redundanta databassäkerhetskopior](sql-database-recovery-using-backups.md#geo-restore) (geo-återställning). GEO-återställning använder en geo-redundant säkerhetskopia som källa och kan användas för att återställa en databas, även om databasen eller datacenter är otillgängligt på grund av ett avbrott.
* Slutligen kan du snabbt kan återställa från ett avbrott om du har konfigurerat en [automatisk redundans grupp](sql-database-geo-replication-overview.md#auto-failover-group-capabilities) för din databas eller databaser. Du kan anpassa princip för redundansväxling för att använda automatisk eller manuell växling vid fel. Även om redundans själva tar bara några sekunder, tar tjänsten minst 1 timme för att aktivera den. Detta är nödvändigt att säkerställa att växling vid fel är berättigade av skalan om avbrottet. Dessutom kan redundansen resultera i små dataförlust på grund av asynkron replikering. Se tabellen tidigare i den här artikeln för information om automatisk redundans RTO och RPO.   

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>


> [!IMPORTANT]
> Om du vill använda aktiv geo-replikering och automatisk redundans grupper, måste du vara prenumerationsägaren eller ha administrativ behörighet i SQL Server. Du kan konfigurera och redundansväxla med Azure portal, PowerShell eller REST-API med hjälp av Azure-Prenumerationsbehörigheter eller med hjälp av Transact-SQL med SQL Server-behörigheter.
> 

Använda grupper i active automatisk redundans om programmet uppfyller något av dessa villkor:

* Är verksamhetskritiskt.
* Har ett servicenivåavtal (SLA) som inte tillåter 12 timmar eller mer stillestånd.
* Avbrott kan resultera i ekonomiska ansvarsskyldigheter.
* Har en hög andel data som ändras och 1 timmes dataförlust kan inte godkännas.
* Den extra kostnaden för aktiv geo-replikering är lägre än de potentiella ekonomiska skyldigheterna och den associerade affärsförlusten.

När du utför en åtgärd, hur lång tid det tar att återställa och hur mycket data går förlorade beror på hur du väljer att använda dessa funktioner för affärskontinuitet i ditt program. Verkligen, kan du välja att använda en kombination av databassäkerhetskopior och aktiv geo-replikering beroende på dina programkrav. En beskrivning av designöverväganden för fristående databaser och för elastiska pooler när dessa funktioner för affärskontinuitet, finns i [utforma ett program för katastrofåterställning i molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och [elastisk lagringspoolen strategier för haveriberedskap](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

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
Om du använder automatiska säkerhetskopieringar med geo-redundant lagring (aktiverat som standard), kan du återställa en databas med hjälp av [geo-återställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Återställningen sker vanligtvis inom 12 timmar – med dataförlust på upp till en timme systemets när den senaste säkerhetskopian av loggen har tagit och replikeras. Databasen kan inte registrera några transaktioner eller svara på frågor förrän återställningen har slutförts. Observera att geo-återställning bara återställs databasen till den senaste tillgängliga tidpunkten.

> [!NOTE]
> Om datacentret är online igen innan du växlar över programmet till den återställda databasen, kan du avbryta återställningen.  

### <a name="perform-post-failover--recovery-tasks"></a>Utföra åtgärder efter en redundansväxling eller återställning
Efter återställningen från endera återställningsmetod måste du utföra följande ytterligare uppgifter innan dina användare och program kan komma igång igen:

* Omdirigera klienter och klientprogram till den nya servern och återställda databasen
* Kontrollera att lämpliga brandväggsregler på servernivå är på plats så att användarna kan ansluta (eller använd [brandväggsregler på databasnivå](sql-database-firewall-configure.md#creating-and-managing-firewall-rules))
* Se till att rätt inloggningar och behörigheter på huvuddatabasnivå är på plats (eller använd [inneslutna användare](https://msdn.microsoft.com/library/ff929188.aspx))
* Konfigurera granskning efter behov
* Konfigurera aviseringar efter behov

> [!NOTE]
> Om du använder en redundansgrupp och ansluta till databaser med läs-och lstener sker omdirigering efter en redundansväxling automatiskt och transparent för programmet.  
>
>

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uppgradera ett program med minimal avbrottstid
Ibland måste ett program kopplas från på grund av planerat underhåll, till exempel en uppgradering av programmet. [Hantera programuppgraderingar](sql-database-manage-application-rolling-upgrade.md) beskriver hur du använder aktiv geo-replikering för att möjliggöra löpande uppgraderingar av ditt molnprogram minimal avbrottstid under uppgraderingarna och etablera en återställningsväg om något går fel. 

## <a name="next-steps"></a>Nästa steg
En beskrivning av designöverväganden för fristående databaser och för elastiska pooler finns i [utforma ett program för katastrofåterställning i molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och [strategier för elastisk pool haveriberedskap](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
