---
title: Azure SQL Database vCore-baserade gränserna - databas | Microsoft Docs
description: Den här sidan beskrivs några vanliga vCore-baserade gränserna för en enskild databas i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 1a14e1a7c50f458067491a8605a0518056ac0aa8
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309899"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database-preview"></a>Azure SQL Database vCore-baserade köp modellen gränser för en enskild databas (förhandsgranskning)

Den här artikeln innehåller detaljerade resurs gränser för enskilda databaser i Azure SQL Database med vCore-baserade inköpsmodell.

DTU-baserade gränser för köp modellen finns [SQL Database DTU-baserade gränserna för](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Databas: lagringsstorlekar och prestandanivåer

Följande tabeller visar resurserna som är tillgängliga för en enskild databas vid varje servicenivå för tjänstnivå och prestandanivå för enskilda databaser. Du kan ange den tjänstnivå och prestandanivå lagringsutrymme för en enskild databas med hjälp av den [Azure-portalen](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), [Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), eller [REST API](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="general-purpose-service-tier"></a>Tjänstnivån för allmänna ändamål

#### <a name="generation-4-compute-platform"></a>Generation 4 beräkningsplattform
|Prestandanivå|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maxstorlek för data (GB)|1024|1024|1536|3072|4096|4096|
|Maxstorlek|307|307|461|922|1229|1229|
|TempDB size(DB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|3200|4800|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|000
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maxstorlek för data (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Maxstorlek|307|307|461|614|1229|1229|1229|1229|
|TempDB size(DB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|2400|3200|4000|8000|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|1|1|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

### <a name="business-critical-service-tier"></a>Kritisk tjänst affärsnivå

#### <a name="generation-4-compute-platform"></a>Generation 4 beräkningsplattform
|Prestandanivå|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|1|2|4|8|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maxstorlek för data (GB)|1024|1024|1024|1024|1024|1024|
|Maxstorlek|307|307|307|307|307|307|
|TempDB size(DB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|I/o-svarstid (CA)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|3200|4800|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Ja|Ja|Ja|Ja|Ja|Ja|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (CA)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|1 – 2 ms (skriva)<br>1 – 2 ms (läsa)|
|Maxstorlek för data (GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|Maxstorlek|307|307|307|307|614|1229|1229|1229|
|TempDB size(DB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Maximalt antal samtidiga arbetare (antal begäranden)|200|400|800|1600|2400|3200|4000|8000|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|3|3|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

## <a name="next-steps"></a>Nästa steg

- Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar.
- Information om allmänna Azure gränser finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
