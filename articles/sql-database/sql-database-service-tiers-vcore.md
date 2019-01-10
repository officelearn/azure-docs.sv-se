---
title: Azure SQL Database-tjänst - vCore | Microsoft Docs
description: Den vCore-baserade inköpsmodellen kan du oberoende skala beräknings- och lagringsresurser, matcha lokala prestanda och optimera pris.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 01/08/2019
ms.openlocfilehash: 9d5a1493316fbfa9a703655f37a40276ee3ffaf7
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156824"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>vCore-tjänstnivåer, Azure Hybrid-förmånen och migrering

Den vCore-baserade inköpsmodellen kan du oberoende skala beräknings- och lagringsresurser, matcha lokala prestanda och optimera pris. Du kan också välja maskinvarusystem:

- Gen 4 – upp till 24 logiska processorer bygger på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer, vCore = 1 sidor (fysiska kärnor), 7 GB per kärna, anslutna SSD
- Gen 5 – upp till 80 logiska processorer som baseras på Intel E5-2673 v4 (Broadwell) 2,3 GHz-processorer, vCore = 1 LP (hyper-tråd), 5.1. GB per kärna, snabb eNVM SSD

vCore-modellen kan du använda [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) att få kostnadsbesparingar.

> [!NOTE]
> Information om DTU-baserade tjänstnivåer finns i [DTU-baserade tjänstnivåer](sql-database-service-tiers-dtu.md). Information om hur man skiljer DTU-baserade tjänstnivåer och vCore-baserade tjänstnivåer finns i [Azure SQL Database köpa modeller](sql-database-service-tiers.md).

## <a name="service-tier-characteristics"></a>Tjänstens nivån egenskaper

VCore-modellen innehåller tre tjänstnivåer generell användning, hyperskala och affärskritisk. Tjänstnivåer särskiljs med hjälp av olika storlekar, design för hög tillgänglighet, felisolering, typer och storleken på lagringsutrymmet för och i/o-intervallet. Du måste separat konfigurera nödvändiga lagring och kvarhållning för säkerhetskopior. I Azure-portalen går du till servern (inte databasen) > hanterade säkerhetskopior > Konfigurera en princip > punkt i tiden Återställ konfiguration > 7-35 dagar.

Tabellen nedan hjälper dig att förstå skillnaderna mellan de tre nivåerna:

