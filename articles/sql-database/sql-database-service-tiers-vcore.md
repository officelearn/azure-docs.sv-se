---
title: Azure SQL Database-tjänst - vCore | Microsoft Docs
description: Den vCore-baserade inköpsmodellen kan du oberoende skala beräknings- och lagringsresurser, matcha lokala prestanda och optimera pris.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693344"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Välj bland de vCore-servicenivåerna och migrera från DTU-tjänstnivåer

Virtuell kärna (vCore)-baserade inköpsmodellen kan du oberoende skala beräknings- och lagringsresurser, matcha lokala prestanda och optimera pris. Du kan också välja generering av maskinvara:

- **Gen4**: Upp till 24 logiska CPU: er som bygger på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, vCore = 1 sidor (fysiska kärnor), 7 GB per kärna, anslutna SSD
- **Gen5**: Upp till 80 logiska processorer som baseras på Intel E5-2673 v4 (Broadwell) 2.3 GHz-processorer, vCore = 1 LP (hyper-tråd), 5.1 GB per kärna, snabb eNVM SSD

Gen4 maskinvara erbjuder betydligt mer minne per vCore. Gen5 maskinvara kan du skala upp beräkningsresurserna som är mycket högre.

> [!NOTE]
> Information om DTU-baserade tjänstnivåer finns i [tjänstnivåer för den DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md). Information om skillnaderna mellan servicenivåer för den DTU-baserade och de vCore-baserade inköpschef modellerna finns i [Azure SQL Database köpa modeller](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Tjänstnivå egenskaper

Den vCore-baserade inköpsmodellen erbjuder tre tjänstnivåer: generell användning, hyperskala och affärskritisk. Dessa särskiljs med hjälp av olika storlekar, design för hög tillgänglighet, felisolering metoder, typer och storlekar för lagring och i/o-intervall.

Du måste separat konfigurera nödvändiga lagring och kvarhållning för säkerhetskopior. Att ange kvarhållningsperioden för säkerhetskopiering, öppna Azure-portalen, gå till servern (inte databasen) och gå sedan till **hantera säkerhetskopior** > **konfigurera principen för**  >   **Punkt i tiden Återställ konfiguration** > **7-35 dagar**.

I följande tabell beskrivs skillnaderna mellan de tre nivåerna:

||**Generellt syfte**|**Affärskritisk**|**Hyperskala**|
|---|---|---|---|
|Bäst för|De flesta företags arbetsbelastningar. Erbjudanden budgetinriktade, balanserade och skalbara beräknings- och lagringsalternativ.|Affärsprogram med höga i/o-krav. Erbjuds högsta uthålligheten mot fel tack vare flera isolerade repliker.|De flesta företags arbetsbelastningar med mycket skalbar lagring och läs-och skalningskrav.|
|Compute|**Etablerad beräkning**:<br/>Gen4: 1-24 virtuella kärnor<br/>Gen5: 2-80 virtuella kärnor<br/>**Beräkning utan Server**:<br/>Gen5: 0,5 – 4 virtuella kärnor|**Etablerad beräkning**:<br/>Gen4: 1-24 virtuella kärnor<br/>Gen5: 2-80 virtuella kärnor|**Etablerad beräkning**:<br/>Gen4: 1-24 virtuella kärnor<br/>Gen5: 2-80 virtuella kärnor|
|Minne|**Etablerad beräkning**:<br/>Gen4: 7 GB per vCore<br/>Gen5: 5.1 GB per vCore<br/>**Beräkning utan Server**:<br/>Gen5: 3 GB per vCore|**Etablerad beräkning**:<br/>Gen4: 7 GB per vCore<br/>Gen5: 5.1 GB per vCore |**Etablerad beräkning**:<br/>Gen4: 7 GB per vCore<br/>Gen5: 5.1 GB per vCore|
|Storage|Använder Fjärrlagring.<br/>**Enkel databas etablerad beräkning**:<br/>5 GB – 4 TB<br/>**Beräkning för enkel databas utan Server**:<br/>5 GB - 1 TB<br/>**Hanterad instans**: 32 GB - 8 TB |Använder lokal SSD-lagring.<br/>**Enkel databas etablerad beräkning**:<br/>5 GB – 4 TB<br/>**Hanterad instans**:<br/>32 GB - 4 TB |Flexibla Storleksökningen av lagring vid behov. Har stöd för upp till 100 TB lagringsutrymme. Använder lokal SSD-lagring för lokala buffertpoolen cache och lokal datalagring. Använder Azure Fjärrlagring sista långsiktig datalagring. |
|I/o-genomströmning (ungefärlig)|**Enkel databas**: 500 IOPS per vCore med 7000 högsta IOPS.<br/>**Hanterad instans**: Beror på [storleken på filen](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS per kärna med 200 000 högsta IOPS|Hyperskala är en arkitektur med flera nivåer med cachelagring på flera nivåer. Effektiva IOPs beror på arbetsbelastningen.|
|Tillgänglighet|1 repliken, inga lässkala repliker|3 repliker, 1 [lässkala repliken](sql-database-read-scale-out.md),<br/>zonredundant hög tillgänglighet (HA)|1 skrivskyddad replik, plus 0-4 [lässkala repliker](sql-database-read-scale-out.md)|
|Säkerhetskopior|[Läsåtkomst till geografiskt redundant lagring (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagar (7 dagar som standard)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagar (7 dagar som standard)|Ögonblicksbildbaserade säkerhetskopior i Azure Fjärrlagring. Återställningar kan du använda de här ögonblicksbilderna för snabb återställning. Säkerhetskopior är omedelbara och beräkning i/o-prestanda påverkas inte. Återställningar är snabba och är inte en storlek för data-åtgärd (tagning minuter i stället för timmar eller dagar).|
|Minnesintern|Stöds inte|Stöds|Stöds inte|
|||

> [!NOTE]
> Du kan få en kostnadsfri Azure SQL-databas på basic-tjänstnivå tillsammans med ett kostnadsfritt Azure-konto. Mer information finns i [skapa en hanterad molndatabas med ditt kostnadsfria Azure-konto](https://azure.microsoft.com/free/services/sql-database/).

- Läs mer om vCore resursgränser [vCore resursbegränsningar i en enda databas](sql-database-vcore-resource-limits-single-databases.md) och [vCore resursbegränsningar i en hanterad instans](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Mer information om generell användning och företag kritiska tjänstnivåer finns i [tjänstnivåer för generell användning och affärskritisk](sql-database-service-tiers-general-purpose-business-critical.md).
- Läs mer om tjänstnivån hyperskala i den vCore-baserade inköpsmodellen [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

I den etablerade Beräkningsnivån av den vCore-baserade inköpsmodellen kan du byta dina befintliga licenser för rabatterade priser på SQL-databas med hjälp av [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Den här Azure-förmån kan du spara upp till 30 procent på Azure SQL Database med hjälp av en lokal SQL Server-licenser med Software Assurance.

![Priser](./media/sql-database-service-tiers/pricing.png)

Du betalar bara för de underliggande Azure-infrastrukturen genom att använda din befintliga SQL Server-licens för SQL database engine själva (Base Compute-priser) med Azure Hybrid-förmånen, eller du kan betala för både den underliggande infrastrukturen och SQL Server licens (Licensinkluderade pris).

Du kan välja eller ändra licensieringsmodellen med hjälp av Azure portal eller med någon av följande API: er:

- Att ställa in eller uppdatera licenstypen med hjälp av PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [Ny AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Att ställa in eller uppdatera licenstypen med hjälp av Azure-CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [Skapa AZ sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Att ställa in eller uppdatera licenstypen med hjälp av REST-API:

  - [Databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databaser – uppdatering](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Hanterade instanser - uppdatering](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrera från den DTU-baserade modellen till den vCore-baserade modellen

### <a name="migrate-a-database"></a>Migrera en databas

Migrera en databas från den DTU-baserade inköpsmodellen till den vCore-baserade inköpsmodellen liknar att uppgradera eller nedgradera mellan tjänstenivåerna standard och premium-i DTU-baserade inköpsmodellen.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrera databaser med geo-replikeringslänkar

Migrera från den DTU-baserade modellen till den vCore-baserade inköpsmodellen liknar att uppgradera eller nedgradera relationerna mellan databaser på standard och premium-servicenivåerna geo-replikering. Du behöver stoppa geo-replikering under migreringen, men du måste följa reglerna sekvensering:

- När du uppgraderar, måste du uppgradera den sekundära databasen först och sedan uppgradera primärt.
- När du nedgraderar, i omvänd ordning: du nedgradera den primära databasen först och sedan nedgraderar sekundär.

När du använder geo-replikering mellan två elastiska pooler, rekommenderar vi att du anger en pool som primärt och andra som sekundär. I så fall bör du använda samma sekvensering riktlinjer när du migrerar elastiska pooler. Om du har elastiska pooler som innehåller både primära och sekundära databaser kan behandla poolen med högre användning som primärt och följa reglerna sekvensering därefter.  

I följande tabell innehåller riktlinjer för specifika Migreringsscenarier:

|Aktuella tjänstnivå|Måltjänstnivån|Migreringstyp|Användaråtgärder|
|---|---|---|---|
|Standard|Generellt syfte|Laterala|Kan migrera i valfri ordning, men måste se till att lämpliga vCore storlek *|
|Premium|Affärskritisk|Laterala|Kan migrera i valfri ordning, men måste se till att lämpliga vCore storlek *|
|Standard|Affärskritisk|Uppgradera|Måste migrera sekundära först|
|Affärskritisk|Standard|Nedgradera|Måste migrera primära först|
|Premium|Generellt syfte|Nedgradera|Måste migrera primära först|
|Generellt syfte|Premium|Uppgradera|Måste migrera sekundära först|
|Affärskritisk|Generellt syfte|Nedgradera|Måste migrera primära först|
|Generellt syfte|Affärskritisk|Uppgradera|Måste migrera sekundära först|
||||

\* Varje 100 dtu: er på standard-nivån kräver minst 1 virtuell kärna, och varje 125 dtu: er på premium-nivån kräver minst 1 virtuell kärna.

### <a name="migrate-failover-groups"></a>Migrera redundansgrupper

Migrering av redundansgrupper med flera databaser kräver enskilda migreringen av de primära och sekundära databaserna. Samma överväganden och sekvensering regler gäller vid den här processen. När databaserna som har konverterats till den vCore-baserade inköpsmodellen, redundansgruppen kommer att gälla med samma principinställningar.

### <a name="create-a-geo-replication-secondary-database"></a>Skapa en sekundär databas med geo-replikering

Du kan skapa en sekundär databas för geo-replikering (en geo-secondary) endast med hjälp av samma tjänstenivå som du använde för den primära databasen. För databaser med en hög log generations hastighet rekommenderar vi att du skapar geo-secondary med samma beräkning storlek som primärt.

Om du skapar en geo-secondary i den elastiska poolen för en enskild primär databas, se till att den `maxVCore` inställningen för poolen matchar beräkningsstorleken för den primära databasen. Om du skapar en geo-secondary för en primär i en annan elastisk pool, rekommenderar vi att poolerna har samma `maxVCore` inställningar.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Använda databaskopian för att konvertera en DTU-baserad databas till en vCore-baserad databas

Du kan kopiera en databas med lämplig storlek i DTU-baserad beräkning till en databas med lämplig storlek för vCore-baserad beräkning utan begränsningar eller särskilda ordningsföljd så länge Målstorlek för beräkning har stöd för den maximala databasstorleken av källdatabasen. Databaskopian skapar en ögonblicksbild av data från och med starttiden för kopieringen och synkronisera inte data mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- Specifika storlekar och lagring som kan användas för enskilda databaser finns i [SQL Database vCore-baserade resursbegränsningar för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- Specifika storlekar och lagring som kan användas för elastiska pooler finns i [SQL Database vCore-baserade resursbegränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
