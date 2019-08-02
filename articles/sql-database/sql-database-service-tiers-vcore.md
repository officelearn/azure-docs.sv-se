---
title: Azure SQL Database-vCore | Microsoft Docs
description: Med den vCore-baserade inköps modellen kan du skala beräknings-och lagrings resurser oberoende av varandra, matcha lokala prestanda och optimera priset.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 06/26/2019
ms.openlocfilehash: e5af3803ebb4cb0a88a082d3c85d0df68da8d1b8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566626"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Välj bland vCore-tjänst nivåerna och migrera från DTU-tjänstens nivåer

Med den virtuella Core-baserade inköps modellen (vCore) kan du skala beräknings-och lagrings resurser oberoende av varandra, matcha lokala prestanda och optimera priset. Du kan också välja att skapa maskin vara:

- **Gen4**: Upp till 24 logiska processorer baserade på Intel E5-2673 v3 (Haswell) 2,4-GHz-processorer, vCore = 1 PP (fysisk kärna), 7 GB per kärna, kopplat SSD
- **Gen5**: Upp till 80 logiska processorer baserade på Intel E5-2673 v4 (Broadwell) 2,3-GHz-processorer, vCore = 1 LP (Hyper-Thread), 5,1 GB per kärna, snabb eNVM SSD

