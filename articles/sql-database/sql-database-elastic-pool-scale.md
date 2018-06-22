---
title: Skala elastisk pool - Azure SQL Database | Microsoft Docs
description: Den här sidan beskrivs skalning resurser för elastiska pooler i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 0d15382f413485ccc287bac945b3c88eb748a9f6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313320"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skala elastisk pool resurser i Azure SQL Database

Den här artikeln beskriver hur du kan skala beräkning och lagring-resurser tillgängliga för elastiska pooler och grupperade databaser i Azure SQL Database. 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>vCore-baserade inköpsmodell: ändra lagringsstorlek elastisk pool

- Lagring kan du konfigurera upp till den maximala storleksgränsen: 
 - För standardlagring, öka eller minska storleken i steg 10 GB
 - För Premium-lagring, öka eller minska storleken i steg om 250 GB
- Lagringsutrymme för en elastisk pool kan etableras genom att öka eller minska den maximala storleken.
- Priset för lagring för en elastisk pool är lagringsmängden multiplicerat med lagring enhetspriset för tjänstnivån. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>vCore-baserade inköpsmodell: ändra elastisk pool beräkningsresurser (vCores)

Du kan öka eller minska prestandanivån uppdateras till en elastisk pool utifrån behov med hjälp av den [Azure-portalen](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [REST API](/rest/api/sql/elasticpools/update).

- När rescaling pool vCores släpps kort databasanslutningar. Det här är samma beteende som inträffar när rescaling dtu: er för en enskild databas (inte i en pool). Mer information om tidsåtgång och effekt av borttagna anslutningar för en databas under rescaling åtgärder finns [Rescaling dtu: er för en enskild databas](#single-database-change-storage-size). 
- Varaktigheten för skala om poolen vCores kan vara beroende av den totala mängden lagringsutrymme som används av alla databaser i poolen. I allmänhet rescaling svarstiden medelvärden 90 minuter eller mindre per 100 GB. Om det totala utrymmet som används av alla databaser i poolen är exempelvis 200 GB och den förväntade svarstiden för rescaling poolen är 3 timmar eller mindre. I vissa fall i Standard- eller Basic-nivån vara rescaling fördröjningen under 5 minuter, oavsett hur mycket diskutrymme som används.
- I allmänhet varaktighet ändra min vCores per databas eller max vCores per databas är fem minuter eller mindre.
- När downsizing pool vCores måste poolutrymmet används vara mindre än den största tillåtna storleken för tjänsten mål vCores nivå och pool.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>DTU-baserade inköpsmodell: ändra lagringsstorlek elastisk pool

- EDTU priset för en elastisk pool innehåller en viss mängd lagring utan extra kostnad. Extra lagring utöver det inkluderade beloppet kan etableras för en extra kostnad upp till max storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB utöver 1 TB. Inkluderade lagring belopp och max storleksgränser finns [elastisk pool: lagringsstorlekar och prestandanivåer](#elastic-pool-storage-sizes-and-performance-levels).
- Extra lagring för en elastisk pool kan etableras genom att öka den maximala storleken som använder den [Azure-portalen](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [REST API ](/rest/api/sql/elasticpools/update).
- Priset för extra lagring för en elastisk pool är den extra lagring multiplicerat med enhetspriset extra lagring för tjänstnivån. Mer information om priserna för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>DTU-baserade inköpsmodell: ändra elastisk pool beräkningsresurser (edtu: er)

Du kan öka eller minska resurserna som är tillgängliga för en elastisk pool utifrån behov med hjälp av den [Azure-portalen](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), eller [ REST-API](/rest/api/sql/elasticpools/update).

- När rescaling pool-edtu: er har en kort databasanslutningar släpps. Det här är samma beteende som inträffar när rescaling dtu: er för en enskild databas (inte i en pool). Mer information om tidsåtgång och effekt av borttagna anslutningar för en databas under rescaling åtgärder finns [Rescaling dtu: er för en enskild databas](#single-database-change-storage-size). 
- Varaktigheten för skala om pool-edtu: er kan vara beroende av den totala mängden lagringsutrymme som används av alla databaser i poolen. I allmänhet rescaling svarstiden medelvärden 90 minuter eller mindre per 100 GB. Om det totala utrymmet som används av alla databaser i poolen är exempelvis 200 GB och den förväntade svarstiden för rescaling poolen är 3 timmar eller mindre. I vissa fall i Standard- eller Basic-nivån vara rescaling fördröjningen under 5 minuter, oavsett hur mycket diskutrymme som används.
- I allmänhet varaktighet ändra min edtu: er per databas eller max edtu: er per databas är fem minuter eller mindre.
- När downsizing pool-edtu: er måste poolutrymmet används vara mindre än den största tillåtna storleken för den mål service nivå och pool-edtu: er.
- När rescaling pool-edtu: er, gäller en extra lagringskostnaden om (1) den maximala lagringsstorleken för poolen stöds av målpoolen och (2) den maximala lagringsstorleken överskrider inkluderade lagring målpoolen. Till exempel om en 100 eDTU Standardpool med en maximal storlek på 100 GB downsized till en 50 eDTU Standardpool, gäller en extra lagringskostnaden eftersom målpool stöder en maximal storlek på 100 GB och dess inkluderade lagringsutrymmet är bara 50 GB. Därför är det extra lagringsutrymmet 100 GB – 50 GB = 50 GB. Priser för extra lagring finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden diskutrymme som används är mindre än den inkluderade lagringsmängden, kan sedan detta extra kostnad undvikas genom att minska den maximala databasstorleken så ingår. 

## <a name="next-steps"></a>Nästa steg

Övergripande resursbegränsningar finns [SQL-databas vCore-baserade gränserna - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) och [SQL Database DTU-baserade gränserna - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).
