---
title: Skala elastiska pool resurser – Azure SQL Database | Microsoft Docs
description: På den här sidan beskrivs skalnings resurser för elastiska pooler i Azure SQL Database.
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
ms.openlocfilehash: c96be7930a33185077134d051b49cba0695327e3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568645"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skala elastiska pool resurser i Azure SQL Database

Den här artikeln beskriver hur du skalar beräknings-och lagrings resurserna som är tillgängliga för elastiska pooler och databaser i pooler i Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Ändra beräknings resurser (virtuella kärnor eller DTU: er)

När du först har valt antalet virtuella kärnor eller eDTU: er kan du skala upp eller ned en elastisk pool dynamiskt baserat på den faktiska upplevelsen med hjälp av [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [POWERSHELL](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effekt av att ändra tjänst nivå eller skala om beräknings storlek

Att ändra tjänst nivå eller beräknings storlek för en elastisk pool följer ett liknande mönster som för enskilda databaser och inbegriper främst tjänsten som utför följande steg:

1. Skapa en ny beräknings instans för den elastiska poolen  

    En ny beräknings instans för den elastiska poolen skapas med den begärda tjänst nivån och beräknings storleken. För vissa kombinationer av tjänst nivå och beräknings storlek måste en replik av varje databas skapas i den nya beräknings instansen som inbegriper kopiering av data och kan starkt påverka den totala svars tiden. Oavsett, är databaserna online under det här steget, och anslutningar fortsätter att dirigeras till databaserna i den ursprungliga beräknings instansen.

2. Växla routning av anslutningar till en ny beräknings instans

    Befintliga anslutningar till databaserna i den ursprungliga beräknings instansen tas bort. Alla nya anslutningar upprättas till databaserna i den nya beräknings instansen. För vissa kombinationer av tjänst nivå och beräknings storlek, kopplas databasfilerna bort och återkopplas under växeln.  Oavsett vilket kan växeln resultera i ett kort avbrott när databaser inte är tillgängliga generellt i mindre än 30 sekunder och ofta i några sekunder. Om det finns tids krävande transaktioner som körs när anslutningar släpps, kan varaktigheten för det här steget ta längre tid för att återställa avbrutna transaktioner. [Accelererad databas återställning](sql-database-accelerated-database-recovery.md) kan minska påverkan från att avbryta tids krävande transaktioner.

> [!IMPORTANT]
> Inga data förloras under något steg i arbets flödet.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Svars tid för ändring av tjänst nivå eller skalning av beräknings storlek

Den uppskattade svars tiden för att ändra tjänst nivån eller skala om beräknings storleken för en enskild databas eller elastisk pool är parameterstyrda enligt följande:

|Tjänstnivå|Enkel databas,</br>Standard (S0-S1)|Basic elastisk pool,</br>Standard (S2-S12) </br>Hyperskala </br>Generell användning enskild databas eller elastisk pool|Premium-eller Affärskritisk enkel databas eller elastisk pool|
|:---|:---|:---|:---|
|**Enkel databas,</br> standard (S0-S1)**|&bull;&nbsp;Tidssvars tid för konstant som är oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 5 minuter|&bull;&nbsp;En latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;&nbsp;En latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|
|**Basic elastisk pool, </br>standard (S2-S12), </br>storskalig, </br>generell användning enskild databas eller elastisk pool**|&bull;&nbsp;En latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Tidssvars tid för konstant som är oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 5 minuter|&bull;&nbsp;En latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|
|**Premium-eller Affärskritisk enkel databas eller elastisk pool**|&bull;&nbsp;En latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;&nbsp;En latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|&bull;&nbsp;En latens som är proportionell till databas utrymmet som används på grund av data kopiering</br>&bull;&nbsp;Normalt är mindre än 1 minut per GB använt utrymme|

> [!NOTE]
>
> - Om du ändrar tjänst nivån eller skalnings beräkning för en elastisk pool, bör summering av utrymmet som används i alla databaser i poolen användas för att beräkna uppskattningen.
> - Om du flyttar en databas till/från en elastisk pool, påverkar endast det utrymme som används av databasen svars tiden, inte det utrymme som används av den elastiska poolen.
>
> [!TIP]
> Information om hur du övervakar pågående åtgärder finns i: [Hantera åtgärder med hjälp av SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list), [Hantera åtgärder med CLI](/cli/azure/sql/db/op), [övervaka åtgärder med hjälp av T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Ytterligare överväganden vid ändring av tjänst nivå eller skalning av beräknings storlek

- När downsizing virtuella kärnor eller eDTU: er för en elastisk pool, måste det förbrukade utrymmet vara mindre än den största tillåtna storleken för mål tjänst nivån och pool eDTU: er.
- Vid skalning av virtuella kärnor-eller eDTU: er för en elastisk pool gäller en extra lagrings kostnad om (1) den maximala lagrings storleken för poolen stöds av adresspoolen och (2) den maximala storleken för lagrings utrymmet överskrider den inkluderade lagrings mängden för mål poolen. Om till exempel en 100 eDTU-standardpool med en Max storlek på 100 GB är downsized till en 50 eDTU-standardpool, kommer en extra lagrings kostnad att gälla eftersom mål poolen har stöd för en maximal storlek på 100 GB och den inkluderade lagrings mängden endast är 50 GB. Det extra lagrings utrymmet är 100 GB – 50 GB = 50 GB. Prissättning av extra lagrings utrymme finns [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden utrymme som används är mindre än den mängd lagrings mängd som används, kan den här extra kostnaden undvikas genom att max storleken för databasen minskas till den inkluderade mängden.

### <a name="billing-during-rescaling"></a>Fakturering under omskalning

Du debiteras för varje timme som det finns en databas med den högsta tjänst nivån och den beräknings storlek som tillämpas under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme. Om du till exempel skapar en enskild databas och tar bort den fem minuter senare, motsvarar din faktura en avgift för en databas timme.

## <a name="change-elastic-pool-storage-size"></a>Ändra lagrings storlek för elastisk pool

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

- Lagringen kan allokeras till max storleks gränsen:

  - Öka eller minska storleken i steg om 10 GB för lagring i tjänst nivåerna standard eller allmän användning
  - Öka eller minska storleken i steg om 250 GB för lagring i tjänst nivåerna Premium eller Business Critical
- Lagring för en elastisk pool kan tillhandahållas genom att öka eller minska dess Max storlek.
- Priset för lagring för en elastisk pool är lagrings beloppet multiplicerat med lagrings enhets priset för tjänst nivån. Mer information om priset för extra lagring finns [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>DTU-baserad inköps modell

- EDTU-priset för en elastisk pool omfattar en viss mängd lagring utan extra kostnad. Extra lagring utöver den mängd som ingår kan tillhandahållas för ytterligare kostnad upp till den maximala storleks gränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB utöver 1 TB. För inkluderade lagrings mängder och Max storleks gränser, se [elastisk pool: lagrings storlekar och beräknings storlekar](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Extra lagrings utrymme för en elastisk pool kan tillhandahållas genom att öka den maximala storleken med hjälp av [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [POWERSHELL](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)eller [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Priset för extra lagring för en elastisk pool är det extra lagrings beloppet multiplicerat med det extra lagrings enhets priset för tjänst nivån. Mer information om priset för extra lagring finns [SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Nästa steg

För övergripande resurs gränser, se [SQL Database vCore resurs gränser-elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md) och [SQL Database DTU-baserade resurs gränser-elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).