Gen4-maskinvara erbjuder betydligt mer minne per vCore. Med Gen5 maskin vara kan du dock skala upp beräknings resurserna mycket högre.

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i AustraliaEast-regionen.
> [!NOTE]
> Information om DTU-baserade tjänst nivåer finns i [tjänst nivåer för den DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md). Information om skillnaderna mellan tjänst nivåerna för DTU-baserade och vCore-baserade inköps modeller finns i [Azure SQL Database inköps modeller](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Egenskaper för tjänst nivå

Den vCore-baserade inköps modellen tillhandahåller tre tjänst nivåer: generell användning, storskalighet och affärs kritisk. Dessa tjänst nivåer särskiljs med en mängd beräknings storlekar, hög tillgänglighets design, metoder för fel isolering, typer och storlekar för lagring och I/O-intervall.

Du måste separat konfigurera nödvändiga lagrings-och kvarhållningsperiod för säkerhets kopieringar. Om du vill ställa in säkerhets kopierings perioden öppnar du Azure Portal, går till servern (inte databasen) och går sedan till **hantera säkerhets kopior** > **Konfigurera princip** > **punkt i tids återställning konfiguration** > **7- 35 dagar**.

I följande tabell förklaras skillnaderna mellan de tre nivåerna:

||**Generellt syfte**|**Verksamhets kritisk**|**Hyperskala**|
|---|---|---|---|
|Passar bäst|De flesta företags arbets belastningar. Erbjuder budget orienterade, balanserade och skalbara beräknings-och lagrings alternativ.|Affärs program med höga I/O-krav. Ger högsta möjliga återhämtning till problem genom att använda flera isolerade repliker.|De flesta företags arbets belastningar med mycket skalbara lagrings-och Läs skalnings krav.|
|Compute|**Allokerad beräkning**:<br/>Gen4 1 till 24 virtuella kärnor<br/>Gen5 2 till 80 virtuella kärnor<br/>**Server lös beräkning**:<br/>Gen5 0,5 – 4 virtuella kärnor|**Allokerad beräkning**:<br/>Gen4 1 till 24 virtuella kärnor<br/>Gen5 2 till 80 virtuella kärnor|**Allokerad beräkning**:<br/>Gen4 1 till 24 virtuella kärnor<br/>Gen5 2 till 80 virtuella kärnor|
|Minne|**Allokerad beräkning**:<br/>Gen4 7 GB per vCore<br/>Gen5 5,1 GB per vCore<br/>**Server lös beräkning**:<br/>Gen5 3 GB per vCore|**Allokerad beräkning**:<br/>Gen4 7 GB per vCore<br/>Gen5 5,1 GB per vCore |**Allokerad beräkning**:<br/>Gen4 7 GB per vCore<br/>Gen5 5,1 GB per vCore|
|Storage|Använder Fjärrlagring.<br/>**Allokerad data bearbetning för enskild databas**:<br/>5 GB – 4 TB<br/>**Beräkning av Server lös enkel databas**:<br/>5 GB-1 TB<br/>**Hanterad instans**: 32 GB - 8 TB |Använder lokal SSD-lagring.<br/>**Allokerad data bearbetning för enskild databas**:<br/>5 GB – 4 TB<br/>**Hanterad instans**:<br/>32 GB – 4 TB |Flexibel automatisk storleks ökning av lagring vid behov. Har stöd för upp till 100 TB lagrings utrymme. Använder lokal SSD-lagring för lokal cache för buffring och lokal data lagring. Använder Azure Fjärrlagring som sista långsiktigt långsiktigt data lager. |
|I/O-genomflöde (ungefärligt)|**Enkel databas**: 500 IOPS per vCore med 7000 högsta IOPS.<br/>**Hanterad instans**: Beror på [fil storleken](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS per kärna med 200 000 högsta IOPS|Hög skalning är en arkitektur med flera nivåer med cachelagring på flera nivåer. Effektiv IOPs är beroende av arbets belastningen.|
|Tillgänglighet|1 replik, inga storskaliga repliker|3 repliker, 1 [storskalig replik](sql-database-read-scale-out.md),<br/>zon-redundant hög tillgänglighet (HA)|1 Läs-och skriv replik, plus [](sql-database-read-scale-out.md) 0-4 storskalig repliker|
|Säkerhetskopior|[Geo-redundant lagring med Läs behörighet (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagar (7 dagar som standard)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagar (7 dagar som standard)|Ögonblicks bilds säkerhets kopieringar i Azure Remote Storage. Återställningar använder dessa ögonblicks bilder för snabb återställning. Säkerhets kopieringar är omedelbara och påverkar inte beräknings-I/O-prestanda. Återställningar är snabba och är inte en storleks data åtgärd (tar några minuter i stället för timmar eller dagar).|
|Minnesintern|Stöds inte|Stöds|Stöds inte|
|||

> [!NOTE]
> Du kan få en kostnads fri Azure SQL-databas på tjänst nivån Basic tillsammans med ett kostnads fritt Azure-konto. Mer information finns i [skapa en hanterad moln databas med ditt kostnads fria Azure-konto](https://azure.microsoft.com/free/services/sql-database/).

- Mer information om vCore resurs gränser finns i [vCore Resource Limits in a Single Database](sql-database-vcore-resource-limits-single-databases.md) och [vCore Resource Limits in en hanterad instans](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Mer information om allmänna syften och affärs kritiska tjänst nivåer finns i [allmänna syften och affärs kritiska tjänst nivåer](sql-database-service-tiers-general-purpose-business-critical.md).
- Mer information om den storskaliga tjänst nivån i den vCore-baserade inköps modellen finns i [storskalig Service Tier](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid-förmån

I den etablerade beräknings nivån för den vCore-baserade inköps modellen kan du byta ut dina befintliga licenser för rabatterade priser på SQL Database genom att använda [Azure Hybrid-förmån för SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Med den här Azure-förmånen kan du Spara upp till 30 procent på Azure SQL Database genom att använda dina lokala SQL Server licenser med Software Assurance.

![prissättning](./media/sql-database-service-tiers/pricing.png)

Med Azure Hybrid-förmån kan du välja att bara betala för den underliggande Azure-infrastrukturen genom att använda din befintliga SQL Server-licens för själva SQL Database-motorn (bas beräknings priser) eller så kan du betala för både den underliggande infrastrukturen och SQL Server licens (licens pris ingår).

Du kan välja eller ändra licensierings modellen med hjälp av Azure Portal eller genom att använda någon av följande API: er:

- Ange eller uppdatera licens typen med hjälp av PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Ange eller uppdatera licens typen med hjälp av Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi-uppdatering](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Ange eller uppdatera licens typen genom att använda REST API:

  - [Databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databaser – uppdatera](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Hanterade instanser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Hanterade instanser – uppdatera](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrera från den DTU-baserade modellen till den vCore-baserade modellen

### <a name="migrate-a-database"></a>Migrera en databas

Migrering av en databas från den DTU-baserade inköps modellen till den vCore-baserade inköps modellen liknar uppgradering eller nedgradering mellan standard-och premium tjänst nivåerna i den DTU-baserade inköps modellen.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrera databaser med länkar till geo-replikering

Migrering från den DTU-baserade modellen till den vCore-baserade inköps modellen liknar att uppgradera eller nedgradera geo-replikeringsrelationen mellan databaser på standard-och premium-tjänst nivåerna. Under migreringen behöver du inte stoppa geo-replikering, men du måste följa dessa ordningsföljds regler:

- När du uppgraderar måste du uppgradera den sekundära databasen först och sedan uppgradera den primära databasen.
- Ändra ordning när du nedgraderar ordningen: du måste nedgradera den primära databasen först och sedan nedgradera den sekundära.

När du använder geo-replikering mellan två elastiska pooler rekommenderar vi att du anger en pool som primär och den andra som den sekundära. I så fall bör du använda samma rikt linjer när du migrerar elastiska pooler. Men om du har elastiska pooler som innehåller både primära och sekundära databaser, behandlar poolen med den högre användningen som primär och följer ordningsföljden för ordningsföljd.  

Följande tabell innehåller vägledning för olika scenarier för migrering:

|Aktuell tjänst nivå|Mål tjänst nivå|Typ av migrering|Användaråtgärder|
|---|---|---|---|
|Standard|Allmänt|Lateral|Kan migrera i vilken ordning som helst, men måste säkerställa lämplig vCore storlek *|
|Premium|Affärskritisk|Lateral|Kan migrera i vilken ordning som helst, men måste säkerställa lämplig vCore storlek *|
|Standard|Affärskritisk|Uppgradera|Måste migrera sekundär första|
|Affärskritisk|Standard|Nedgradera|Måste migrera primär första|
|Premium|Allmänt|Nedgradera|Måste migrera primär första|
|Allmänt|Premium|Uppgradera|Måste migrera sekundär första|
|Affärskritisk|Allmänt|Nedgradera|Måste migrera primär första|
|Allmänt|Affärskritisk|Uppgradera|Måste migrera sekundär första|
||||

\*Varje 100 DTU: er på standard nivån kräver minst 1 vCore och varje 125-DTU: er på Premium-nivån kräver minst 1 vCore.

### <a name="migrate-failover-groups"></a>Migrera redundansväxla grupper

Migrering av failover-grupper med flera databaser kräver en individuell migrering av de primära och sekundära databaserna. Under den processen gäller samma överväganden och ordningsföljd regler. När databaserna har konverterats till den vCore-baserade inköps modellen, kommer gruppen för redundans att gälla med samma princip inställningar.

### <a name="create-a-geo-replication-secondary-database"></a>Skapa en sekundär databas för geo-replikering

Du kan skapa en sekundär databas för geo-replikering (endast en geo-sekundär) med samma tjänst nivå som du använde för den primära databasen. För databaser med hög logg skapande frekvens rekommenderar vi att du skapar geo-Secondary med samma beräknings storlek som den primära.

Om du skapar en geo-sekundär i den elastiska poolen för en enda primär databas kontrollerar du att `maxVCore` inställningen för poolen matchar den primära databasens beräknings storlek. Om du skapar en geo-Secondary för en primär i en annan elastisk pool, rekommenderar vi att poolerna har samma `maxVCore` inställningar.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Använd databas kopiering för att konvertera en DTU-baserad databas till en vCore-baserad databas

Du kan kopiera alla databaser med en DTU-baserad beräknings storlek till en databas med en vCore beräknings storlek utan begränsningar eller särskilda sekvenser så länge mål beräknings storleken har stöd för den maximala databas storleken för käll databasen. Databas kopian skapar en ögonblicks bild av data från den tidpunkt då kopieringen startar och synkroniserar inte data mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- De angivna beräknings storlekarna och lagrings storleks alternativen för enskilda databaser finns i [SQL Database vCore resurs gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- De angivna beräknings storlekarna och lagrings storleks alternativen för elastiska pooler finns i [SQL Database vCore resurs gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
