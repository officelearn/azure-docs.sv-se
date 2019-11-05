---
title: Azure SQL Database resurs gränser för vCore-elastiska pooler | Microsoft Docs
description: På den här sidan beskrivs några vanliga vCore resurs gränser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 11/04/2019
ms.openlocfilehash: 65c2bfe4d79f5b7d468999143524b96b60f0efaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495990"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Resurs begränsningar för elastiska pooler med vCore inköps modell

Den här artikeln innehåller detaljerade resurs gränser för Azure SQL Database elastiska pooler och databaser i pooler med hjälp av vCore inköps modell.

För inköps modell gränser för DTU, se [SQL Database gräns för DTU-resurs-elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](sql-database-file-space-management.md).

Du kan ange tjänst nivå, beräknings storlek och lagrings mängd med hjälp av [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [POWERSHELL](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)eller [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> För skalnings vägledning och överväganden, se [skala en elastisk pool](sql-database-elastic-pool-scale.md)


## <a name="general-purpose---provisioned-compute---gen4"></a>Generell användning-allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Tjänst nivå för generell användning: generation 4-beräknings plattform (del 1)

|Beräknings storlek|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Maximalt antal databaser per pool|100|200|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|512|756|756|1536|1536|1536|
|Maximal logg storlek|154|227|227|461|461|461|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Logg hastighets gränser (Mbit/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|
|Maximalt antal samtidiga arbetare per pool (begär Anden) * |210|420|630|840|1050|1260|
|Maximalt antal samtidiga inloggningar per pool * |210|420|630|840|1050|1260|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Läs skalbarhet|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.


### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Tjänst nivå för generell användning: generation 4 beräknings plattform (del 2)

|Beräknings storlek|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|168|
|Maximalt antal databaser per pool|500|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|1536|2048|2048|2048|3584|4096|
|Största logg storlek (GB)|461|614|614|614|1075|1229|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Logg hastighets gränser (Mbit/s)|32,8125|37,5|37,5|37,5|37,5|37,5|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|1470|1680|1890|2 100|3360|5040|
|Maximal pool för samtidiga inloggningar (begär Anden) *|1470|1680|1890|2 100|3360|5040|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Läs skalbarhet|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.


## <a name="general-purpose---provisioned-compute---gen5"></a>Generell användning-allokerad beräkning – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Tjänst nivå för generell användning: generation 5 beräknings plattform (del 1)

|Beräknings storlek|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Maximalt antal databaser per pool|100|200|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|512|756|756|1536|1536|1536|
|Största logg storlek (GB)|154|227|227|461|461|461|461|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Logg hastighets gränser (Mbit/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|32,8125|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|210|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|210|420|630|840|1050|1260|1470|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Läs skalbarhet|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Tjänst nivå för generell användning: generation 5 beräknings plattform (del 2)

|Beräknings storlek|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maximalt antal databaser per pool|500|500|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|2048|2048|3072|3072|4096|4096|4096|
|Största logg storlek (GB)|614|614|922|922|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Logg hastighets gränser (Mbit/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|1680|1890|2 100|2520|3360|4200|8400|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Läs skalbarhet|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Allmänt syfte – etablerad beräkning – Fsv2-serien

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2-seriens beräknings generation (för hands version)

|Beräknings storlek|GP_Fsv2_72|
|:--- | --: |
|Beräknings generation|Fsv2-serien|
|Virtuella kärnor|72|
|Minne (GB)|136|
|Maximalt antal databaser per pool|500|
|Columnstore-stöd|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|
|Maximal data storlek (GB)|4096|
|Största logg storlek (GB)|1024|
|Maximal data storlek för TempDB (GB)|333|
|Lagringstyp|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|36000|
|Logg hastighets gränser (Mbit/s)|37,5|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|1680|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|1680|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0-72|
|Antal repliker|1|
|Multi-AZ|Gäller inte|
|Läs skalbarhet|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|




\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.


## <a name="business-critical---provisioned-compute---gen4"></a>Verksamhets kritiskt allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Verksamhets kritisk tjänst nivå: generation 4 beräknings plattform (del 1)

|Beräknings storlek|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Maximalt antal databaser per pool|Endast enskilda databaser stöds för den här beräknings storleken|50|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|650|650|650|650|650|650|
|Största logg storlek (GB)|195|195|195|195|195|195|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Logg hastighets gränser (Mbit/s)|10|20|30|40|50|60|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|210|420|630|840|1050|1260|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|210|420|630|840|1050|1260|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|Gäller inte|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Verksamhets kritisk tjänst nivå: generation 4 beräknings plattform (del 2)

|Beräknings storlek|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|81,6|91,8|102|122,4|163,2|204|
|Maximalt antal databaser per pool|100|100|100|100|100|100|
|Columnstore-stöd|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Minnes intern OLTP-lagring (GB)|7|8|9,5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|650|650|650|650|1024|1024|
|Största logg storlek (GB)|195|195|195|195|307|307|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Logg hastighets gränser (Mbit/s)|70|80|80|80|80|80|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|1470|1680|1890|2 100|3360|5040|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|1470|1680|1890|2 100|3360|5040|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

## <a name="business-critical---provisioned-compute---gen5"></a>Verksamhets kritiskt allokerad beräkning – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Verksamhets kritisk tjänst nivå: generation 5 beräknings plattform (del 1)

|Beräknings storlek|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Maximalt antal databaser per pool|Endast enskilda databaser stöds för den här beräknings storleken|50|100|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|307|461|461|922|922|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|35000|
|Logg hastighets gränser (Mbit/s)|15|30|45|60|75|90|105|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|210|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|210|420|630|840|1050|1260|1470|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|Gäller inte|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Verksamhets kritisk tjänst nivå: generation 5 beräknings plattform (del 2)

|Beräknings storlek|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maximalt antal databaser per pool|100|100|100|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Logg hastighets gränser (Mbit/s)|120|120|120|120|120|120|120|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

## <a name="business-critical---provisioned-compute---m-series"></a>Verksamhets kritiskt allokerad beräkning – M-serien

### <a name="m-series-compute-generation-preview"></a>Beräknings generation i M-serien (för hands version)

|Beräknings storlek|GP_M_128|
|:--- | --: |
|Beräknings generation|M-serien|
|Virtuella kärnor|128|
|Minne (GB)|3767|
|Maximalt antal databaser per pool|100|
|Columnstore-stöd|Ja|
|Minnes intern OLTP-lagring (GB)|481|
|Maximal data storlek (GB)|4096|
|Största logg storlek (GB)|2048|
|Maximal data storlek för TempDB (GB)|4096|
|Lagringstyp|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|40000|
|Logg hastighets gränser (Mbit/s)|120|
|Maximalt antal samtidiga arbetare per pool (begär Anden) *|1680|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) *|1680|
|Maximalt antal tillåtna sessioner|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0-128|
|Antal repliker|4|
|Multi-AZ|Ja|
|Läs skalbarhet|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|



\* för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](sql-database-vcore-resource-limits-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och dess maximala vCore per databas är 2, är det högsta antalet samtidiga arbetare 200.  Om max vCore per databas är 0,5, är Max samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vcore.  För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

Om alla virtuella kärnor av en elastisk pool är upptagna får varje databas i poolen en lika stor mängd data bearbetnings resurser för att bearbeta frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. Resurs delnings skälighet för elastisk pool är utöver den mängd resurser som på annat sätt garanteras för varje databas när vCore min per databas är inställt på ett värde som inte är noll.

## <a name="database-properties-for-pooled-databases"></a>Databas egenskaper för databaser i pooler

I följande tabell beskrivs egenskaperna för databaser i pooler.

> [!NOTE]
> Resurs gränserna för enskilda databaser i elastiska pooler är vanligt vis samma som för enskilda databaser utanför pooler som har samma beräknings storlek. Till exempel är det högsta antalet samtidiga arbetare för en GP_Gen4_1-databas 200 arbetare. Därför är det högsta antalet samtidiga arbetare för en databas i en GP_Gen4_1-pool också 200 arbetare. Observera att det totala antalet samtidiga arbetare i GP_Gen4_1-poolen är 210.

| Egenskap | Beskrivning |
|:--- |:--- |
| Max virtuella kärnor per databas |Det maximala antalet virtuella kärnor som en databas i poolen kan använda, om den är tillgänglig baserat på användningen av andra databaser i poolen. Max virtuella kärnor per databas är inte en resurs garanti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange Max virtuella kärnor per databas tillräckligt högt för att hantera toppar i databas användningen. En viss grad av överbelastning förväntas eftersom poolen vanligt vis förutsätter frekventa och kalla användnings mönster för databaser där alla databaser inte samtidigt överskrider varandra.|
| Minsta virtuella kärnor per databas |Det minsta antalet virtuella kärnor som alla databaser i poolen garanterar. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Den minsta virtuella kärnor per databas kan anges till 0 och är också standardvärdet. Den här egenskapen anges till var som helst mellan 0 och den genomsnittliga virtuella kärnor-användningen per databas. Produkten av antalet databaser i poolen och det minsta virtuella kärnor per databas får inte överskrida virtuella kärnor per pool.|
| Maximalt lagrings utrymme per databas |Maximal databas storlek som har angetts av användaren för en databas i en pool. Databaser i pooler delar allokerat lagrings utrymme, så den storlek som en databas kan uppnå är begränsad till den mindre av de återstående lagrings utrymmena för poolen och databas storleken. Maximal databas storlek avser den maximala storleken för datafilerna och inkluderar inte utrymmet som används av loggfiler. |
|||

## <a name="next-steps"></a>Nästa steg

- För vCore resurs gränser för en enskild databas, se [resurs gränser för enskilda databaser med vCore inköps modell](sql-database-vcore-resource-limits-single-databases.md)
- För resurs gränser för DTU för en enskild databas, se [resurs gränser för enskilda databaser med inköps modellen DTU](sql-database-dtu-resource-limits-single-databases.md)
- För resurs gränser för DTU för elastiska pooler, se [resurs gränser för elastiska pooler med inköps modellen DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Resurs begränsningar för hanterade instanser finns i [resurs gränser för hanterade](sql-database-managed-instance-resource-limits.md)instanser.
- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om resurs gränser på en databas server finns i [Översikt över resurs gränser på en SQL Database-Server](sql-database-resource-limits-database-server.md) för information om begränsningar på Server-och prenumerations nivåer.
