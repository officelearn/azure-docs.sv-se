---
title: Azure SQL Database vCore-baserade gränserna - elastiska pooler | Microsoft Docs
description: Den här sidan beskrivs några vanliga vCore-baserade gränserna för elastiska pooler i Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 01f213c7cf5f6be3ef84601a50bb4455422faf22
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309920"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools-preview"></a>Azure SQL Database vCore-baserade köp modellen gränser för elastiska pooler (förhandsgranskning)

Den här artikeln innehåller de detaljerade gränserna för elastiska pooler i Azure SQL Database och grupperade databaser med hjälp av vCore-baserade inköpsmodell.

DTU-baserade gränser för köp modellen finns [SQL Database DTU-baserade gränserna - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).


## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastisk pool: lagringsstorlekar och prestandanivåer

Följande tabeller visar de tillgängliga resurserna på varje servicenivå för tjänstnivå och prestandanivå för elastiska pooler SQL-databas. Du kan ange tjänstnivån, prestanda och storage belopp med den [Azure-portalen](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), eller [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Resurs-gränserna för enskilda databaser i elastiska pooler är vanligtvis samma som för enskilda databaser utanför pooler som har samma prestanda. Högsta antal samtidiga arbetare för en GP_Gen4_1 databas är till exempel 200 anställda. Högsta antal samtidiga arbetare för en databas i en pool med GP_Gen4_1 är därför också 200 anställda. Observera att det totala antalet samtidiga arbetare i GP_Gen4_1 poolen är 210.

### <a name="general-purpose-service-tier"></a>Tjänstnivån för allmänna ändamål

#### <a name="generation-4-compute-platform"></a>Generation 4 beräkningsplattform
|Prestandanivå|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Maskinvaran generation|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Minne (GB)|7|14|28|56|112|168|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|Maxstorlek för data (GB)|512|756|1536|2048|3584|4096|
|Maxstorlek|154|227|461|614|1075|1229|
|TempDB size(DB)|32|64|128|256|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|3360|5040|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|100|200|500|500|500|500|
|Minsta/högsta elastisk pool Klicka-slutar|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Maskinvaran generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|11|22|44|88|132|176|220|440|
|Columnstore-support|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnesintern OLTP lagringsutrymme (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Lagringstyp|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|Premium (Fjärrlagring)|
|Maxstorlek för data (GB)|512|756|1536|2048|3072|4096|4096|4096|
|Maxstorlek|154|227|461|614|922|1229|1229|1229|
|TempDB size(DB)|64|128|256|384|384|384|384|384|
|Mål-IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|I/o-svarstid (CA)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|5-7 ms (skriva)<br>5-10 ms (läsa)|
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|2520|3360|4200|8400
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|100|200|500|500|500|500|500|500|
|Minsta/högsta elastisk pool Klicka-slutar|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
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
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|3360|5040|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|Gäller inte|50|100|100|100|100|
|Minsta/högsta elastisk pool Klicka-slutar|Gäller inte|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Antal repliker|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Ja|Ja|Ja|Ja|Ja|Ja|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

#### <a name="generation-5-compute-platform"></a>Generation 5 beräkningsplattform
|Prestandanivå|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
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
|Maximalt antal samtidiga arbetare (antal begäranden)|210|420|840|1680|2520|3360|5040|8400|
|Högsta tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Max poolen densitet|Gäller inte|50|100|100|100|100|100|100|
|Minsta/högsta elastisk pool Klicka-slutar|Gäller inte|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Antal repliker|3|3|3|3|3|3|3|3|
|Flera AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läsa skalbar|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Ingår lagring för säkerhetskopiering|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|1 X-DB storlek|
|||

Om alla vCores för en elastisk pool är upptagna, får varje databas i poolen lika mycket beräkningsresurser för bearbetning av frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. Elastisk pool resursdelning skälighet är förutom valfritt antal resurs annars garanterat varje databas när vCore minsta per databas har angetts till ett värde som inte är noll.

### <a name="database-properties-for-pooled-databases"></a>Databasegenskaper för grupperade databaser

I följande tabell beskrivs egenskaperna för pool-databaser.

| Egenskap  | Beskrivning |
|:--- |:--- |
| Max vCores per databas |Det maximala antalet vCores som alla databaser i poolen kan använda om tillgängliga baserat på användning av andra databaser i poolen. Max vCores per databas är inte en resurs garanti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange högsta vCores per databas som är tillräckligt högt för att hantera toppar i databasen användning. En viss grad av övertilldelning är att förvänta eftersom poolen normalt arbetar efter ett mönster med frekvent och lågfrekvent användning av databaser där inte alla databaser har aktivitetstoppar samtidigt.|
| Min vCores per databas |Det minsta antalet vCores som garanterat alla databaser i poolen. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Min vCores per databas kan anges till 0 och även är standardvärdet. Den här egenskapen anges till var som helst mellan 0 och genomsnittlig vCores användning per databas. Produkten av antalet databaser i poolen och min vCores per databas får inte överskrida vCores per pool.|
| Maximalt lagringsutrymme per databas |Den maximala databasstorleken anges av användaren för en databas i en pool. Grupperade databaser dela allokerade poolen lagring, så att en databas kan nå storlek är begränsad till mindre återstående poolens lagringsutrymme och databasstorlek. Max databasens storlek avser den maximala storleken för datafiler och innehåller inte det utrymme som används av loggfiler. |
|||
 
## <a name="next-steps"></a>Nästa steg

- Se [SQL-databasen med vanliga frågor om](sql-database-faq.md) för svar på vanliga frågor och svar.
- Information om allmänna Azure gränser finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
