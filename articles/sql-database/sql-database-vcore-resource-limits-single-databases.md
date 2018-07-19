---
title: Azure SQL Database vCore-baserade resursbegränsningar - databas | Microsoft Docs
description: Den här sidan beskriver några vanliga vCore-baserade resursbegränsningar för en enskild databas i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: carlrab
ms.openlocfilehash: ac9070e328ef867a0b0e8d7d81f5147a50357928
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126416"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Azure SQL Database vCore-baserade köpa modellen gränser för en enskild databas

Den här artikeln innehåller detaljerade resursgränser för enskilda databaser i Azure SQL Database med hjälp av den vCore-baserade inköpsmodellen.

DTU-baserade gränser för inköpschef modellen för finns i [SQL Database DTU-baserade resursbegränsningar](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Databas: lagringsstorlekar och prestandanivåer

Följande tabeller visar resurserna som är tillgängliga för en enskild databas på varje servicenivå för tjänstnivå och prestandanivå för enskilda databaser. Du kan ange den tjänstnivån, prestandanivån och lagringsutrymmet för en enkel databas med hjälp av den [Azure-portalen](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), [Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), eller [REST-API](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="general-purpose-service-tier"></a>Tjänstnivå för allmänt syfte

#### <a name="generation-4-compute-platform"></a>Beräkningsplattform generation 4
|Prestandanivå|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Maximal datastorlek (GB)|1024|1024|1536|3072|4096|4096|
|Maximal storlek|307|307|461|922|1229|1229|
|TempDB size(GB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|800|1600|3200|4800|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|000
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|
|||

#### <a name="generation-5-compute-platform"></a>Beräkningsplattform generation 5
|Prestandanivå|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (ungefärlig)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|5 – 7 ms (skriva)<br>5 – 10 ms (läsa)|
|Maximal datastorlek (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maximal storlek|307|307|461|614|1229|1229|1229|1229|
|TempDB size(GB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|800|1600|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|
|||

### <a name="business-critical-service-tier"></a>Kritiska-affärsnivå

#### <a name="generation-4-compute-platform"></a>Beräkningsplattform generation 4
|Prestandanivå|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|1|2|4|8|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Maximal storlek|307|307|307|307|307|307|
|TempDB size(GB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maximalt antal samtidiga arbetare (begäranden)|200|400|800|1600|3200|4800|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|3|3|3|3|3|3|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|
|||

#### <a name="generation-5-compute-platform"></a>Beräkningsplattform generation 5
|Prestandanivå|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Stöd för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|InMemory-OLTP-lagring (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maximal datastorlek (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maximal storlek|307|307|307|307|614|1229|1229|1229|
|TempDB size(GB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Maximalt antal samtidiga arbetare (begäranden)|200|400|800|1600|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|3|3|3|3|3|3|3|3|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lässkalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad lagring av säkerhetskopior|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|1 X-databasstorlek|
|||

## <a name="next-steps"></a>Nästa steg

- Se [SQL Database vanliga frågor och svar](sql-database-faq.md) svar på vanliga frågor och svar.
- Information om allmänna Azure-begränsningar finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
