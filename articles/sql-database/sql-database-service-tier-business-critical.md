---
title: Affärskritisk tjänstnivå
description: Lär dig mer om den affärskritiska nivån i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268683"
---
# <a name="business-critical-tier---azure-sql-database"></a>Affärskritisk nivå - Azure SQL Database

> [!NOTE]
> Business Critical-nivån kallas Premium i DTU-inköpsmodell. En jämförelse av den vCore-baserade inköpsmodellen med den DTU-baserade inköpsmodellen finns i [Azure SQL Database som köper modeller och resurser](sql-database-purchase-models.md).

Azure SQL Database baseras på SQL Server Database Engine-arkitektur som har justerats för molnmiljön för att säkerställa 99,99 % tillgänglighet även vid infrastrukturfel. Det finns tre arkitektoniska modeller som används i Azure SQL Database:
- Allmänt syfte/standard 
- Affärskritiskt/Premium
- Hyperskala

Premium/Business Critical service nivåmodell baseras på ett kluster av databasmotorprocesser. Den här arkitektoniska modellen bygger på ett faktum att det alltid finns ett kvorum för tillgängliga databasmotornoder och har minimal prestandapåverkan på din arbetsbelastning även under underhållsaktiviteter.

Azure uppgraderar och korrigerar underliggande operativsystem, drivrutiner och SQL Server Database Engine transparent med minimal driftstopp för slutanvändare. 

Premium-tillgänglighet är aktiverad på Premium- och Affärskritiska tjänstnivåer i Azure SQL Database och den är utformad för intensiva arbetsbelastningar som inte kan tolerera någon prestandapåverkan på grund av de pågående underhållsåtgärderna.

I premiummodellen integrerar Azure SQL-databasen beräkning och lagring på den enda noden. Hög tillgänglighet i den här arkitekturmodellen uppnås genom replikering av beräkningsprocess (SQL Server Database Engine process) och lagring (lokalt ansluten SSD) som distribueras i fyra nodkluster, med hjälp av teknik som liknar SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Kluster av databasmotornoder](media/sql-database-managed-instance/business-critical-service-tier.png)

Både SQL-databasmotorprocessen och underliggande mdf/ldf-filer placeras på samma nod med lokalt ansluten SSD-lagring som ger låg svarstid till din arbetsbelastning. Hög tillgänglighet implementeras med hjälp av teknik som liknar SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Varje databas är ett kluster av databasnoder med en primär databas som är tillgänglig för kundarbetsbelastning och tre sekundära processer som innehåller kopior av data. Den primära noden skickar ständigt ändringarna till sekundära noder för att säkerställa att data är tillgängliga på sekundära repliker om den primära noden kraschar av någon anledning. Redundans hanteras av SQL Server Database Engine – en sekundär replik blir den primära noden och en ny sekundär replik skapas för att säkerställa tillräckligt många noder i klustret. Arbetsbelastningen omdirigeras automatiskt till den nya primära noden.

Dessutom har Business Critical-klustret inbyggd [lässkalningsfunktion](sql-database-read-scale-out.md) som ger inbyggd skrivskyddad nod som kan användas för att köra skrivskyddade frågor (till exempel rapporter) som inte ska påverka prestanda för din primära arbetsbelastning.

## <a name="when-to-choose-this-service-tier"></a>När ska du välja den här tjänstnivån?

Business Critical-tjänstnivån är utformad för program som kräver svar med låg latens från den underliggande SSD-lagringen (1–2 ms i genomsnitt), snabb återställning om den underliggande infrastrukturen misslyckas eller behöver avlasta rapporter, analyser och skrivskyddade frågor till den kostnadsfria läsbara sekundära repliken av den primära databasen.

De viktigaste anledningarna till att du bör välja Affärskritisk tjänstnivå i stället för allmän nivå är:
-   Låga IO-svarstidskrav – arbetsbelastning som kräver snabba svar från lagringslagret (1-2 millisekunder i genomsnitt) bör använda affärskritisk nivå. 
-   Frekvent kommunikation mellan program och databas. Program som inte kan utnyttja cachelagring eller [begäran om cachelagring](sql-database-use-batching-to-improve-performance.md) i programlager och som behöver skicka många SQL-frågor som måste bearbetas snabbt är bra kandidater för affärskritisk nivå.
-   Ett stort antal uppdateringar – infoga, uppdatera och ta bort åtgärder ändrar datasidorna `CHECKPOINT` i minnet (smutsig sida) som måste sparas i datafiler med åtgärden. Potentiell databasmotorprocesskrasch eller en redundansväxling av databasen med ett stort antal smutsiga sidor kan öka återställningstiden på nivån Allmänt syfte. Använd affärskritisk nivå om du har en arbetsbelastning som orsakar många ändringar i minnet. 
-   Tidskrävande transaktioner som ändrar data. Transaktioner som öppnas under en längre tid förhindrar trunkering av loggfilen som kan öka loggstorleken och antalet [virtuella loggfiler (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). Stort antal VLF kan bromsa återställning av databasen efter redundans.
-   Arbetsbelastning med rapportering och analytiska frågor som kan omdirigeras till den kostnadsfria sekundära skrivskyddade repliken.
- Högre återhämtning och snabbare återställning från felen. I händelse av systemfel inaktiveras databasen för primär instans och en av de sekundära replikerna blir omedelbart ny läs-skrivprimär databas som är redo att bearbeta frågorna. Databasmotorn behöver inte analysera och göra om transaktioner från loggfilen och läsa in alla data i minnesbufferten.
- Avancerat skydd mot datakorruption - Business Critical-nivån utnyttjar databasrepliker bakom kulisserna för kontinuitetsändamål, och därför utnyttjar tjänsten också automatisk sidreparation, vilket är samma teknik som används för SQL [Server-databasspegling och tillgänglighetsgrupper](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). I händelse av att en replik inte kan läsa en sida på grund av ett problem med dataintegritet, hämtas en ny kopia av sidan från en annan replik som ersätter den oläsliga sidan utan dataförlust eller avbrott i kunden. Den här funktionen är tillämplig på nivån Allmänt syfte om databasen har en geo-sekundär replik.
- Högre tillgänglighet – Business Critical-nivån i Multi-AZ-konfiguration garanterar 99,995 % tillgänglighet, jämfört med 99,99 % av nivån för allmänt syfte.
- Snabb geoåterställning - Affärskritisk nivå som konfigurerats med geo-replikering har ett garanterat återställningspunktmål (RPO) på 5 sek och återställningstidsmål (RTO) på 30 sek för 100 % av distribuerade timmar.

## <a name="next-steps"></a>Nästa steg

- Sök efter resursegenskaper (antal kärnor, IO, minne) på affärskritisk nivå i [hanterad instans,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)en databas i [vCore-modell](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) eller [DTU-modell](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)eller Elastisk pool i [vCore-modell](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) och [DTU-modell](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Läs mer om nivåer [för allmänt ändamål](sql-database-service-tier-general-purpose.md) och [hyperskala.](sql-database-service-tier-hyperscale.md)
- Läs mer om [Service Fabric](../service-fabric/service-fabric-overview.md).
- Fler alternativ för hög tillgänglighet och haveriberedskap finns i [Kontinuitet i verksamheten](sql-database-business-continuity.md).
