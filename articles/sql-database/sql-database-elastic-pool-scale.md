---
title: Skala resurser i elastiska pooler
description: På den här sidan beskrivs skalning av resurser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462606"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skala elastiska poolresurser i Azure SQL Database

I den här artikeln beskrivs hur du skalar beräknings- och lagringsresurser som är tillgängliga för elastiska pooler och poolade databaser i Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Ändra beräkningsresurser (virtuella kärnor eller DU:er)

När du först har plockat antalet virtuella kärnor eller eDU:er kan du skala en elastisk pool uppåt eller nedåt dynamiskt baserat på faktisk upplevelse med hjälp av [Azure-portalen,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effekten av att ändra tjänstnivå eller skala om beräkningsstorleken

Ändra tjänstnivån eller beräkningsstorleken för en elastisk pool följer ett liknande mönster som för enskilda databaser och innebär huvudsakligen att tjänsten utför följande steg:

1. Skapa ny beräkningsinstans för den elastiska poolen  

    En ny beräkningsinstans för den elastiska poolen skapas med den begärda tjänstnivån och beräkningsstorleken. För vissa kombinationer av tjänstnivå och ändring av beräkningsstorlek måste en replik av varje databas skapas i den nya beräkningsinstansen som innebär att du kopierar data och kan starkt påverka den övergripande svarstiden. Oavsett, databaserna förblir online under det här steget, och anslutningar fortsätter att dirigeras till databaserna i den ursprungliga beräkningsinstansen.

2. Växla routning av anslutningar till ny beräkningsinstans

    Befintliga anslutningar till databaserna i den ursprungliga beräkningsinstansen tas bort. Alla nya anslutningar upprättas till databaserna i den nya beräkningsinstansen. För vissa kombinationer av tjänstnivå och ändringar av beräkningsstorlek tas databasfiler bort och återansluts under växeln.  Oavsett kan växeln resultera i ett kort avbrott i tjänsten när databaser inte är tillgängliga i allmänhet i mindre än 30 sekunder och ofta bara några sekunder. Om det finns tidskrävande transaktioner som körs när anslutningar tas bort kan varaktigheten för det här steget ta längre tid för att återställa avbrutna transaktioner. [Snabbare databasåterställning](sql-database-accelerated-database-recovery.md) kan minska effekten från att avbryta långvariga transaktioner.

> [!IMPORTANT]
> Inga data går förlorade under något steg i arbetsflödet.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Svarstid för att ändra tjänstnivå eller skala om beräkningsstorlek

Den uppskattade svarstiden för att ändra tjänstnivån eller skala om beräkningsstorleken för en enskild databas eller elastisk pool är parameteriserad enligt följande:

|Tjänstenivå|Grundläggande enskild databas,</br>Standard (S0-S1)|Grundläggande elastisk pool,</br>Standard (S2-S12), </br>Hyperskala, </br>Allmän databas för allmänt ändamål eller elastisk pool|Premium eller affärskritisk enda databas eller elastisk pool|
|:---|:---|:---|:---|
|**Grundläggande enskild</br> databas, Standard (S0-S1)**|&bull;&nbsp;Konstant tidsfördröjning oberoende av det utrymme som används</br>&bull;&nbsp;Normalt är mindre än 5 minuter|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|
|**Grundläggande elastisk </br>pool, Standard (S2-S12), </br>Hyperskala, </br>allmän databas eller elastisk pool**|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Konstant tidsfördröjning oberoende av det utrymme som används</br>&bull;&nbsp;Normalt är mindre än 5 minuter|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|
|**Premium eller affärskritisk enda databas eller elastisk pool**|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|&bull;&nbsp;Latens proportionell mot databasutrymme som används på grund av datakopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB utrymme som används|

> [!NOTE]
>
> - När det gäller att ändra tjänstnivån eller skala om beräkning för en elastisk pool, bör summering av utrymme som används i alla databaser i poolen användas för att beräkna uppskattningen.
> - När det gäller att flytta en databas till/från en elastisk pool påverkar endast det utrymme som används av databasen svarstiden, inte det utrymme som används av den elastiska poolen.
>
> [!TIP]
> Om du vill övervaka pågående åtgärder läser du: [Hantera åtgärder med SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), Hantera åtgärder med [CLI](/cli/azure/sql/db/op), Övervaka åtgärder [med T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Ytterligare överväganden vid ändring av tjänstnivå eller omskalning av beräkningsstorlek

- När du ändrar storlek på virtuella kärnor eller eDTU:er för en elastisk pool måste utrymmet för poolen vara mindre än den högsta tillåtna storleken på måltjänstnivån och pool-EDTU:er.
- Vid omskalning av eDTI:er för en elastisk pool tillkommer en extra lagringskostnad om (1) poolens lagringsmaximitet stöds av målpoolen och (2) lagrings maxstorleken överstiger den medföljande lagringsmängden för målpoolen. Om till exempel en 100 eDTU Standard pool med en maxstorlek på 100 GB är nedstämd till en 50 eDTU Standard pool, då en extra lagringskostnad gäller eftersom målpoolen stöder en maxstorlek på 100 GB och dess medföljande lagringsmängd är endast 50 GB. Så är den extra lagringsmängden 100 GB – 50 GB = 50 GB. För prissättning av extra lagringsutrymme, se [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än det inkluderade lagringsbeloppet, kan den här extra kostnaden undvikas genom att minska databasens maxstorlek till det medföljande beloppet.

### <a name="billing-during-rescaling"></a>Fakturering vid omskalning

Du debiteras för varje timme som en databas finns med den högsta tjänstnivån + beräkningsstorleken som tillämpades under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme. Om du till exempel skapar en enskild databas och tar bort den fem minuter senare visar fakturan en debitering för en databastimme.

## <a name="change-elastic-pool-storage-size"></a>Ändra lagringsstorlek för elastisk pool

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

- Lagring kan etableras upp till maxstorleksgränsen:

  - För lagring på standard- eller tjänstnivåerna för allmänt bruk ökar eller minskar du storleken i steg om 10 GB
  - För lagring på premium- eller affärskritiska tjänstnivåer ökar eller minskar du storleken i steg om 250 GB
- Lagring för en elastisk pool kan etableras genom att öka eller minska dess maxstorlek.
- Priset för lagring för en elastisk pool är lagringsbeloppet multiplicerat med lagringsenhetspriset för tjänstnivån. Mer information om priset på extra lagringsutrymme finns i [SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-baserad inköpsmodell

- I eDTU-priset för en elastisk pool ingår en viss mängd lagringsutrymme utan extra kostnad. Extra lagringsutrymme utöver det medföljande beloppet kan etableras för en extra kostnad upp till maxstorleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB längre än 1 TB. För inkluderade lagringsbelopp och maxstorleksgränser finns i [Elastisk pool: lagringsstorlekar och beräkningsstorlekar](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Extra lagringsutrymme för en elastisk pool kan etableras genom att öka dess maxstorlek med hjälp av [Azure-portalen,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Priset för extra lagringsutrymme för en elastisk pool är det extra lagringsbeloppet multiplicerat med det extra priset för lagringsenhet på servicenivån. Mer information om priset på extra lagringsutrymme finns i [SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Nästa steg

Övergripande resursgränser finns i [SQL Database vCore-baserade resursgränser - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) och [DTU-baserade resursgränser för SQL Database - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).
