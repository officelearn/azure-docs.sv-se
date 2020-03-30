---
title: Molnaffärskontinuitet - databasåterställning
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
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096860"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Översikt över affärskontinuitet med Azure SQL Database

**Affärskontinuitet** i Azure SQL Database refererar till de mekanismer, principer och procedurer som gör att ditt företag kan fortsätta att fungera inför avbrott, särskilt till dess datorinfrastruktur. I de flesta fall hanterar Azure SQL Database de störande händelser som kan inträffa i molnmiljön och håller dina program och affärsprocesser igång. Det finns dock vissa störande händelser som inte kan hanteras av SQL Database automatiskt, till exempel:

- Användaren har av misstag tagit bort eller uppdaterat en rad i en tabell.
- Illvillig angripare lyckades ta bort data eller släppa en databas.
- Jordbävning orsakade ett strömavbrott och tillfälligt inaktiverat datacenter.

Den här översikten beskriver de funktioner som Azure SQL Database tillhandahåller för affärskontinuitet och haveriberedskap. Lär dig mer om alternativ, rekommendationer och självstudier för återställning från störande händelser som kan orsaka dataförlust eller orsaka att databasen och programmet blir otillgängliga. Lär dig vad du kan göra när en användare eller ett programfel påverkar dataintegriteten, en Azure-region har ett avbrott eller ditt program kräver underhåll.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-funktioner som du kan använda för att upprätthålla affärskontinuitet

Ur ett databasperspektiv finns det fyra stora potentiella störningsscenarier:

- Lokala maskinvaru- eller programvarufel som påverkar databasnoden, till exempel ett diskenhetsfel.
- Data korruption eller radering som vanligtvis orsakas av ett programfel eller mänskliga fel. Sådana fel är programspecifika och kan vanligtvis inte identifieras av databastjänsten.
- Datacenter avbrott, eventuellt orsakas av en naturkatastrof. Det här scenariot kräver en viss nivå av geo-redundans med program redundans till ett alternativt datacenter.
- Uppgraderings- eller underhållsfel, oförutsedda problem som uppstår under planerat underhåll av infrastruktur eller uppgraderingar kan kräva snabb återställning till ett tidigare databastillstånd.

För att minska de lokala maskinvaru- och programvarufelen innehåller SQL Database en arkitektur med [hög tillgänglighet](sql-database-high-availability.md), vilket garanterar automatisk återställning från dessa fel med upp till 99,995 % tillgänglighetsslalan.  

För att skydda ditt företag från dataförlust skapar SQL Database automatiskt fullständiga säkerhetskopieringar av databaser varje vecka, differentiella säkerhetskopieringar av databaser var 12:e timme och säkerhetskopiering av transaktionsloggar var 5-10:e minut . Säkerhetskopiorna lagras i RA-GRS-lagring i minst 7 dagar för alla tjänstnivåer. Alla tjänstnivåer utom Grundläggande stöder konfigurerbar lagringsperiod för säkerhetskopiering för återställning i point-in-time, upp till 35 dagar. 

SQL Database innehåller också flera funktioner för affärskontinuitet, som du kan använda för att minska olika oplanerade scenarier. 

