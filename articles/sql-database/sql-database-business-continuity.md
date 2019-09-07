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
ms.date: 06/25/2019
ms.openlocfilehash: 5cc033787e1045926ff4fece6826e41f430d48fd
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744457"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Översikt över affärskontinuitet med Azure SQL Database

**Verksamhets kontinuitet** i Azure SQL Database syftar på mekanismer, principer och procedurer som gör det möjligt för din verksamhet att fortsätta att arbeta i störnings miljön, särskilt för dess dator infrastruktur. I de flesta fall kommer Azure SQL Database att hantera de störande händelser som kan uppstå i moln miljön och hålla dina program och affärs processer igång. Det finns dock vissa störande händelser som inte kan hanteras av SQL Database automatiskt, till exempel:

- Användaren råkade ta bort eller uppdaterade en rad i en tabell.
- Skadlig angripare lyckades ta bort data eller släpper en databas.
- Jord bävning orsakade ett strömavbrott och tillfälligt inaktiverat Data Center.

Den här översikten beskriver de funktioner som Azure SQL Database tillhandahåller för affärskontinuitet och haveriberedskap. Lär dig mer om alternativ, rekommendationer och självstudier för att komma åt från störande händelser som kan leda till data förlust eller orsaka att databasen och programmet blir otillgängligt. Lär dig vad du ska göra när ett användar-eller program fel påverkar data integriteten, att en Azure-region har ett avbrott eller att programmet kräver underhåll.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-funktioner som du kan använda för att upprätthålla affärskontinuitet

Från ett databas perspektiv finns fyra stora möjliga störningar:

- Lokala maskin-eller program varu problem påverkar noden, till exempel disk enhets haverier.
- Datafel eller borttagning av data orsakas vanligt vis av ett program fel eller ett mänskligt fel. Sådana problem är programspecifika och kan normalt inte identifieras av databas tjänsten.
- Data Center avbrott, eventuellt orsakade av en naturlig katastrof. Det här scenariot kräver en viss nivå av GEO-redundans med programredundans till ett alternativt Data Center.
- Uppgraderings-eller underhålls fel, oväntade problem som inträffar under underhåll eller uppgraderingar av planerad infrastruktur kan kräva snabb återställning till tidigare databas tillstånd.

För att minimera de lokala maskin-och program varu felen innehåller SQL Database en [arkitektur med hög tillgänglighet](sql-database-high-availability.md)som garanterar automatisk återställning från dessa problem med upp till 99,995% tillgänglighets-SLA.  

För att skydda ditt företag mot data förlust skapar SQL Database automatiskt fullständiga säkerhets kopieringar varje vecka, differentiella databas säkerhets kopior var 12: e timme och säkerhets kopieringar av transaktions loggar var 5-10 minut. Säkerhets kopiorna lagras i RA-GRS-lagring under minst 7 dagar för alla tjänst nivåer. Alla tjänst nivåer utom Basic Support konfigurerbar kvarhållning av säkerhets kopior för återställning till tidpunkt, upp till 35 dagar. 

SQL Database innehåller också flera funktioner för verksamhets kontinuitet, som du kan använda för att minimera olika oplanerade scenarier. 

