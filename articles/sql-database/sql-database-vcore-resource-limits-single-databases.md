---
title: vCore resurs gränser – enskild databas
description: På den här sidan beskrivs några vanliga vCore resurs gränser för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/11/2019
ms.openlocfilehash: 4455181ddf69613ba07bcaeedb26273a4bb5a74d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647857"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Resurs gränser för enskilda databaser med vCore inköps modell

Den här artikeln innehåller detaljerade resurs gränser för Azure SQL Database enskilda databaser med hjälp av vCore inköps modell.

För inköps modell gränser för DTU för enskilda databaser på en SQL Database-Server, se [Översikt över resurs gränser på en SQL Database Server](sql-database-resource-limits-database-server.md).

Du kan ange tjänst nivå, beräknings storlek och lagrings belopp för en enskild databas med hjälp av [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)eller [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Information om skalning och överväganden finns i [skala en enskild databas](sql-database-single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Generell användning – Server lös beräkning – Gen5

[Server lös beräknings nivån](sql-database-serverless.md) är för närvarande endast tillgänglig på Gen5 maskin vara.

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Beräknings storlek|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-max virtuella kärnor|0.5-1|0.5-2|0,5-4|0,75-6|1.0-8|
|Min-max minne (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00 – 24|
|Minsta fördröjning för automatisk paus (minuter)|60|60|60|60|60|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|512|1024|1024|1024|1536|
|Största logg storlek (GB)|154|307|307|307|461|
|Maximal data storlek för TempDB (GB)|32|64|128|192|256|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|320|640|1280|1920|2560|
|Högsta logg frekvens (Mbit/s)|3.8|7.5|15|22.5|30|
|Maximalt antal samtidiga arbetare (begär Anden)|75|150|300|450|600|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Beräknings storlek|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|
|Min-max virtuella kärnor|1,25-10|1.50 – 12|1,75-14|2,00-16|
|Min-max minne (GB)|3,75 – 30|4.50-36|5,25-42|6.00-48|
|Minsta fördröjning för automatisk paus (minuter)|60|60|60|60|
|Columnstore-stöd|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|1536|3072|3072|3072|
|Största logg storlek (GB)|461|461|461|922|
|Maximal data storlek för TempDB (GB)|320|384|448|512|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|3200|3840|4480|5120|
|Högsta logg frekvens (Mbit/s)|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|750|900|1050|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Storskalig-allokerad beräkning – Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräknings generation (del 1)

|Prestandanivå|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100|
|Största logg storlek (TB)|1 |1 |1 |1 |1 |1 |
|Maximal data storlek för TempDB (GB)|32|64|96|128|160|192|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|I/o-svarstid (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1 000|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräknings generation (del 2)

|Prestandanivå|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100 |
|Största logg storlek (TB)|1 |1 |1 |1 |1 |1 |
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|I/o-svarstid (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Storskalig-allokerad beräkning – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Prestandanivå|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100 |100|
|Största logg storlek (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximal data storlek för TempDB (GB)|64|128|192|256|320|384|448|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|I/o-svarstid (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1 000|1200|1400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Prestandanivå|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100 |100 |
|Största logg storlek (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|I/o-svarstid (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|800|1600|2400|3200|8000|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Anteckningar

**Anmärkning 1**: storskalig är en arkitektur med flera nivåer med separata beräknings-och lagrings komponenter: [storskalig arkitektur för tjänst nivå](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Anmärkning 2**: storskalig arkitektur med flera nivåer har cachelagring på flera nivåer. Effektiv IOPS är beroende av arbets belastningen.

**Anmärkning 3**: latens är 1-2 MS för data i RBPEX SSD-baserade cacheminnet på beräknings repliker, som cachelagrar de mest använda data sidorna. Högre latens för data som hämtas från sid servrar.

## <a name="general-purpose---provisioned-compute---gen4"></a>Generell användning-allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräknings generation (del 1)

|Beräknings storlek|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|
|Största logg storlek (GB)|307|307|461|461|461|922|
|Maximal data storlek för TempDB (GB)|32|64|96|128|160|192|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|320|640|960|1280|1600|1920|
|Högsta logg frekvens (Mbit/s)|3.75|7.5|11.25|15|18,75|22.5|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1 000|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräknings generation (del 2)

|Beräknings storlek|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|3072|3072|3072|3072|4096|4096|
|Största logg storlek (GB)|922|922|922|922|1229|1229|
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)
|Max data IOPS *|2240|2560|2880|3200|5120|7680|
|Högsta logg frekvens (Mbit/s)|26,3|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Generell användning-allokerad beräkning – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Beräknings storlek|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|461|461|461|922|922|
|Maximal data storlek för TempDB (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|640|1280|1920|2560|3200|3840|4480|
|Högsta logg frekvens (Mbit/s)|7.5|15|22.5|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1 000|1200|1400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Beräknings storlek|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1229|1229|1229|1229|
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|5120|5760|6400|7680|10240|12800|25600|
|Högsta logg frekvens (Mbit/s)|30|30|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Lässkalning|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Allmänt syfte – etablerad beräkning – Fsv2-serien

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2-seriens beräknings generation (för hands version)

|Beräknings storlek|GP_Fsv2_72|
|:--- | --: |
|Beräknings generation|Fsv2-serien|
|virtuella kärnor|72|
|Minne (GB)|136,2|
|Columnstore-stöd|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|
|Maximal data storlek (GB)|4096|
|Största logg storlek (GB)|1024|
|Maximal data storlek för TempDB (GB)|333|
|Lagringstyp|Fjärr-SSD|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|23 040|
|Högsta logg frekvens (Mbit/s)|30|
|Maximalt antal samtidiga arbetare (begär Anden)|3600|
|Maximalt antal samtidiga sessioner|30,000|
|Antal repliker|1|
|Multi-AZ|Gäller inte|
|Lässkalning|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Verksamhets kritiskt allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräknings generation (del 1)

|Beräknings storlek|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|1024|1024|1024|1024|1024|1024|
|Största logg storlek (GB)|307|307|307|307|307|307|
|Maximal data storlek för TempDB (GB)|32|64|96|128|160|192|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|4,000|8,000|12 000|16,000|20,000|24,000|
|Högsta logg frekvens (Mbit/s)|8|16|24|32|40|48|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1 000|1200|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1 000|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräknings generation (del 2)

|Beräknings storlek|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|7|8|9.5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|1024|1024|1024|1024|1024|1024|
|Största logg storlek (GB)|307|307|307|307|307|307|
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Maximal data-IOPS |28 000|32,000|36 000|40,000|64,000|76 800|
|Högsta logg frekvens (Mbit/s)|56|64|64|64|64|64|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga inloggningar (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Verksamhets kritiskt allokerad beräkning – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Beräknings storlek|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|461|461|461|922|922|
|Maximal data storlek för TempDB (GB)|64|128|192|256|320|384|448|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|8000|16,000|24,000|32,000|40,000|48 000|56 000|
|Högsta logg frekvens (Mbit/s)|24|48|72|96|96|96|96|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1 000|1200|1400|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1 000|1200|1400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Beräknings storlek|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1229|1229|1229|1229|
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|64,000|72 000|80,000|96 000|128 000|160 000|204 800|
|Högsta logg frekvens (Mbit/s)|96|96|96|96|96|96|96|
|Maximalt antal samtidiga arbetare (begär Anden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga inloggningar|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Verksamhets kritiskt allokerad beräkning – M-serien

### <a name="m-series-compute-generation-preview"></a>Beräknings generation i M-serien (för hands version)

|Beräknings storlek|GP_M_128|
|:--- | --: |
|Beräknings generation|M-serien|
|virtuella kärnor|128|
|Minne (GB)|3767|
|Columnstore-stöd|Ja|
|Minnes intern OLTP-lagring (GB)|481|
|Maximal data storlek (GB)|4096|
|Största logg storlek (GB)|2048|
|Maximal data storlek för TempDB (GB)|4096|
|Lagringstyp|Lokal SSD|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|204 800|
|Högsta logg frekvens (Mbit/s)|192|
|Maximalt antal samtidiga arbetare (begär Anden)|12800|
|Maximalt antal samtidiga sessioner|30000|
|Antal repliker|4|
|Multi-AZ|Ja|
|Lässkalning|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|

\* det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Nästa steg

- För resurs gränser för DTU för en enskild databas, se [resurs gränser för enskilda databaser med inköps modellen DTU](sql-database-dtu-resource-limits-single-databases.md)
- VCore resurs gränser för elastiska pooler finns i [resurs gränser för elastiska pooler med hjälp av vCore inköps modell](sql-database-vcore-resource-limits-elastic-pools.md)
- För resurs gränser för DTU för elastiska pooler, se [resurs gränser för elastiska pooler med inköps modellen DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Resurs begränsningar för hanterade instanser finns i [resurs gränser för hanterade](sql-database-managed-instance-resource-limits.md)instanser.
- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resurs gränser på en databas server finns i [Översikt över resurs gränser på en SQL Database-Server](sql-database-resource-limits-database-server.md) för information om begränsningar på Server-och prenumerations nivåer.