- Med [temporära tabeller](sql-database-temporal-tables.md) kan du återställa radversioner från valfri tidpunkt.
- [Med inbyggda automatiska säkerhetskopior](sql-database-automated-backups.md) och [Point in Time Restore](sql-database-recovery-using-backups.md#point-in-time-restore) kan du återställa hela databasen till någon tidpunkt inom den konfigurerade kvarhållningsperioden upp till 35 dagar.
- Du kan [återställa en borttagen databas](sql-database-recovery-using-backups.md#deleted-database-restore) till den punkt där den togs bort om SQL **Database-servern inte har tagits bort**.
- [Långsiktig lagring av säkerhetskopiering](sql-database-long-term-retention.md) gör att du kan hålla säkerhetskopiorna upp till 10 år.
- [Med aktiv georeplikering](sql-database-active-geo-replication.md) kan du skapa läsbara repliker och manuellt växla över till en replik vid avbrott i datacenter eller programuppgradering.
- [Med gruppen Automatisk redundans](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) kan programmet automatiskt återställas vid avbrott i ett datacenter.

## <a name="recover-a-database-within-the-same-azure-region"></a>Återställa en databas inom samma Azure-region

Du kan använda automatiska säkerhetskopieringar av databaser för att återställa en databas till en tidpunkt tidigare. På så sätt kan du återställa dataskador som orsakas av mänskliga fel. Med point-in-time-återställningen kan du skapa en ny databas på samma server som representerar datatillståndet före den skadade händelsen. För de flesta databaser tar återställningsåtgärderna mindre än 12 timmar. Det kan ta längre tid att återställa en mycket stor eller mycket aktiv databas. Mer information om återställningstid finns i [återställningstiden för databasen](sql-database-recovery-using-backups.md#recovery-time). 

Om den maximala lagringsperioden för säkerhetskopiering av point-in-time restore (PITR) inte är tillräcklig för ditt program, kan du utöka den genom att konfigurera en långsiktig bevarandeprincip (LTR) för databasen/databaserna. Mer information finns i [Långsiktig lagring av säkerhetskopiering](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Jämföra georeplikering med redundansgrupper

[Grupper för automatisk redundans](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) förenklar distributionen och användningen av [georeplikering](sql-database-active-geo-replication.md) och lägger till ytterligare funktioner enligt beskrivningen i följande tabell:

|                                              | Geo-replikering | Redundansgrupper  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatisk redundans                           |     Inga          |      Ja         |
| Växla över flera databaser samtidigt  |     Inga          |      Ja         |
| Användaren måste uppdatera anslutningssträngen efter redundans      |     Ja         |      Inga          |
| Hanterad instans stöds                   |     Inga          |      Ja         |
| Kan vara i samma region som primär             |     Ja         |      Inga          |
| Flera repliker                            |     Ja         |      Inga          |
| Stöder lässkala                          |     Ja         |      Ja         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Återställa en databas till den befintliga servern

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar.

- Ett alternativ är att vänta tills databasen är tillbaka online när avbrottet på datadatacentret är över. Det här fungerar för program som har råd att ha databasen offline. Till exempel ett utvecklingsprojekt eller en kostnadsfri utvärderingsversion som du inte behöver arbeta med hela tiden. När ett datacenter har ett avbrott vet du inte hur länge avbrottet kan pågå, så det här alternativet fungerar bara om du inte behöver databasen på ett tag.
- Ett annat alternativ är att återställa en databas på alla servrar i alla Azure-regioner med hjälp av [geo-redundanta databassäkerhetskopior](sql-database-recovery-using-backups.md#geo-restore) (geo-återställning). Geoåterställning använder en geo-redundant säkerhetskopiering som källa och kan användas för att återställa en databas även om databasen eller datacentret är otillgängligt på grund av ett avbrott.
- Slutligen kan du snabbt återställa från ett avbrott om du har konfigurerat antingen geografiskt sekundärt med [aktiv georeplikering](sql-database-active-geo-replication.md) eller en [automatisk redundansgrupp](sql-database-auto-failover-group.md) för databasen eller databaserna. Beroende på vilket du väljer av dessa tekniker kan du använda manuell eller automatisk redundans. Medan redundans själv tar bara några sekunder, tjänsten tar minst 1 timme att aktivera den. Detta är nödvändigt för att säkerställa att redundansen motiveras av avbrottets omfattning. Redundansen kan också resultera i små dataförluster på grund av den asynkrona replikeringen. 

När du utvecklar din affärskontinuitetsplan är det viktigt att du känner till den längsta godkända tiden innan programmet är helt återställt efter en avbrottshändelse. Den tid som krävs för att programmet ska återställas helt kallas återställningstidsmål (RTO). Du måste också förstå den maximala perioden för de senaste datauppdateringarna (tidsintervallet) som programmet kan tolerera att förlora när det återställs från en oplanerad störande händelse. Den potentiella dataförlusten kallas återställningspunktsmål (RPO).

Olika återställningsmetoder erbjuder olika nivåer av RPO och RTO. Du kan välja en specifik återställningsmetod eller använda en kombination av metoder för att uppnå fullständig programåterställning. I följande tabell jämförs RPO och RTO för varje återställningsalternativ. Grupper för automatisk redundans förenklar distributionen och användningen av georeplikering och lägger till ytterligare funktioner enligt beskrivningen i följande tabell.

| Återställningsmetod | Rto | RPO |
| --- | --- | --- | 
| Geoåterställning från geo-replikerade säkerhetskopior | 12 h | 1 h |
| Automatiska redundansgrupper | 1 h | 5 s |
| Manuell databas redundans | 30 s | 5 s |

> [!NOTE]
> *Manuell databasväxling* refererar till redundans för en enskild databas till dess geo-replikerade sekundära med det [oplanerade läget](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Se tabellen tidigare i den här artikeln för information om den automatiska redundansen RTO och RPO.


Använd grupper för automatisk redundans om ditt program uppfyller något av följande villkor:

- Är verksamhetskritiskt.
- Har ett servicenivåavtal (SLA) som inte tillåter 12 timmar eller mer av driftstopp.
- Driftstopp kan leda till finansiellt ansvar.
- Har en hög dataförändring och 1 timmes dataförlust är inte acceptabelt.
- Den extra kostnaden för aktiv geo-replikering är lägre än de potentiella ekonomiska skyldigheterna och den associerade affärsförlusten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Du kan välja att använda en kombination av säkerhetskopiering av databaser och aktiv geo-replikering beroende på dina programkrav. En diskussion om designöverväganden för fristående databaser och för elastiska pooler med hjälp av dessa funktioner för affärskontinuitet finns i [Utforma ett program för molnkatastrofåterställning](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och strategier för återställning av elastisk pool [katastrof.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

Följande avsnitt innehåller en översikt över stegen för att återställa med hjälp av antingen databassäkerhetskopior eller aktiv geo-replikering. Detaljerade steg som planeringskrav, steg efter återställning och information om hur du simulerar ett avbrott för att utföra en borrmaskin för haveriberedskap finns i [Återställa en SQL-databas från ett avbrott](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Förbereda för ett avbrott

Oavsett vilken funktion för affärskontinuitet du använder, måste du:

- Identifiera och förbereda målservern, inklusive IP-brandväggsregler på servernivå, inloggningar och behörigheter på huvuddatabasnivå.
- Bestämma hur du ska omdirigera klienter och klientprogram till den nya servern.
- Dokumentera andra beroenden, till exempel granskningsinställningar och aviseringar.

Om du inte förbereder dig ordentligt, att föra dina program online efter en redundans eller en databas återhämtning tar extra tid och sannolikt också kräver felsökning i en tid av stress - en dålig kombination.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Växla över till en geod replikerad sekundär databas

Om du använder aktiva geo-replikerings- eller automatisk redundansgrupper som återställningsmekanism kan du konfigurera en automatisk redundansprincip eller använda [manuell oplanerad redundans](sql-database-active-geo-replication-portal.md#initiate-a-failover). När redundansen har initierats gör redundansen att den sekundära blir den nya primära och redo att registrera nya transaktioner och svara på frågor - med minimal dataförlust för de data som ännu inte replikerats. Information om hur du utformar redundansprocessen finns i [Utforma ett program för molnkatastrofåterställning](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> När datacentret är online igen återansluter de gamla primärfärgerna automatiskt till den nya primära och blir sekundära databaser. Om du behöver flytta primärt tillbaka till den ursprungliga regionen kan du initiera en planerad redundans manuellt (återställning efter fel).

### <a name="perform-a-geo-restore"></a>Utföra en geo-återställning

Om du använder automatiska säkerhetskopior med geouppsagbar lagring (aktiverad som standard) kan du återställa databasen med hjälp av [geoåterställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Återställning sker vanligtvis inom 12 timmar - med dataförlust på upp till en timme bestäms av när den senaste loggsäkerhetskopiningen togs och replikerades. Databasen kan inte registrera några transaktioner eller svara på frågor förrän återställningen har slutförts. Geoåterställning återställer bara databasen till den senast tillgängliga tidpunkten.

> [!NOTE]
> Om datacentret är online igen innan du växlar över programmet till den återställda databasen kan du avbryta återställningen.

### <a name="perform-post-failover--recovery-tasks"></a>Utföra åtgärder efter en redundansväxling eller återställning

Efter återställningen från endera återställningsmetod måste du utföra följande ytterligare uppgifter innan dina användare och program kan komma igång igen:

- Omdirigera klienter och klientprogram till den nya servern och återställda databasen
- Se till att lämpliga IP-brandväggsregler på servernivå finns på plats för användare att ansluta eller använda [brandväggar på databasnivå](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) för att aktivera lämpliga regler.
- Se till att rätt inloggningar och behörigheter på huvuddatabasnivå är på plats (eller använd [inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Konfigurera granskning efter behov
- Konfigurera aviseringar efter behov

> [!NOTE]
> Om du använder en redundansk grupp och ansluter till databaserna med hjälp av read-write lstener, sker omdirigeringen efter redundans automatiskt och transparent till programmet.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uppgradera ett program med minimal avbrottstid

Ibland måste ett program tas offline på grund av planerat underhåll, till exempel en programuppgradering. [Hantera programuppgraderingar](sql-database-manage-application-rolling-upgrade.md) beskriver hur du använder aktiv geo-replikering för att aktivera rullande uppgraderingar av ditt molnprogram för att minimera driftstopp under uppgraderingar och tillhandahålla en återställningsväg om något går fel.

## <a name="next-steps"></a>Nästa steg

En diskussion om överväganden om programdesign för fristående databaser och elastiska pooler finns i [Utforma ett program för molnkatastrofåterställning](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och strategier för återställning av elastisk [pool.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
