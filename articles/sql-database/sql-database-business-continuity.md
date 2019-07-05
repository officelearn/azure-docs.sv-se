---
title: Affärskontinuitet i molnet – databasåterställning – SQL Database | Microsoft Docs
description: Lär dig hur Azure SQL Database stöder databasåterställning och affärskontinuitet i molnet och hur du kan köra verksamhetskritiska molnprogram.
keywords: affärskontinuitet, molnaffärskontinuitet, databashaveriberedskap, databasåterställning
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 06/25/2019
ms.openlocfilehash: 2f3723e0a1b14edd6f516f3cc080501bea80d486
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442042"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Översikt över affärskontinuitet med Azure SQL Database

**Affärskontinuitet** refererar till den mekanismer, principer och procedurer som gör att ditt företag även om störningar, särskilt i sin infrastruktur för databehandling i Azure SQL Database. I de flesta fall hanterar störande händelser som kan inträffa i molnmiljön och se till att program och processer som körs i Azure SQL Database. Det finns dock vissa störande händelser som automatiskt som inte kan hanteras av SQL-databas:

- Användaren har tagits bort av misstag eller uppdatera en rad i en tabell.
- Angripare är klar att ta bort data eller ta bort en databas.
- Jordbävning orsakade ett strömavbrott och tillfälligt inaktiverad datacenter.

Den här översikten beskriver de funktioner som Azure SQL Database tillhandahåller för affärskontinuitet och haveriberedskap. Läs mer om alternativ, rekommendationer och självstudier för att återställa avbrottshändelser som kan leda till dataförlust eller göra databasen och programmet otillgängliga. Lär dig vad du ska göra när en användare eller ett program fel påverkar dataintegriteten, uppstår ett avbrott i en Azure-region eller ditt program kräver underhåll.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-funktioner som du kan använda för att upprätthålla affärskontinuitet

Databas ur finns det fyra viktiga potentiella avbrott scenarier:

- Lokala maskinvaru- eller fel som påverkar databasnoden, till exempel en diskenhet fel.
- Skadade data eller ta bort vanligtvis orsakats av programmet bugg eller mänskliga fel. Sådana fel är programspecifika och vanligtvis inte kan identifieras av tjänsten.
- Datacenter-avbrott kan orsakas av att en naturkatastrof. Det här scenariot kräver någon form av geo-redundans med programmet redundans till ett alternativt datacenter.
- Uppgradering eller underhåll fel oförutsedda problem som uppstår under planerat underhåll av infrastrukturen eller uppgraderingar kan kräva snabb återställning till en tidigare databastillstånd.

För att lösa lokal maskinvara och programvarufel, SQL Database innehåller en [arkitektur med hög tillgänglighet](sql-database-high-availability.md), som garanterar att automatisk återställning från de här felen med upp till 99.995% serviceavtal.  

För att skydda ditt företag mot dataförlust, SQL-databas ska skapas automatiskt fullständiga databassäkerhetskopieringar varje vecka, differentiella databassäkerhetskopieringar var 12: e timme och transaktionen loggsäkerhetskopior var 5 – 10 minuter. Säkerhetskopiorna lagras i RA-GRS-lagring i minst 7 dagar för alla tjänstnivåer. Alla tjänstnivåer utom Basic stöder konfigurerbara kvarhållningsperiod för point-in-time-återställning, upp till 35 dagar. 

SQL Database tillhandahåller också flera funktioner för affärskontinuitet, som du kan använda för att minimera olika oplanerad scenarier. 