||**Generell användning**|**Affärskritisk**|**Hyperskala (förhandsversion)**|
|---|---|---|---|
|Bäst för|De flesta företags arbetsbelastningar. Erbjudanden budgetera objektorienterad balanserade och skalbara beräknings- och lagringsalternativ.|Affärsprogram med höga I/O-krav. Erbjuder den högsta uthålligheten mot fel tack vare flera isolerade repliker.|De flesta företags arbetsbelastningar med mycket skalbar lagring och läs-och skalningskrav|
|Compute|Gen4: 1-24 vCore<br/>Gen5: 1-80 vCore|Gen4: 1-24 vCore<br/>Gen5: 1-80 vCore|Gen4: 1-24 vCore<br/>Gen5: 1-80 vCore|
|Minne|Gen4: 7 GB per kärna<br>Gen5: 5.1 GB per kärna | Gen4: 7 GB per kärna<br>Gen5: 5.1 GB per kärna |Gen4: 7 GB per kärna<br>Gen5: 5.1 GB per kärna|
|Storage|Använder [premium Fjärrlagring](../virtual-machines/windows/premium-storage.md):<br/>Databas: 5 GB – 4 TB<br/>Hanterad instans: 32 GB TILL 8 TB |Använder lokal SSD-lagring:<br/>Databas: 5 GB – 4 TB<br/>Hanterad instans: 32 GB TILL 4 TB |Flexibel, automatisk storleksökning av lagring vid behov. Har stöd för upp till 100 TB lagring och mycket mer. Lokal SSD-lagring för lokala buffertminne för poolen och lokal datalagring. Azure Fjärrlagring sista långsiktig datalagring. |
|I/o-genomströmning (ungefärlig)|Databas: 500 IOPS per vCore med 7000 högsta IOPS</br>Hanterad instans: Beror på [storleken på filen](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS per kärna med 200 000 högsta IOPS|TBD|
|Tillgänglighet|1 repliken, inga lässkala|3 repliker, 1 [lässkala repliken](sql-database-read-scale-out.md),<br/>zonen redundant hög tillgänglighet|?|
|Säkerhetskopior|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagar (7 dagar som standard)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagar (7 dagar som standard)|ögonblicksbild-baserad säkerhetskopiering i Azure Fjärrlagring och återställningar kan du använda de här ögonblicksbilderna för snabb återställning. Säkerhetskopior är omedelbara och påverkar inte i/o-prestanda för databearbetning. Återställningar är mycket snabbt och är inte en storlek på data igen (tar några minuter i stället för timmar eller dagar).|
|I minnet|Stöds inte|Stöds|Stöds inte|
|||

> [!NOTE]
> Du kan få en kostnadsfri Azure SQL-databas på Basic-tjänstnivå tillsammans med ett kostnadsfritt Azure-konto att utforska Azure. Mer information finns i [skapa en hanterad molndatabas med ditt kostnadsfria Azure-konto](https://azure.microsoft.com/free/services/sql-database/).

- Mer information finns i [vCore resursbegränsningar i enkel databas](sql-database-vcore-resource-limits-single-databases.md) och [vCore resursbegränsningar i Managed Instance](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Läs mer om tjänstnivåer för generell användning och affärskritisk [tjänstnivåer för generell användning och affärskritisk](sql-database-service-tiers-general-purpose-business-critical.md).
- Mer information om tjänstnivån hyperskala i den vCore-baserade inköpsmodellen finns [hyperskala tjänstnivå](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Om du behöver mindre än 1 vCore för beräkningskapacitet kan använda den DTU-baserade inköpsmodellen.

Se [SQL Database vanliga frågor och svar](sql-database-faq.md) svar på vanliga frågor och svar.

## <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

I den vCore-baserade inköpsmodellen, kan du byta dina befintliga licenser för rabatterade priser på SQL-databas med hjälp av den [Azure Hybrid-förmånen för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Den här Azure-förmån kan du använda en lokal SQL Server-licenser för att spara upp till 30% på Azure SQL Database med hjälp av en lokal SQL Server-licenser med Software Assurance.

![prissättning](./media/sql-database-service-tiers/pricing.png)

Med Azure Hybrid-förmånen kan du betalar bara för underliggande Azure-infrastrukturen med din befintliga SQL Server-licens för SQL database engine själva (**BasePrice**) eller betala för både den underliggande infrastrukturen och SQL Server-licens (**LicenseIncluded**). Du kan välja eller ändra licensieringsmodellen med Azure portal eller med någon av följande API: er.

- Att ställa in eller uppdatera licenstypen med hjälp av PowerShell:

  - [Ny AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase):
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [Ny AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

- Att ställa in eller uppdatera licenstypen med Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [Skapa AZ sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [uppdatering av AZ sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Att ställa in eller uppdatera licenstypen med hjälp av REST-API:

  - [Databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databaser – uppdatering](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Hanterade instanser - uppdatering](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migrering från DTU-modellen till vCore-modellen

### <a name="migration-of-a-database"></a>Migrering av en databas

Migrera en databas från den DTU-baserade inköpsmodellen till den vCore-baserade inköpsmodellen liknar att uppgradera eller nedgradera mellan Standard och Premium-databaser i den DTU-baserade inköpsmodellen.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migrering av databaser med geo-replikeringslänkar

Migrera från den DTU-baserade modellen till den vCore-baserade modellen liknar att uppgradera eller nedgradera geo-replikering relationerna mellan Standard och Premium-databaser. Det kräver inte avslutar geo-replikering, men användaren måste följa reglerna ordningsföljd. När du uppgraderar, måste du uppgradera den sekundära databasen först och sedan uppgradera primärt. När du nedgraderar, i omvänd ordning: du nedgradera den primära databasen först och sedan nedgraderar sekundär.

När du använder geo-replikering mellan två elastiska pooler, rekommenderar vi att du anger en pool som primärt och den andra – som sekundärt. I så fall bör migrerar elastiska pooler använda samma riktlinjer.  Men är det tekniskt sett är det möjligt att en elastisk pool innehåller både primära och sekundära databaser. I det här fallet ska om du vill migrera korrekt du behandla poolen med högre användning som ”primär” och följ sekvensering reglerna i enlighet med detta.  

I följande tabell innehåller riktlinjer för specifika Migreringsscenarier:

|Aktuella tjänstnivå|Måltjänstnivån|Migreringstyp|Användaråtgärder|
|---|---|---|---|
|Standard|Generellt syfte|Laterala|Kan migrera i valfri ordning, men måste se till att en lämplig vCore storlek *|
|Premium|Affärskritisk|Laterala|Kan migrera i valfri ordning, men måste se till att lämpliga vCore storlek *|
|Standard|Affärskritisk|Uppgradera|Måste migrera sekundära först|
|Affärskritisk|Standard|Nedgradera|Måste migrera primära först|
|Premium|Generellt syfte|Nedgradera|Måste migrera primära först|
|Generellt syfte|Premium|Uppgradera|Måste migrera sekundära först|
|Affärskritisk|Generellt syfte|Nedgradera|Måste migrera primära först|
|Generellt syfte|Affärskritisk|Uppgradera|Måste migrera sekundära först|
||||

\* Varje 100 dtu: er i Standard-nivån kräver minst 1 virtuell kärna och varje 125 DTU på premiumnivån kräver minst 1 virtuell kärna

### <a name="migration-of-failover-groups"></a>Migrering av redundansgrupper

Migrering av redundansgrupper med flera databaser kräver enskilda migreringen av de primära och sekundära databaserna. Samma överväganden och sekvensering regler gäller vid den här processen. När databaserna som har konverterats till den vCore-baserade modellen, redundansgruppen kommer att gälla med samma principinställningar.

### <a name="creation-of-a-geo-replication-secondary"></a>Skapandet av en sekundär geo-replikering

Du kan bara skapa en geo-secondary baserat på samma tjänstenivå som primär. För databasen med hög log generation hastighet vi rekommenderar att sekundärt skapas med samma beräkning storlek som primärt. Om du skapar en geo-secondary i den elastiska poolen för en enskild primär databas, är det bäst att poolen har de `maxVCore` inställning som matchar beräkningsstorleken primära databasen. Om du skapar en geo-secondary i den elastiska poolen för en primär i en annan elastisk pool, är det bäst att poolerna har samma `maxVCore` inställningar

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Använda databaskopian och konvertera en DTU-baserad databas till en vCore-baserad databas

Du kan kopiera en databas med lämplig storlek i DTU-baserad beräkning till en databas med lämplig storlek för vCore-baserad beräkning utan begränsningar eller särskilda ordningsföljd så länge Målstorlek för beräkning har stöd för den maximala databasstorleken av källdatabasen. Databaskopian skapar en ögonblicksbild av data från och med starttiden för kopieringen och utför inte synkronisera data mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- För att få information om specifika storlekar och lagring som kan användas för enkel databas, finns i [SQL Database vCore-baserade resursbegränsningar för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- För att få information om specifika storlekar och lagring som kan användas för elastiska pooler, se [SQL Database vCore-baserade resursbegränsningar för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