- Med [temporära tabeller](sql-database-temporal-tables.md) kan du återställa radversioner från valfri tidpunkt.
- [Inbyggda automatiserade säkerhets kopieringar](sql-database-automated-backups.md) och [tidpunkts återställning](sql-database-recovery-using-backups.md#point-in-time-restore) gör att du kan återställa slutförda databaser till en viss tidpunkt inom den konfigurerade kvarhållningsperioden upp till 35 dagar.
- Du kan [återställa en borttagen databas](sql-database-recovery-using-backups.md#deleted-database-restore) till den tidpunkt då den togs bort om **SQL Database servern inte har tagits bort**.
- [Långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-retention.md) gör att du kan behålla säkerhets kopiorna upp till 10 år.
- [Aktiv geo-replikering](sql-database-active-geo-replication.md) gör att du kan skapa läsbara repliker och manuellt redundansväxla till en replik i händelse av avbrott i ett Data Center eller en program uppgradering.
- Med [gruppen automatisk redundans](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) kan programmet automatiskt återställas i händelse av ett avbrott i data centret.

## <a name="recover-a-database-within-the-same-azure-region"></a>Återställ en databas inom samma Azure-region

Du kan använda automatiska databas säkerhets kopieringar för att återställa en databas till en tidpunkt tidigare. På så sätt kan du återställa från skadade data som orsakas av mänskliga fel. Med med programinformation-in-Time-återställning kan du skapa en ny databas på samma server som representerar data statusen innan händelsen skadas. För de flesta databaser tar återställnings åtgärderna i mindre än 12 timmar. Det kan ta längre tid att återställa en mycket stor eller mycket aktiv databas. Mer information om återställnings tid finns i [databasens återställnings tid](sql-database-recovery-using-backups.md#recovery-time). 

Om den högsta kvarhållningsperioden för kvarhållning av säkerhets kopior för PITR (Point-in-Time Restore) inte räcker för ditt program kan du utöka det genom att konfigurera en princip för långsiktig kvarhållning (brv) för databaserna. Mer information finns i [långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Jämför geo-replikering med failover-grupper

[Grupper för automatisk redundans](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) fören klar distributionen och användningen av [geo-replikering](sql-database-active-geo-replication.md) och Lägg till ytterligare funktioner enligt beskrivningen i följande tabell:

|                                              | Geo-replikering | Redundansgrupper  |
|:---------------------------------------------| :-------------- | :----------------|
| Automatisk redundans                           |     Nej          |      Ja         |
| Redundansväxla flera databaser samtidigt  |     Nej          |      Ja         |
| Uppdatera anslutnings sträng efter redundans      |     Ja         |      Nej          |
| Hanterad instans stöds                   |     Nej          |      Ja         |
| Kan finnas i samma region som primär             |     Ja         |      Nej          |
| Flera repliker                            |     Ja         |      Nej          |
| Stöder Read-Scale                          |     Ja         |      Ja         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Återställ en databas till den befintliga servern

Även om det är ovanligt finns risken för ett avbrott på ett Azure-datacenter. När ett avbrott uppstår orsakar det ett verksamhetsavbrott som kan vara några få minuter eller flera timmar.

- Ett alternativ är att vänta tills databasen är tillbaka online när avbrottet på datadatacentret är över. Det här fungerar för program som har råd att ha databasen offline. Till exempel ett utvecklingsprojekt eller en kostnadsfri utvärderingsversion som du inte behöver arbeta med hela tiden. När ett Data Center har ett avbrott vet du inte hur lång tid det tar för avbrottet, så det här alternativet fungerar bara om du inte behöver databasen en stund.
- Ett annat alternativ är att återställa en databas på en server i valfri Azure-region med hjälp av Geo [-redundanta databas säkerhets kopieringar](sql-database-recovery-using-backups.md#geo-restore) (geo-återställning). Geo-återställning använder en Geo-redundant säkerhets kopia som källa och kan användas för att återställa en databas även om databasen eller data centret inte går att komma åt på grund av ett avbrott.
- Slutligen kan du snabbt återställa efter ett avbrott om du har konfigurerat en geo-Secondary med hjälp av [aktiv geo-replikering](sql-database-active-geo-replication.md) eller en [grupp för automatisk redundans](sql-database-auto-failover-group.md) för databasen eller databaserna. Beroende på ditt val av dessa tekniker kan du antingen använda manuell eller automatisk redundans. Även om det bara tar några sekunder att redundansväxla, tar det minst 1 timme för tjänsten att aktivera den. Detta är nödvändigt för att säkerställa att redundansväxlingen är motiverat av avbrottets skala. Dessutom kan redundansväxlingen leda till små data förluster på grund av den asynkrona replikeringens karaktär. 

När du utvecklar din affärskontinuitetsplan är det viktigt att du känner till den längsta godkända tiden innan programmet är helt återställt efter en avbrottshändelse. Tiden som krävs för att programmet ska återställas fullständigt kallas för återställnings tids mål (RTO). Du måste också förstå den maximala tiden för de senaste data uppdateringarna (tidsintervall) som programmet kan tolerera vid återställning från en oplanerad störnings händelse. Den potentiella data förlusten kallas för återställnings punkt mål (återställnings punkt mål).

Olika återställnings metoder erbjuder olika nivåer av återställnings-och RTO. Du kan välja en specifik återställnings metod eller använda en kombination av metoder för att uppnå fullständig program återställning. I följande tabell jämförs återställnings-och RTO för varje återställnings alternativ. Grupper för automatisk redundans fören klar distributionen och användningen av geo-replikering och lägger till ytterligare funktioner enligt beskrivningen i följande tabell.

| Återställnings metod | RTO | Mål för återställningspunkt |
| --- | --- | --- | 
| Geo-återställning från geo-replikerade säkerhets kopieringar | 12 h | 1 tim |
| Automatiska redundansgrupper | 1 tim | 5 s |
| Manuell databas växling vid fel | 30 s | 5 s |

> [!NOTE]
> *Manuell databas växling* syftar på redundansväxling av en enkel databas till dess geo-replikerade sekundära med det [oplanerade läget](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Se tabellen tidigare i den här artikeln för information om RTO för automatisk redundans och återställnings punkt.


Använd grupper för automatisk redundans om ditt program uppfyller något av följande kriterier:

- Är verksamhetskritiskt.
- Har ett service nivå avtal (SLA) som inte tillåter 12 timmar eller mer av drift stopp.
- Stillestånds tiden kan resultera i ekonomiska skulder.
- Har en hög frekvens av data ändringar och 1 timme data förlust är inte acceptabel.
- Den extra kostnaden för aktiv geo-replikering är lägre än de potentiella ekonomiska skyldigheterna och den associerade affärsförlusten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Du kan välja att använda en kombination av databas säkerhets kopior och aktiv geo-replikering beroende på dina program krav. En beskrivning av design överväganden för fristående databaser och elastiska pooler med hjälp av dessa funktioner för affärs kontinuitet finns i [utforma ett program för haveri beredskap i molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och [elastiska pooler](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

I följande avsnitt får du en översikt över stegen för att återställa med hjälp av databas säkerhets kopior eller aktiv geo-replikering. Detaljerade anvisningar, till exempel planerings krav, efter återställnings steg och information om hur du simulerar ett avbrott för att utföra en haveri beredskaps granskning finns i [återställa en SQL Database från ett avbrott](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Förbereda för ett avbrott

Oavsett vilken funktion för affärskontinuitet du använder, måste du:

- Identifiera och Förbered mål servern, inklusive regler för IP-brandvägg på server nivå, inloggningar och behörigheter på huvud databas nivå.
- Bestämma hur du ska omdirigera klienter och klientprogram till den nya servern.
- Dokumentera andra beroenden, till exempel granskningsinställningar och aviseringar.

Om du inte förbereder det korrekt, tar du med dina program online efter en redundansväxling eller om databas återställningen tar extra tid och sannolikt också behöver fel sökning vid en tidpunkt då belastningen är felaktig.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Redundansväxla till en geo-replikerad sekundär databas

Om du använder aktiv geo-replikering eller grupper för automatisk redundans som återställnings metod kan du konfigurera en princip för automatisk redundansväxling eller använda [manuell oplanerad redundansväxling](sql-database-active-geo-replication-portal.md#initiate-a-failover). Efter initieringen gör redundansväxlingen den sekundära att bli den nya primära och redo att registrera nya transaktioner och svara på frågor – med minimal data förlust för data som ännu inte har repliker ATS. Information om hur du utformar redundansväxlingen finns i [utforma ett program för haveri beredskap för molnet](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> När data centret är online igen kommer den gamla presidentval automatiskt att återansluta till den nya primära och blir sekundär databas. Om du behöver flytta tillbaka den primära tillbaka till den ursprungliga regionen kan du starta en planerad redundansväxling manuellt (failback).

### <a name="perform-a-geo-restore"></a>Utföra en geo-återställning

Om du använder automatiserade säkerhets kopieringar med Geo-redundant lagring (aktiverat som standard) kan du återställa databasen med [geo-återställning](sql-database-disaster-recovery.md#recover-using-geo-restore). Återställningen sker vanligt vis inom 12 timmar, med data förlust på upp till en timme, baserat på när den senaste logg säkerhets kopieringen gjordes och replikerades. Databasen kan inte registrera några transaktioner eller svara på frågor förrän återställningen har slutförts. Observera att geo-återställning bara återställer databasen till senaste tillgängliga tidpunkt.

> [!NOTE]
> Om data centret är online igen innan du växlar programmet över till den återställda databasen kan du avbryta återställningen.

### <a name="perform-post-failover--recovery-tasks"></a>Utföra åtgärder efter en redundansväxling eller återställning

Efter återställningen från endera återställningsmetod måste du utföra följande ytterligare uppgifter innan dina användare och program kan komma igång igen:

- Omdirigera klienter och klientprogram till den nya servern och återställda databasen
- Se till att det finns tillräckligt med regler för IP-brandvägg på server nivå för att användarna ska kunna ansluta eller använda [brand väggar på databas nivå](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) för att aktivera lämpliga regler.
- Se till att rätt inloggningar och behörigheter på huvuddatabasnivå är på plats (eller använd [inneslutna användare](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Konfigurera granskning efter behov
- Konfigurera aviseringar efter behov

> [!NOTE]
> Om du använder en failover-grupp och ansluter till databaserna med hjälp av Skriv-och skriv lstener sker omdirigeringen efter redundansväxlingen automatiskt och transparent till programmet.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Uppgradera ett program med minimal avbrottstid

Ibland måste ett program tas offline på grund av planerat underhåll, till exempel en program uppgradering. [Hantera program uppgraderingar](sql-database-manage-application-rolling-upgrade.md) beskriver hur du använder aktiv geo-replikering för att aktivera rullande uppgraderingar av ditt moln program för att minimera drift stopp vid uppgraderingar och ange en återställnings väg om något går fel.

## <a name="next-steps"></a>Nästa steg

En beskrivning av program design överväganden för fristående databaser och för elastiska pooler finns i [utforma ett program för haveri beredskap för moln haveri beredskap](sql-database-designing-cloud-solutions-for-disaster-recovery.md) och [strategier för haveri beredskap för elastisk pool](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