- Med [temporära tabeller](sql-database-temporal-tables.md) kan du återställa radversioner från valfri tidpunkt.
- [Inbyggda automatiska säkerhetskopior](sql-database-automated-backups.md) och [tidpunkt för återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore) gör det möjligt att återställa fullständig databasen till vissa inom den konfigurera bevarandeperioden upp till 35 dagar.
- Du kan [återställa en borttagen databas](sql-database-recovery-using-backups.md#deleted-database-restore) till den tidpunkt då den togs bort om den **SQL Database-server inte har tagits bort**.
- [Långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) gör det möjligt för dig att hålla säkerhetskopior till 10 år.
- [Aktiv geo-replikering](sql-database-active-geo-replication.md) kan du skapa läsbara repliker och manuellt växla över till alla repliker vid en data center avbrott eller programmet uppgradering.
- [Automatisk redundans grupp](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) kan programmet automatiskt återhämtning vid avbrott i datacentret.

## <a name="recover-a-database-within-the-same-azure-region"></a>Återställa en databas i samma Azure-region

Du kan använda automatiska databassäkerhetskopieringar för att återställa en databas till en tidpunkt i förflutna. På så sätt kan du återställa från skadade beror på mänskliga fel. Paketåtkomst-in-time-återställning kan du skapa en ny databas på samma server som representerar tillståndet för data före felaktig händelsen. För de flesta databaser återställningsåtgärden tar mindre än 12 timmar. Det kan ta längre tid att återställa en databas med mycket stora eller mycket aktiv. Mer information om tiden för återställning finns i [databasen återställningstid](sql-database-recovery-using-backups.md#recovery-time). 

Om maximala stöds kvarhållningsperiod för point-in-time-återställning (PITR) inte är tillräckligt för ditt program kan du utöka den genom att konfigurera en långsiktig kvarhållning av säkerhetskopior (LTR)-princip för databaserna. Mer information finns i [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-azure-region"></a>Återställa en databas till en annan Azure-region

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar.

- Ett alternativ är att vänta tills databasen är tillbaka online när avbrottet på datadatacentret är över. Det här fungerar för program som har råd att ha databasen offline. Till exempel ett utvecklingsprojekt eller en kostnadsfri utvärderingsversion som du inte behöver arbeta med hela tiden. När det uppstår ett avbrott i ett datacenter, vet inte du hur länge driftstörningarna kan vara, så det här alternativet fungerar bara om du inte behöver databasen på ett tag.
- Ett annat alternativ är att återställa en databas på en server i alla Azure-region med [geo-redundanta databassäkerhetskopior](sql-database-recovery-using-backups.md#geo-restore) (geo-återställning). GEO-återställning använder en geo-redundant säkerhetskopia som källa och kan användas för att återställa en databas, även om databasen eller datacenter är otillgängligt på grund av ett avbrott.
- Slutligen kan du snabbt kan återställa från ett avbrott om du har konfigurerat antingen geo-secondary med hjälp av [aktiv geo-replikering](sql-database-active-geo-replication.md) eller en [automatisk redundans grupp](sql-database-auto-failover-group.md) för din databas eller databaser. Du kan använda antingen manuell eller automatisk redundans beroende på ditt val av dessa tekniker. Även om redundans själva tar bara några sekunder, tar tjänsten minst 1 timme för att aktivera den. Detta är nödvändigt att säkerställa att växling vid fel är berättigade av skalan om avbrottet. Dessutom kan redundansen resultera i små dataförlust på grund av asynkron replikering. 

När du utvecklar din affärskontinuitetsplan är det viktigt att du känner till den längsta godkända tiden innan programmet är helt återställt efter en avbrottshändelse. Den tid som krävs för programmet för att återställa kallas återställningstid (RTO). Du måste också att förstå den längsta tid för senaste datauppdateringar (tidsintervall) som programmet kan tolerera att förlora när du återställer från en oplanerad avbrottshändelse. Den potentiella dataförlusten kallas mål för återställningspunkt (RPO).

Olika återställningsmetoder erbjuder olika nivåer av RPO och RTO. Du kan välja en specifik återställningsmetod eller använda en kombination av metoder för att achicethe fullständig programåterställning. I följande tabell jämförs RPO och RTO med varje återställningsalternativ för.

| Återställningsmetod | MÅL FÖR ÅTERSTÄLLNINGSTID | RPO |
| --- | --- | --- | 
| GEO-återställning från geo-replikerade säkerhetskopior | 12 timmar | för 1 timma |
| Automatiska redundansgrupper | för 1 timma | 5 s |
| Manuell databasredundans | 30 s | 5 s |

> [!NOTE]
> *Manuell databasredundans* refererar till redundans för en enkel databas till dess geo-replikerad sekundär med hjälp av den [oplanerad läge](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Se tabellen tidigare i den här artikeln för information om automatisk redundans RTO och RPO.


Använda grupper för automatisk redundans om programmet uppfyller något av dessa villkor:

- Är verksamhetskritiskt.
- Har ett servicenivåavtal (SLA) som inte tillåter 12 timmar eller mer stillestånd.
- Avbrott kan resultera i ekonomiska ansvarsskyldigheter.
- Har en hög andel data som ändras och 1 timmes dataförlust kan inte godkännas.
- Den extra kostnaden för aktiv geo-replikering är lägre än de potentiella ekonomiska skyldigheterna och den associerade affärsförlusten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Du kan välja att använda en kombination av databassäkerhetskopior och aktiv geo-replikering beroende på dina programkrav. En beskrivning av designöverväganden för fristående databaser och för elastiska pooler när dessa funktioner för affärskontinuitet, finns i [utforma ett program för katastrofåterställning i molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och [haveriberedskap för elastisk pool strategier för](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Följande avsnitt innehåller en översikt över stegen för att återställa med hjälp av databassäkerhetskopior eller aktiv geo-replikering. Detaljerade anvisningar för planering av krav, steg efter återställning och information om hur du simulerar ett avbrott för att utföra ett programåterställningstest, i [återställa en SQL-databas från ett avbrott](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Förbereda för ett avbrott

Oavsett vilken funktion för affärskontinuitet du använder, måste du:

- Identifiera och Förbered målservern, inklusive IP-brandväggsregler på servernivå, inloggningar och på huvuddatabasnivå.
- Bestämma hur du ska omdirigera klienter och klientprogram till den nya servern.
- Dokumentera andra beroenden, till exempel granskningsinställningar och aviseringar.

Om du inte framställer korrekt, att dina program efter en redundansväxling eller en databasåterställning tar extra tid och förmodligen även kräva felsökning i taget stress - ingen bra kombination.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Växla över till en geo-replikerad sekundär databas

Om du använder aktiv geo-replikering eller grupper för automatisk redundans som återställningsmetod, du kan konfigurera en princip för automatisk redundans eller använda [manuell oplanerad redundans](sql-database-active-geo-replication-portal.md#initiate-a-failover). När åtgärden har startat orsakar växling vid fel sekundärt du blir den nya primärt och är redo att registrera nya transaktioner och svara på frågor – med minimal dataförlust för de data som inte har replikerats. Information om hur du utformar redundansprocessen finns i [utforma ett program för katastrofåterställning i molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> När datacentret är online igen återansluta till den nya primärt gamla USA: s presidentval automatiskt och blir sekundära databaser. Om du vill flytta den primära tillbaka till den ursprungliga regionen kan du initiera en planerad redundans manuellt (återställning).

### <a name="perform-a-geo-restore"></a>Utföra en geo-återställning

Om du använder automatiska säkerhetskopieringar med geo-redundant lagring (aktiverat som standard), kan du återställa en databas med hjälp av [geo-återställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Återställningen sker vanligtvis inom 12 timmar – med dataförlust på upp till en timme systemets när den senaste säkerhetskopian av loggen har tagit och replikeras. Databasen kan inte registrera några transaktioner eller svara på frågor förrän återställningen har slutförts. Observera att geo-återställning bara återställs databasen till den senaste tillgängliga tidpunkten.

> [!NOTE]
> Om datacentret är online igen innan du växlar över programmet till den återställda databasen, kan du avbryta återställningen.

### <a name="perform-post-failover--recovery-tasks"></a>Utföra åtgärder efter en redundansväxling eller återställning

Efter återställningen från endera återställningsmetod måste du utföra följande ytterligare uppgifter innan dina användare och program kan komma igång igen:

- Omdirigera klienter och klientprogram till den nya servern och återställda databasen
- Kontrollera att rätt IP-brandväggsregler för på servernivå är uppfyllda för användare att ansluta eller använda [databasnivå](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-the-azure-portal) att aktivera lämpliga regler.
- Se till att rätt inloggningar och behörigheter på huvuddatabasnivå är på plats (eller använd [inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Konfigurera granskning efter behov
- Konfigurera aviseringar efter behov

> [!NOTE]
> Om du använder en redundansgrupp och ansluta till databaser med läs-och lstener sker omdirigering efter en redundansväxling automatiskt och transparent för programmet.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uppgradera ett program med minimal avbrottstid

Ibland måste ett program kopplas från på grund av planerat underhåll, till exempel en uppgradering av programmet. [Hantera programuppgraderingar](sql-database-manage-application-rolling-upgrade.md) beskriver hur du använder aktiv geo-replikering för att möjliggöra löpande uppgraderingar av ditt molnprogram minimal avbrottstid under uppgraderingarna och etablera en återställningsväg om något går fel.

## <a name="next-steps"></a>Nästa steg

En beskrivning av designöverväganden för fristående databaser och för elastiska pooler finns i [utforma ett program för katastrofåterställning i molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och [strategier för elastisk pool haveriberedskap](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
