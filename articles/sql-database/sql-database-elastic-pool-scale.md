---
title: Skala resurser för elastisk pool – Azure SQL Database | Microsoft Docs
description: Den här sidan beskriver skala resurser för elastiska pooler i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 0f63739c8718ed7d6625bd18de4fdfff4df60276
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412346"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skala resurser för elastisk pool i Azure SQL Database

Den här artikeln beskriver hur du skalar beräknings- och lagringsresurser som är tillgängliga för elastiska pooler och databaser i pooler i Azure SQL Database. 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>vCore-baserade inköpsmodellen: ändra lagringsstorleken för elastisk pool

- Storage kan etableras upp till den maximala storleksgränsen: 
 - För standardlagring, öka eller minska storleken i steg om 10 GB
 - För Premium storage kan öka eller minska storleken i steg om 250 GB
- Lagring för en elastisk pool kan etableras genom att öka eller minska dess maxstorleken.
- Priset för lagring för en elastisk pool är det lagringsutrymmet multiplicerat med a-pris för lagring av tjänstnivån. Mer information om priset för extra lagringsutrymme finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>vCore-baserade inköpsmodellen: ändra elastisk pool beräkningsresurser (virtuella kärnor)

Du kan öka eller minska prestandanivån till en elastisk pool baserat på resursen behov med hjälp av den [Azure-portalen](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [REST-API](/rest/api/sql/elasticpools/update).

- När det skalas om poolen virtuella kärnor, databasanslutningar kort att tas bort. Det här är samma beteende som inträffar när det skalas om dtu: er för en enskild databas (inte i en pool). Mer information om tidsåtgång och effekt av brutna anslutningar för en databas under skalas om åtgärder finns [skalas om dtu: er för en enskild databas](#single-database-change-storage-size). 
- Tid för att skala om poolen virtuella kärnor kan vara beroende av den totala mängden lagringsutrymme som används av alla databaser i poolen. I allmänhet skalas om fördröjningen beräknar medelvärdet 90 minuter eller mindre per 100 GB. Om det totala utrymmet som används av alla databaser i poolen är exempelvis 200 GB, så är den förväntade svarstiden för rescaling poolen är tre timmar eller mindre. I vissa fall inom Standard eller Basic-nivå kan kan skalas om fördröjningen vara mindre än fem minuter, oavsett hur mycket utrymme som används.
- I allmänhet varaktighet att ändra min virtuella kärnor per databas eller högsta antal virtuella kärnor per databas är fem minuter eller mindre.
- När downsizing pool virtuella kärnor, måste poolutrymmet används vara mindre än det högsta tillåtna storleken på tjänsten target vCores nivå och pool.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>DTU-baserade inköpsmodellen: ändra lagringsstorleken för elastisk pool

- EDTU-priset för en elastisk pool innehåller en viss mängd lagringsutrymme utan extra kostnad. Extra lagringsutrymme utöver mängden kan etableras för en ytterligare kostnad upp till den maximala storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB mer än 1 TB. Inkluderad lagring belopp och max storleksgränser finns i [elastisk pool: lagringsstorlekar och prestandanivåer](#elastic-pool-storage-sizes-and-performance-levels).
- Extra lagringsutrymme för en elastisk pool kan etableras genom att öka sin maximala storlek med hjälp av den [Azure-portalen](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [REST API ](/rest/api/sql/elasticpools/update).
- Priset för extra lagringsutrymme för en elastisk pool är det extra lagringsutrymmet multiplicerat med extra lagringsutrymme enhetspriset för tjänstnivån. Mer information om priset för extra lagringsutrymme finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>DTU-baserade inköpsmodellen: ändra elastisk pool beräkningsresurser (edtu: er)

Du kan öka eller minska resurserna som är tillgängliga i en elastisk pool baserat på resursen behov med hjälp av den [Azure-portalen](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [ REST-API](/rest/api/sql/elasticpools/update).

- När det skalas om poolens edtu: er, databasanslutningar kort att tas bort. Det här är samma beteende som inträffar när det skalas om dtu: er för en enskild databas (inte i en pool). Mer information om tidsåtgång och effekt av brutna anslutningar för en databas under skalas om åtgärder finns [skalas om dtu: er för en enskild databas](#single-database-change-storage-size). 
- Tid för att skala om poolens edtu: er kan vara beroende av den totala mängden lagringsutrymme som används av alla databaser i poolen. I allmänhet skalas om fördröjningen beräknar medelvärdet 90 minuter eller mindre per 100 GB. Om det totala utrymmet som används av alla databaser i poolen är exempelvis 200 GB, så är den förväntade svarstiden för rescaling poolen är tre timmar eller mindre. I vissa fall inom Standard eller Basic-nivå kan kan skalas om fördröjningen vara mindre än fem minuter, oavsett hur mycket utrymme som används.
- I allmänhet varaktighet att ändra min edtu: er per databas eller max edtu: er per databas är fem minuter eller mindre.
- När downsizing poolens edtu: er, måste poolutrymmet används vara mindre än det högsta tillåtna storleken på målet service-nivån och pool edtu: erna.
- När det skalas om poolens edtu: er, gäller en kostnad för extra lagringsutrymme om (1) den högsta lagringsstorleken för poolen stöds av målpoolen och (2) den högsta lagringsstorleken överstiger mängden som lagringsutrymme för målpoolen. Till exempel om en Standard-pool med en maximal storlek på 100 GB med 100 edtu: er är downsized till en 50 eDTU-Standardpool gäller kostnaden för en extra lagring eftersom målpool stöder en maximal storlek på 100 GB och dess lagringsutrymme är endast 50 GB. Så är det extra lagringsutrymmet 100 GB – 50 GB = 50 GB. Priser för extra lagringsutrymme finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än det lagringsutrymme, och sedan detta extra kostnader kan undvikas genom att minska den maximala databasstorleken till mängden som ingår. 

## <a name="next-steps"></a>Nästa steg

Övergripande resursbegränsningar finns [SQL Database vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) och [SQL Database DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).
