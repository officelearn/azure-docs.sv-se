---
title: Azure SQL Database vCore-baserade resursbegränsningar - databas | Microsoft Docs
description: Den här sidan beskriver några vanliga vCore-baserade resursbegränsningar för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/09/2019
ms.openlocfilehash: 894922a80ab874e5304ef441571e03ef559a34b0
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215430"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Azure SQL Database vCore-baserade köpa modellen gränser för en enskild databas

Den här artikeln innehåller detaljerade resursgränser för enskilda databaser i Azure SQL Database med hjälp av den vCore-baserade inköpsmodellen.

DTU-baserade inköpschef modellen gränser för enskilda databaser på en logisk server, se [översikt över resource begränsar på en logisk server](sql-database-resource-limits-logical-server.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

Du kan ange tjänstnivå, beräkningsstorleken och lagringsutrymmet för en enkel databas med hjälp av den [Azure-portalen](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), [ Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases), eller [REST-API](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Tjänstnivå för allmänna ändamål: Lagringsstorlekar och storlekar

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Tjänstnivå för allmänna ändamål: Generation 4-beräkningsplattformen (del 1)

|Beräkna storlek|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|1024|1024|1024|1536|1536|1536|
|Maximal storlek (GB)|307|307|307|461|461|461|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Mål-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|600|800|1000|1200|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|000
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Tjänstnivå för allmänna ändamål: Generation 4-beräkningsplattformen (del 2)

|Beräkna storlek|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|168|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|1536|3072|3072|3072|4096|4096|
|Maximal storlek (GB)|461|922|922|922|1229|1229|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)
|Mål-IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Maximalt antal samtidiga arbetare (begäranden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Tjänstnivå för allmänna ändamål: Generation 5-beräkningsplattformen (del 1)

|Beräkna storlek|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Minne (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|1024|1024|1024|1536|1536|1536|1536|
|Maximal storlek (GB)|307|307|307|461|461|461|461|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Mål-IOPS (64 KB)|500|1000|1500|2000|2500|3000|3500|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|600|800|1000|1200|1400|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Tjänstnivå för allmänna ändamål: Generation 5-beräkningsplattformen (del 2)

|Beräkna storlek|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Minne (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximal storlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Mål-IOPS (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Maximalt antal samtidiga arbetare (begäranden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Kritiska-affärsnivå: Lagringsstorlekar och storlekar

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Kritiska-affärsnivå: Generation 4-beräkningsplattformen (del 1)

|Beräkna storlek|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Stöd för Columnstore|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|InMemory-OLTP-lagring (GB)|1|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Maximal storlek (GB)|307|307|307|307|307|307|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|600|800|1000|1200|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Kritiska-affärsnivå: Generation 4-beräkningsplattformen (del 2)

|Beräkna storlek|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|168|
|Stöd för Columnstore|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|InMemory-OLTP-lagring (GB)|7|8|9.5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Maximal storlek (GB)|307|307|307|307|307|307|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Maximalt antal samtidiga arbetare (begäranden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Kritiska-affärsnivå: Generation 5-beräkningsplattformen (del 1)

|Beräkna storlek|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Minne (GB)|11|22|33|44|55|66|77|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|1.571|3,142|4.713|6.284|8.655|11.026|13.397|
|Maximal datastorlek (GB)|1024|1024|1024|1536|1536|1536|1536|
|Maximal storlek (GB)|307|307|307|461|461|461|461|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|35000|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|600|800|1000|1200|1400|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Kritiska-affärsnivå: Generation 5-beräkningsplattformen (del 2)

|Beräkna storlek|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Minne (GB)|88|99|110|132|176|220|440|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Maximal datastorlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximal storlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Mål-IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Maximalt antal samtidiga arbetare (begäranden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|

## <a name="hyperscale-service-tier-preview"></a>Hyperskala tjänstnivå (förhandsversion)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Generation 4-beräkningsplattformen: Lagringsstorlekar och storlekar

|Prestandanivå|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (TB)|100 |100 |100 |100 |100 |100 |
|Maximal storlek (TB)|1 |1 |1 |1 |1 |1 |
|TempDB-storlek (GB)|32|64|128|256|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Mål-IOPS (64 KB)|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|
|I/o-svarstid (ungefärlig)|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|800|1600|3200|4800|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|2|2|2|2|2|2|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|7|7|7|7|7|7|
|||

### <a name="generation-5-compute-platform"></a>Beräkningsplattform generation 5

|Prestandanivå|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|10.2|20.4|40.8|81.6|122.4|163.2|204|408|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal datastorlek (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maximal storlek (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|TempDB-storlek (GB)|64|128|256|384|384|384|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Mål-IOPS (64 KB)|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|
|I/o-svarstid (ungefärlig)|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|Fastställas|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|800|1600|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|2|2|2|2|2|2|2|2|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior (förhandsversion gräns)|7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Nästa steg

- Se [SQL Database vanliga frågor och svar](sql-database-faq.md) svar på vanliga frågor och svar.
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
