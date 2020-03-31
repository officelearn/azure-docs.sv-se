---
title: vCore resursgränser - elastiska pooler
description: På den här sidan beskrivs några vanliga vCore-resursgränser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 03/03/2020
ms.openlocfilehash: b3c5594b8eef76dcb57903408dd1e77c96890eab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346274"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Resursgränser för elastiska pooler med hjälp av vCore-inköpsmodellen

Den här artikeln innehåller detaljerade resursgränser för elastiska Azure SQL Database-pooler och poolade databaser med hjälp av vCore-inköpsmodellen.

Information om DTU-inköpsmodellgränser finns i [SQL Database DTU-resursgränser - elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

Du kan ange tjänstnivå, beräkningsstorlek och lagringsbelopp med [Azure-portalen,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)eller [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> För skalning av vägledning och överväganden finns i [Skala en elastisk pool](sql-database-elastic-pool-scale.md)

## <a name="general-purpose---provisioned-compute---gen4"></a>Allmänt ändamål - etablerad beräkning - Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna i östra eller södra Brasilien.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Servicenivå för allmänt ändamål: Beräkningsplattform för Generation 4 (del 1)

|Beräkna storlek|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Max antal DBs per pool <sup>1</sup>|100|200|500|500|500|500|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|512|756|1536|1536|1536|2048|
|Max loggstorlek|154|227|461|461|461|614|
|TempDB max datastorlek (GB)|32|64|96|128|160|192|
|Lagringstyp|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS per pool <sup>2</sup> |400|800|1200|1600|2000|2400|
|Max logghastighet per pool (MBps)|4.7|9.4|14.1|18.8|23.4|28.1|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup> |210|420|630|840|1050|1260|
|Max samtidiga inloggningar per pool <sup>3</sup> |210|420|630|840|1050|1260|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Servicenivå för allmänt ändamål: Beräkningsplattform för Generation 4 (del 2)

|Beräkna storlek|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159.5|
|Max antal DBs per pool <sup>1</sup>|500|500|500|500|500|500|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|2048|2048|2048|2048|3584|4096|
|Max loggstorlek (GB)|614|614|614|614|1075|1229|
|TempDB max datastorlek (GB)|224|256|288|320|512|768|
|Lagringstyp|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Max logghastighet per pool (MBps)|32.8|37.5|37.5|37.5|37.5|37.5|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Max samtidiga inloggningar pool (förfrågningar) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).    

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

## <a name="general-purpose---provisioned-compute---gen5"></a>Allmänt ändamål - etablerad beräkning - Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Servicenivå för allmänt ändamål: Beräkningsplattform för Generation 5 (del 1)

|Beräkna storlek|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|2|4|6|8|10|12|14|
|Minne (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Max antal DBs per pool <sup>1</sup>|100|200|500|500|500|500|500|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|512|756|1536|1536|1536|2048|2048|
|Max loggstorlek (GB)|154|227|461|461|461|614|614|
|TempDB max datastorlek (GB)|64|128|192|256|320|384|448|
|Lagringstyp|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Max logghastighet per pool (MBps)|9.4|18.8|28.1|37.5|37.5|37.5|37.5|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Antal repliker|1|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Servicenivå för allmänt ändamål: Beräkningsplattform för Generation 5 (del 2)

|Beräkna storlek|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|16|18|20|24|32|40|80|
|Minne (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Max antal DBs per pool <sup>1</sup>|500|500|500|500|500|500|500|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|2048|3072|3072|3072|4096|4096|4096|
|Max loggstorlek (GB)|614|922|922|922|1229|1229|1229|
|TempDB max datastorlek (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|Premium (fjärr) lagring|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS per pool <sup>2</sup> |6,400|7,200|8,000|9 600|12,800|16 000|32,000|
|Max logghastighet per pool (MBps)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Antal repliker|1|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Allmänt ändamål - etablerad beräkning - Fsv2-serien

### <a name="fsv2-series-compute-generation-preview"></a>Beräkningsgenerering i Fsv2-serien (förhandsversion)

|Beräkna storlek|GP_Fsv2_72|
|:--- | --: |
|Beräkna generering|Fsv2-serien|
|vCores (virtuella)|72|
|Minne (GB)|136.2|
|Max antal DBs per pool <sup>1</sup>|500|
|Support för Columnstore|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|
|Max datastorlek (GB)|4096|
|Max loggstorlek (GB)|1024|
|TempDB max datastorlek (GB)|333|
|Lagringstyp|Premium (fjärr) lagring|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|16 000|
|Max logghastighet per pool (MBps)|37.5|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|3780|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|3780|
|Maximalt antal samtidiga sessioner|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0-72|
|Antal repliker|1|
|Flera AZ|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

## <a name="business-critical---provisioned-compute---gen4"></a>Affärskritisk - etablerad beräkning - Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna i östra eller södra Brasilien.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Affärskritisk tjänstnivå: Generation 4-beräkningsplattform (del 1)

|Beräkna storlek|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|2|3|4|5|6|
|Minne (GB)|14|21|28|35|42|
|Max antal DBs per pool <sup>1</sup>|50|100|100|100|100|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Max datastorlek (GB)|1024|1024|1024|1024|1024|
|Max loggstorlek (GB)|307|307|307|307|307|
|TempDB max datastorlek (GB)|64|96|128|160|192|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|9,000|13,500|18 000|22,500|27,000|
|Max logghastighet per pool (MBps)|20|30|40|50|60|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|420|630|840|1050|1260|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|420|630|840|1050|1260|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Antal repliker|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Affärskritisk tjänstnivå: Generation 4-beräkningsplattform (del 2)

|Beräkna storlek|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159.5|
|Max antal DBs per pool <sup>1</sup>|100|100|100|100|100|100|
|Support för Columnstore|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|OLTP-lagring i minnet (GB)|7|8|9.5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Max datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Max loggstorlek (GB)|307|307|307|307|307|307|
|TempDB max datastorlek (GB)|224|256|288|320|512|768|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|31,500|36,000|40,500|45 000|72,000|96,000|
|Max logghastighet per pool (MBps)|70|80|80|80|80|80|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Antal repliker|4|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

## <a name="business-critical---provisioned-compute---gen5"></a>Affärskritisk - etablerad beräkning - Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Affärskritisk tjänstnivå: Beräkningsplattform för Generation 5 (del 1)

|Beräkna storlek|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|4|6|8|10|12|14|
|Minne (GB)|20.8|31.1|41.5|51.9|62.3|72.7|
|Max antal DBs per pool <sup>1</sup>|50|100|100|100|100|100|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|3.14|4.71|6.28|8.65|11.02|13.39|
|Max datastorlek (GB)|1024|1536|1536|1536|3072|3072|
|Max loggstorlek (GB)|307|307|461|461|922|922|
|TempDB max datastorlek (GB)|128|192|256|320|384|448|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|18 000|27,000|36,000|45 000|54,000|63,000|
|Max logghastighet per pool (MBps)|60|90|120|120|120|120|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|420|630|840|1050|1260|1470|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Antal repliker|4|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Affärskritisk tjänstnivå: Beräkningsplattform för Generation 5 (del 2)

|Beräkna storlek|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|16|18|20|24|32|40|80|
|Minne (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Max antal DBs per pool <sup>1</sup>|100|100|100|100|100|100|100|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.68|
|Max datastorlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max loggstorlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB max datastorlek (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|72,000|81,000|90 000|108,000|144,000|180,000|256,000|
|Max logghastighet per pool (MBps)|120|120|120|120|120|120|120|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Antal repliker|4|4|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

## <a name="business-critical---provisioned-compute---m-series"></a>Affärskritisk - etablerad beräkning - M-serien

### <a name="m-series-compute-generation-preview"></a>Beräkningsgenerering i M-serien (förhandsversion)

|Beräkna storlek|BC_M_128|
|:--- | --: |
|Beräkna generering|M-serien|
|vCores (virtuella)|128|
|Minne (GB)|3767.1|
|Max antal DBs per pool <sup>1</sup>|100|
|Support för Columnstore|Ja|
|OLTP-lagring i minnet (GB)|1768|
|Max datastorlek (GB)|4096|
|Max loggstorlek (GB)|2048|
|TempDB max datastorlek (GB)|4096|
|Lagringstyp|Lokal SSD|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS per pool <sup>2</sup>|200 000|
|Max logghastighet per pool (MBps)|333|
|Max samtidiga arbetare per pool (begäranden) <sup>3</sup>|13,440|
|Max samtidiga inloggningar per pool (begäranden) <sup>3</sup>|13,440|
|Maximalt antal samtidiga sessioner|30,000|
|Min/max elastiska pool vCore-alternativ per databas|0-128|
|Antal repliker|4|
|Flera AZ|Ja|
|Lässkalning|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|

<sup>1</sup> Se [Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> Det maximala värdet för IO-storlekar mellan 8 kB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

<sup>3</sup> För max samtidiga arbetare (begäranden) för en enskild databas, se Begränsningar för [en databasresurs](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen till exempel använder Gen5 och max vCore per databas är inställd på 2, är värdet för max samtidiga arbetare 200.  Om max vCore per databas är inställt på 0,5 är värdet för max samtidiga arbetare 50, eftersom det på Gen5 finns högst 100 samtidiga arbetare per vCore. För andra max vCore-inställningar per databas som är mindre 1 vCore eller mindre skalas antalet samtidiga arbetare på samma sätt.

Om alla virtuella kärnor i en elastisk pool är upptagna får varje databas i poolen lika många beräkningsresurser för att bearbeta frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. Rättvisa för elastisk poolresursdelning är ett tillägg till alla resurser som annars garanteras varje databas när vCore min per databas är inställt på ett värde som inte är noll.

## <a name="database-properties-for-pooled-databases"></a>Databasegenskaper för poolade databaser

I följande tabell beskrivs egenskaperna för poolade databaser.

> [!NOTE]
> Resursgränserna för enskilda databaser i elastiska pooler är i allmänhet desamma som för enskilda databaser utanför pooler som har samma beräkningsstorlek. Till exempel är max samtidiga arbetare för en GP_Gen4_1 databas 200 arbetare. Så, max samtidiga arbetstagare för en databas i en GP_Gen4_1 pool är också 200 arbetstagare. Observera att det totala antalet samtidiga arbetare i GP_Gen4_1 poolen är 210.

| Egenskap | Beskrivning |
|:--- |:--- |
| Max vCore per databas |Det maximala antalet virtuella kärnor som en databas i poolen kan använda, om tillgängligt baserat på användning av andra databaser i poolen. Max vCores per databas är inte en resursgaranti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange max vCores per databas tillräckligt högt för att hantera toppar i databasanvändning. En viss grad av över-begåvningar förväntas eftersom poolen i allmänhet förutsätter varma och kalla användningsmönster för databaser där alla databaser inte samtidigt topp.|
| Min vCores per databas |Det minsta antalet virtuella kärnor som alla databaser i poolen garanteras. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Min vCores per databas kan ställas in på 0 och är också standardvärdet. Den här egenskapen är inställd på någonstans mellan 0 och den genomsnittliga vCores-användningen per databas. Produkten av antalet databaser i poolen och min vCores per databas får inte överstiga vCore per pool.|
| Max lagringsutrymme per databas |Den maximala databasstorlek som användaren har angett för en databas i en pool. Poolade databaser delar allokerat poollagring, så storleken som en databas kan nå är begränsad till den mindre av återstående poollagring och databasstorlek.  Maximal databasstorlek avser den maximala storleken för datafilerna och omfattar inte utrymmet som används av loggfiler. |
|||

## <a name="next-steps"></a>Nästa steg

- För vCore-resursgränser för en enskild databas finns i [resursgränser för enskilda databaser med hjälp av vCore-inköpsmodellen](sql-database-vcore-resource-limits-single-databases.md)
- För DTU-resursgränser för en enskild databas finns i [resursgränser för enskilda databaser med hjälp av DTU-inköpsmodellen](sql-database-dtu-resource-limits-single-databases.md)
- För DTU-resursgränser för elastiska pooler finns i [resursgränser för elastiska pooler med DTU-inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md)
- För resursgränser för hanterade instanser finns i [resursbegränsningar för hanterade instanser](sql-database-managed-instance-resource-limits.md).
- Information om allmänna Azure-gränser finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resursbegränsningar på en databasserver finns i [översikt över resursbegränsningar på en SQL Database-server](sql-database-resource-limits-database-server.md) för information om begränsningar på server- och prenumerationsnivå.
