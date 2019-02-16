---
title: Azure SQL Database vCore-baserade resursbegränsningar - elastiska pooler | Microsoft Docs
description: Den här sidan beskriver några vanliga vCore-baserade resursbegränsningar för elastiska pooler i Azure SQL Database.
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
ms.date: 02/15/2019
ms.openlocfilehash: b0bd5c0ba8641bd287951168485be715a0b46224
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328681"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Resursgränser för elastiska pooler när gränserna vCore-baserade inköpschef modell

Den här artikeln innehåller detaljerade resursgränser för Azure SQL Database elastiska pooler och databaser i en pool med hjälp av den vCore-baserade inköpsmodellen.

DTU-baserade gränser för inköpschef modellen för finns i [SQL Database DTU-baserade resursbegränsningar - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

Du kan ange tjänstnivå, beräkningsstorleken och storage mycket med den [Azure-portalen](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), eller [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Resursbegränsningar enskilda databaser i elastiska pooler är vanligtvis samma som för enskilda databaser utanför pooler som har samma compute storlek. Max samtidiga arbetare för en GP_Gen4_1-databas är till exempel 200 arbetare. Max samtidiga arbetare för en databas i poolen GP_Gen4_1 är därför också 200 arbetare. Observera att det totala antalet samtidiga arbetare i GP_Gen4_1 pool är 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Tjänstnivå för allmänna ändamål: Lagringsstorlekar och storlekar

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Tjänstnivå för allmänna ändamål: Generation 4-beräkningsplattformen (del 1)

|Beräkna storlek|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|512|756|756|1536|1536|1536|
|Maximal storlek|154|227|227|461|461|461|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Mål-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Maximalt antal samtidiga arbetare per pool (begäranden) * |210|420|630|840|1050|1260|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Maximalt antal databaser per pool|100|200|300|500|500|500|
|Min/max-databaspool vCore val per databas|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Tjänstnivå för allmänna ändamål: Generation 4-beräkningsplattformen (del 2)

|Beräkna storlek|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|168|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|1536|2048|2048|2048|3584|4096|
|Maximal storlek (GB)|461|614|614|614|1075|1229|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Mål-IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Maximalt antal samtidiga arbetare per pool (begäranden) *|1470|1680|1890|2 100|3360|5040|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Maximalt antal databaser per pool|200|500|500|500|500|500|
|Min/max-databaspool vCore val per databas|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Tjänstnivå för allmänna ändamål: Generation 5-beräkningsplattformen (del 1)

|Beräkna storlek|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Minne (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|512|756|756|1536|1536|1536|
|Maximal storlek (GB)|154|227|227|461|461|461|461|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Mål-IOPS (64 KB)|500|1000|1500|2000|2500|3000|3500|
|Maximalt antal samtidiga arbetare per pool (begäranden) *|210|420|630|840|1050|1260|1470|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Maximalt antal databaser per pool|200|500|500|500|500|500|500|
|Min/max-databaspool vCore val per databas|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Tjänstnivå för allmänna ändamål: Generation 5-beräkningsplattformen (del 2)

|Beräkna storlek|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Minne (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|2048|2048|3072|3072|4096|4096|4096|
|Maximal storlek (GB)|614|614|922|922|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Mål-IOPS (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Maximalt antal samtidiga arbetare per pool (begäranden) *|1680|1890|2 100|2520|33600|4200|8400|
|Maximalt antal databaser per pool|500|500|500|500|500|500|500|
|Min/max-databaspool vCore val per databas|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Kritiska-affärsnivå: Lagringsstorlekar och storlekar

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Kritiska-affärsnivå: Generation 4-beräkningsplattformen (del 1)

|Beräkna storlek|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Minne (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Stöd för Columnstore|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|InMemory-OLTP-lagring (GB)|1|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Maximal storlek (GB)|307|307|307|307|307|307|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Maximalt antal samtidiga arbetare per pool (begäranden) *|210|420|630|840|1050|1260|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Maximalt antal databaser per pool|Endast enskild databaser har stöd för den här beräkningsstorleken|50|100|100|100|100|
|Min/max-databaspool vCore val per databas|Gäller inte|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Kritiska-affärsnivå: Generation 4-beräkningsplattformen (del 2)

|Beräkna storlek|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Minne (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Stöd för Columnstore|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|InMemory-OLTP-lagring (GB)|7|8|9.5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Maximal storlek (GB)|307|307|307|307|307|307|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Maximalt antal samtidiga arbetare per pool (begäranden) *|1470|1680|1890|2 100|3360|5040|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Maximalt antal databaser per pool|100|100|100|100|100|100|
|Min/max-databaspool vCore val per databas|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Kritiska-affärsnivå: Generation 5-beräkningsplattformen (del 1)

|Beräkna storlek|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Minne (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|Maximal datastorlek (GB)|1024|1024|1024|1536|1536|1536|1536|
|Maximal storlek (GB)|307|307|307|461|461|461|461|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|35000|
|Maximalt antal samtidiga arbetare per pool (begäranden) *|210|420|630|840|1050|1260|1470|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Maximalt antal databaser per pool|Endast enskild databaser har stöd för den här beräkningsstorleken|50|100|100|100|100|100|
|Min/max-databaspool vCore val per databas|Gäller inte|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Kritiska-affärsnivå: Generation 5-beräkningsplattformen (del 2)

|Beräkna storlek|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Minne (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Maximal datastorlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximal storlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Maximalt antal samtidiga arbetare per pool (begäranden) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Maximalt antal databaser per pool|100|100|100|100|100|100|100|
|Min/max-databaspool vCore val per databas|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

\* De maximala samtidiga arbetare (begäranden) för alla enskilda databaser, se [enkel database-resursgränser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen använder Gen5 och dess max vCore per databas är till exempel 2, så är de maximala samtidiga arbetarna är 200.  Om max vCore per databas är 0,5, är de maximala samtidiga arbetarna 50 eftersom det finns högst 100 samtidiga arbetare per vcore på Gen5.  För andra max vCore-inställningarna per databas som är mindre 1 virtuell kärna eller mindre antal max samtidiga arbetare är på samma sätt skala.

Om alla virtuella kärnor för en elastisk pool är upptagen, får varje databas i poolen lika mycket av beräkningsresurser för att bearbeta frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. Elastisk pool resursen rättvis resursdelning är utöver den mängd resurser som annars att garantera att varje databas när den vCore-min per databas har angetts till ett annat värde än noll.

## <a name="database-properties-for-pooled-databases"></a>Databasegenskaperna för databaser i en pool

I följande tabell beskrivs egenskaperna för databaser i en pool.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Maximalt antal virtuella kärnor per databas |Det maximala antalet virtuella kärnor som en databas i poolen kan använda, om tillgängliga beroende på användningen av andra databaser i poolen. Maximalt antal virtuella kärnor per databas är inte resursgaranti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ställ in max virtuella kärnor per databas som är tillräckligt högt för att hantera toppar i databasanvändningen. En viss grad av över transaktionen är att förvänta eftersom poolen Allmänt förutsätter heta och kalla användningsmönster för databaser där alla databaser har aktivitetstoppar samtidigt.|
| Min virtuella kärnor per databas |Det minsta antalet virtuella kärnor som en databas i poolen är garanterad. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Min virtuella kärnor per databas kan anges till 0 och även är standardvärdet. Den här egenskapen anges till var som helst mellan 0 och den genomsnittliga vCores användningen per databas. Produkten av antalet databaser i poolen och min virtuella kärnor per databas får inte överskrida virtuella kärnor per pool.|
| Maximalt lagringsutrymme per databas |Den maximala databasstorleken som anges av användaren för en databas i poolen. Pooldatabaser delar lagringsutrymme för allokerade pool, så den storlek som en databas kan nå är begränsad till det mindre av återstående lagringspoolen lagring och databasstorlek. Maximal databasstorlek avser den maximala storleken för datafilerna och omfattar inte utrymmet som används av loggfiler. |
|||

## <a name="next-steps"></a>Nästa steg

- VCore-resursgränser för en enskild databas, se [resursgränser för enskilda databaser med hjälp av den vCore-baserade inköpsmodellen](sql-database-vcore-resource-limits-single-databases.md)
- DTU-resursgränser för en enskild databas, se [resursgränser för enskilda databaser med hjälp av den DTU-baserade inköpsmodellen](sql-database-dtu-resource-limits-single-databases.md)
- DTU-resursgränser för elastiska pooler, se [resursgränser för elastiska pooler med hjälp av den DTU-baserade inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md)
- Resursgränser för hanterade instanser, se [hanterad instans resursbegränsningar](sql-database-managed-instance-resource-limits.md).
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Läs om hur resursbegränsningar på en databasserver, [översikt över resursgränser på en SQL-databasserver](sql-database-resource-limits-database-server.md) information om begränsningar på de server och prenumeration.
