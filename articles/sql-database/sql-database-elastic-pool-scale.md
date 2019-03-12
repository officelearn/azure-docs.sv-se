---
title: Skala resurser för elastisk pool – Azure SQL Database | Microsoft Docs
description: Den här sidan beskriver skala resurser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/06/2019
ms.openlocfilehash: b2ad701115a69520658c2aa9cea53dbda90cf868
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726762"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skala resurser för elastisk pool i Azure SQL Database

Den här artikeln beskriver hur du skalar beräknings- och lagringsresurser som är tillgängliga för elastiska pooler och databaser i pooler i Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Ändra beräkningsresurser (virtuella kärnor eller dtu: er)

När du har valt antalet virtuella kärnor eller edtu: er, du kan skala en elastisk pool upp eller ned dynamiskt utifrån det faktiska resultatet med hjälp av den [Azure-portalen](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), eller [REST-API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effekten av att ändra tjänst-nivå eller skalas om beräkningsstorleken

Tjänsten ändrades till nivå eller beräkna storleken på en elastisk pool följer ett liknande mönster för enskilda databaser och främst innebär att tjänsten utföra följande steg:

1. Skapa ny beräkningsinstans för den elastiska poolen  

    En ny beräkningsinstans för den elastiska poolen har skapats med den begärda tjänstnivå och beräkningsstorleken. För vissa kombinationer av tjänstnivå och beräkning storleksändringar en replik av varje databas måste skapas i den nya compute-instansen som innebär att kopiera data och starkt kan påverka den totala svarstiden. Oavsett databaserna är online i det här steget och anslutningar fortsätter så att de dirigeras till databaser i den ursprungliga beräkningsinstansen.

2. Växla routning av anslutningar till nya beräkningsinstans

    Befintliga anslutningar till databaser i den ursprungliga beräkningsinstansen ignoreras. Alla nya anslutningar upprättas till databaser i den nya compute-instansen. För vissa kombinationer av tjänstnivå och beräkning storleksändringar databasfiler är oberoende och återansluta under växeln.  Oavsett kan växeln resultera i ett kort avbrott när databaser är inte tillgängliga Allmänt i mindre än 30 sekunder och ofta bara några sekunder. Om det finns tidskrävande transaktioner som körs när anslutningar släpps, varaktigheten för det här steget kan ta längre tid för att återställa avbrutna transaktioner. [Accelererad databasåterställning](sql-database-accelerated-database-recovery.md) kan minska påverkan på avbryts tidskrävande transaktioner.

> [!IMPORTANT]
> Inga data förloras under något steg i arbetsflödet.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Svarstiden för att ändra tjänst-nivå eller skalas om beräkningsstorleken

Fördröjning för att ändra tjänstnivå eller skala om beräkningsstorleken för en enkel databas eller elastisk pool är parameteriserat på följande sätt:

|Tjänstenivå|Grundläggande enkel databas</br>Standard (S0-S1)|Grundläggande elastisk pool</br>Standard (S2-S12), </br>I hyperskala </br>Allmänt syfte enkel databas eller elastisk pool|Premium- eller affärskritiska enkel databas eller elastisk pool|
|:---|:---|:---|:---|
|**Grundläggande enkel databas,</br> Standard (S0-S1)**|&bull; &nbsp;Konstant tidsfördröjningen som är oberoende av använt utrymme</br>&bull; &nbsp;Vanligtvis, mindre än 5 minuter|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|
|**Grundläggande elastisk pool, </br>Standard (S2-S12) </br>i hyperskala </br>generell användning enkel databas eller elastisk pool**|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Konstant tidsfördröjningen som är oberoende av använt utrymme</br>&bull; &nbsp;Vanligtvis, mindre än 5 minuter|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|
|**Premium- eller affärskritiska enkel databas eller elastisk pool**|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|&bull; &nbsp;Fördröjning i proportion till databasutrymme användas för kopiering av data</br>&bull; &nbsp;Vanligtvis, mindre än 1 minut per GB utrymme som används|

> [!NOTE]
>
> - När det gäller när du byter tjänstenivå eller skalas om för en elastisk pool databearbetning, bör summering utrymme som används av alla databaser i poolen användas för att beräkna beräkningen.
> - När det gäller flyttar en databas till och från en elastisk pool, påverkar endast det utrymme som används av databasen svarstid, inte utrymmet som används av den elastiska poolen.
>
> [!TIP]
> För att övervaka åtgärder som pågår, se: [Hantera åtgärder med hjälp av REST-API SQL](https://docs.microsoft.com/rest/api/sql/operations/list), [hantera åtgärder med hjälp av CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Ytterligare överväganden när du ändrar tjänsten nivå eller skalas om beräkningsstorleken

- När downsizing virtuella kärnor eller edtu: er för en elastisk pool, måste poolutrymmet används vara mindre än det högsta tillåtna storleken på målet service-nivån och pool edtu: erna.
- När det skalas om virtuella kärnor eller edtu: er för en elastisk pool, gäller en kostnad för extra lagringsutrymme om (1) den högsta lagringsstorleken för poolen stöds av målpoolen och (2) den högsta lagringsstorleken överstiger mängden som lagringsutrymme för målpoolen. Till exempel om en Standard-pool med en maximal storlek på 100 GB med 100 edtu: er är downsized till en 50 eDTU-Standardpool gäller kostnaden för en extra lagring eftersom målpool stöder en maximal storlek på 100 GB och dess lagringsutrymme är endast 50 GB. Så är det extra lagringsutrymmet 100 GB – 50 GB = 50 GB. Priser för extra lagringsutrymme finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än det lagringsutrymme, och sedan detta extra kostnader kan undvikas genom att minska den maximala databasstorleken till mängden som ingår.

### <a name="billing-during-rescaling"></a>Fakturering under skalas om

Du debiteras för varje timme som det finns en databas med hjälp av den högsta tjänstenivå + compute storlek som gällde under den timmen, oavsett användning eller om databasen var aktiv under mindre än en timme. Om du skapar en enkel databas och raderar den fem minuter senare visar din faktura en avgift för en databastimme.

## <a name="change-elastic-pool-storage-size"></a>Ändra lagringsstorleken för elastisk pool

### <a name="vcore-based-purchasing-model"></a>Virtuell kärna-baserad inköpsmodell

- Storage kan etableras upp till den maximala storleksgränsen:

  - För lagring på tjänstnivåerna standard eller allmänna, öka eller minska storleken i steg om 10 GB
  - För lagring i premium- eller affärskritisk tjänstnivåer, öka eller minska storleken i steg om 250 GB
- Lagring för en elastisk pool kan etableras genom att öka eller minska dess maxstorleken.
- Priset för lagring för en elastisk pool är det lagringsutrymmet multiplicerat med a-pris för lagring av tjänstnivån. Mer information om priset för extra lagringsutrymme finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-baserade inköpsmodellen

- EDTU-priset för en elastisk pool innehåller en viss mängd lagringsutrymme utan extra kostnad. Extra lagringsutrymme utöver mängden kan etableras för en ytterligare kostnad upp till den maximala storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB mer än 1 TB. Inkluderad lagring belopp och max storleksgränser finns i [elastisk pool: lagringsstorlekar och storlekar på](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Extra lagringsutrymme för en elastisk pool kan etableras genom att öka sin maximala storlek med hjälp av den [Azure-portalen](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), eller [REST API ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Priset för extra lagringsutrymme för en elastisk pool är det extra lagringsutrymmet multiplicerat med extra lagringsutrymme enhetspriset för tjänstnivån. Mer information om priset för extra lagringsutrymme finns [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Nästa steg

Övergripande resursbegränsningar finns [SQL Database vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) och [SQL Database DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).
