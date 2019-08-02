---
title: Azure SQL Database vCore resurs gränser – enkel databas | Microsoft Docs
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
ms.date: 04/22/2019
ms.openlocfilehash: 661ac9ea3fd87268c43bf0a0eba66e30f636fc77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566210"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Resurs gränser för enskilda databaser med den vCore-baserade inköps modellen

Den här artikeln innehåller detaljerade resurs gränser för Azure SQL Database enskilda databaser med den vCore-baserade inköps modellen.

För DTU-baserade inköps modell gränser för enskilda databaser på en SQL Database-Server, se [Översikt över resurs gränser på en SQL Database Server](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva minska en databas för att frigöra oanvänt utrymme. Mer information finns i [hantera utrymmet i Azure SQL Database](sql-database-file-space-management.md).

Du kan ange tjänst nivå, beräknings storlek och lagrings belopp för en enskild databas med hjälp av [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)eller [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Information om skalning och överväganden finns i [skala en enskild databas](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Generell användning tjänst nivå: Lagrings storlekar och beräknings storlekar

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i AustraliaEast-regionen.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Generell användning tjänst nivå: Generation 4-beräknings plattform (del 1)

|Beräknings storlek|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generation|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal data storlek (GB)|1024|1024|1024|1536|1536|1536|
|Största logg storlek (GB)|307|307|307|461|461|461|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Logg hastighets gränser (Mbit/s)|3.75|7.5|11.25|15|18,75|22.5|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läs skalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Generell användning tjänst nivå: Generation 4-beräknings plattform (del 2)

|Beräknings storlek|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generation|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|168|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal data storlek (GB)|1536|3072|3072|3072|4096|4096|
|Största logg storlek (GB)|461|922|922|922|1229|1229|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)
|Mål-IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Logg hastighets gränser (Mbit/s)|26,25|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läs skalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Generell användning tjänst nivå: Generation 5-beräknings plattform (del 1)

|Beräknings storlek|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generation|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Minne (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|307|461|461|461|461|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Logg hastighets gränser (Mbit/s)|3.75|7.5|11.25|15|18,75|22.5|26,25|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|1400|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läs skalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Generell användning tjänst nivå: Generation 5-beräknings plattform (del 2)

|Beräknings storlek|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generation|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Minne (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Logg hastighets gränser (Mbit/s)|30|30|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läs skalbarhet|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

### <a name="serverless-compute-tier"></a>Serverlös beräkningsnivå

[Server lös beräknings nivån](sql-database-serverless.md) är i för hands version och endast för enskilda databaser som använder vCore inköps modell.

#### <a name="generation-5-compute-platform"></a>Generation 5-beräknings plattform

|Beräknings storlek|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|H/W-generation|5|5|5|
|Min-max virtuella kärnor|0,5-1|0,5-2|0,5-4|
|Min-max minne (GB)|2.02-3|2.05-6|2.10-12|
|Minsta fördröjning för automatisk paus (timmar)|6|6|6|
|Columnstore-stöd|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Saknas|Gäller inte|
|Maximal data storlek (GB)|512|1024|1024|
|Största logg storlek (GB)|12|24|48|
|TempDB-storlek (GB)|32|64|128|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-svarstid (ungefärlig)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Mål-IOPS (64 KB)|500|1000|2000|
|Logg hastighets gränser (Mbit/s)|2.5|5.6|10|
|Maximalt antal samtidiga arbetare (begär Anden)|75|150|300|
|Maximalt antal tillåtna sessioner|30000|30000|30000|
|Antal repliker|1|1|1|
|Multi-AZ|Gäller inte|Saknas|Gäller inte|
|Läs skalbarhet|Gäller inte|Saknas|Gäller inte|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>Affärskritisk tjänst nivå för allokerad beräknings nivå

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i AustraliaEast-regionen.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Affärskritisk tjänst nivå: Generation 4-beräknings plattform (del 1)

|Beräknings storlek|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generation|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|650|650|650|650|650|650|
|Största logg storlek (GB)|195|195|195|195|195|195|
|TempDB-storlek (GB)|32|64|96|128|160|192|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Logg hastighets gränser (Mbit/s)|8|16|24|32|40|48|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1000|1200|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Affärskritisk tjänst nivå: Generation 4-beräknings plattform (del 2)

|Beräknings storlek|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W-generation|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|168|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|7|8|9,5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|650|650|650|650|1024|1024|
|Största logg storlek (GB)|195|195|195|195|307|307|
|TempDB-storlek (GB)|224|256|288|320|384|384|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Logg hastighets gränser (Mbit/s)|56|64|64|64|64|64|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga inloggningar (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Affärskritisk tjänst nivå: Generation 5-beräknings plattform (del 1)

|Beräknings storlek|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generation|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Minne (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|307|461|461|922|922|
|TempDB-storlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Logg hastighets gränser (Mbit/s)|12|24|36|48|60|72|84|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1000|1200|1400|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Affärskritisk tjänst nivå: Generation 5-beräknings plattform (del 2)

|Beräknings storlek|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W-generation|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Minne (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB-storlek (GB)|384|384|384|384|384|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-svarstid (ungefärlig)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Mål-IOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Logg hastighets gränser (Mbit/s)|96|96|96|96|96|96|96|
|Maximalt antal samtidiga arbetare (begär Anden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga inloggningar|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

## <a name="hyperscale-service-tier"></a>Hyperskalatjänstnivå

### <a name="generation-5-compute-platform"></a>Generation 5-beräknings plattform

|Prestandanivå|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W-generation|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Minne (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Största logg storlek (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|TempDB-storlek (GB)|64|128|256|384|384|384|384|384|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Mål-IOPS (64 KB)| [Anmärkning 1](#note-1) |[Anmärkning 1](#note-1)|[Anmärkning 1](#note-1) |[Anmärkning 1](#note-1) |[Anmärkning 1](#note-1) |[Anmärkning 1](#note-1) |[Anmärkning 1](#note-1) | [Anmärkning 1](#note-1) |
|I/o-svarstid (ungefärlig)|Ska fastställas|Ska fastställas|Ska fastställas|Ska fastställas|Ska fastställas|Ska fastställas|Ska fastställas|Ska fastställas|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|800|1600|2400|3200|4000|8000|
|Maximalt antal tillåtna sessioner|30000|30000|30000|30000|30000|30000|30000|30000|
|Antal repliker|2|2|2|2|2|2|2|2|
|Multi-AZ|Gäller inte|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Gäller inte|
|Läs skalbarhet|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring |7|7|7|7|7|7|7|7|
|||

### <a name="note-1"></a>Anmärkning 1

Hög skalning är en arkitektur med flera nivåer med cachelagring på flera nivåer. Effektiv IOPS är beroende av arbets belastningen.

### <a name="next-steps"></a>Nästa steg

- För resurs gränser för DTU för en enskild databas, se [resurs gränser för enskilda databaser med hjälp av den DTU-baserade inköps modellen](sql-database-dtu-resource-limits-single-databases.md)
- VCore resurs gränser för elastiska pooler finns i [resurs gränser för elastiska pooler med hjälp av vCore-baserad inköps modell](sql-database-vcore-resource-limits-elastic-pools.md)
- För DTU-resurs gränser för elastiska pooler, se [resurs gränser för elastiska pooler med hjälp av den DTU-baserade inköps modellen](sql-database-dtu-resource-limits-elastic-pools.md)
- Resurs begränsningar för hanterade instanser finns i [resurs gränser](sql-database-managed-instance-resource-limits.md)för hanterade instanser.
- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
- Information om resurs gränser på en databas server finns i [Översikt över resurs gränser på en SQL Database-Server](sql-database-resource-limits-database-server.md) för information om begränsningar på Server-och prenumerations nivåer.
