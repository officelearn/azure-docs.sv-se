---
title: vCore-resursgränser - en enda databas
description: På den här sidan beskrivs några vanliga vCore-resursgränser för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481073"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Resursgränser för enskilda databaser med hjälp av inköpsmodellen för vCore

Den här artikeln innehåller detaljerade resursgränser för enskilda Azure SQL Database-databaser med hjälp av vCore-inköpsmodellen.

Information om DTU-inköpsmodellgränser för enskilda databaser på en SQL Database-server finns i [Översikt över resursbegränsningar på en SQL Database-server](sql-database-resource-limits-database-server.md).

Du kan ange tjänstnivå, beräkningsstorlek och lagringsbelopp för en enskild databas med [Azure-portalen,](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server) [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)eller [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Om du vill skala vägledning och överväganden finns i [Skala en enskild databas](sql-database-single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Allmänt ändamål - serverlös beräkning - Gen5

Den [serverlösa beräkningsnivån](sql-database-serverless.md) är endast tillgänglig på Gen5-maskinvara.

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräkningsgenerering (del 1)

|Beräkna storlek|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|Min-max vCores|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Min-max-minne (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Min fördröjning för automatisk paus (minuter)|60|60|60|60|60|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|512|1024|1024|1024|1536|
|Max loggstorlek (GB)|154|307|307|307|461|
|TempDB max datastorlek (GB)|32|64|128|192|256|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS *|320|640|1280|1920|2560|
|Max logghastighet (MBps)|3.8|7.5|15|22.5|30|
|Max samtidiga arbetstagare (begäranden)|75|150|300|450|600|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräkning generation (del 2)

|Beräkna storlek|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|Min-max vCores|1.25-10|1.50-12|1.75-14|2.00-16|
|Min-max-minne (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Min fördröjning för automatisk paus (minuter)|60|60|60|60|
|Support för Columnstore|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|1536|3072|3072|3072|
|Max loggstorlek (GB)|461|461|461|922|
|TempDB max datastorlek (GB)|320|384|448|512|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS *|3200|3840|4480|5120|
|Max logghastighet (MBps)|30|30|30|30|
|Max samtidiga arbetstagare (begäranden)|750|900|1050|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Hyperskala - etablerad beräkning - Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräkningsgenerering (del 1)

|Prestandanivå|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|[RBPEX (PÅ ANDRA)](sql-database-service-tier-hyperscale.md#compute) Storlek|3X minne|3X minne|3X minne|3X minne|3X minne|3X minne|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (TB)|100 |100 |100 |100 |100 |100|
|Max loggstorlek (TB)|1 |1 |1 |1 |1 |1 |
|TempDB max datastorlek (GB)|32|64|96|128|160|192|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Max logghastighet (MBps)|100 |100 |100 |100 |100 |100 |
|IO-latens (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Max samtidiga arbetstagare (begäranden)|200|400|600|800|1000|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Lagring av säkerhetskopiering av lagring|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräkningsgenerering (del 2)

|Prestandanivå|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159.5|
|[RBPEX (PÅ ANDRA)](sql-database-service-tier-hyperscale.md#compute) Storlek|3X minne|3X minne|3X minne|3X minne|3X minne|3X minne|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (TB)|100 |100 |100 |100 |100 |100 |
|Max loggstorlek (TB)|1 |1 |1 |1 |1 |1 |
|TempDB max datastorlek (GB)|224|256|288|320|512|768|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Max logghastighet (MBps)|100 |100 |100 |100 |100 |100 |
|IO-latens (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Max samtidiga arbetstagare (begäranden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Lagring av säkerhetskopiering av lagring|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hyperskala - etablerad beräkning - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräkningsgenerering (del 1)

|Prestandanivå|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|2|4|6|8|10|12|14|
|Minne (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX (PÅ ANDRA)](sql-database-service-tier-hyperscale.md#compute) Storlek|3X minne|3X minne|3X minne|3X minne|3X minne|3X minne|3X minne|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (TB)|100 |100 |100 |100 |100 |100 |100|
|Max loggstorlek (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB max datastorlek (GB)|64|128|192|256|320|384|448|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Max logghastighet (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO-latens (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Max samtidiga arbetstagare (begäranden)|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lagring av säkerhetskopiering av lagring|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräkning generation (del 2)

|Prestandanivå|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|16|18|20|24|32|40|80|
|Minne (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX (PÅ ANDRA)](sql-database-service-tier-hyperscale.md#compute) Storlek|3X minne|3X minne|3X minne|3X minne|3X minne|3X minne|3X minne|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (TB)|100 |100 |100 |100 |100 |100 |100 |
|Max loggstorlek (TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB max datastorlek (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max data IOPS *|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Max logghastighet (MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO-latens (ungefärlig)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|[Anmärkning 3](#notes)|
|Max samtidiga arbetstagare (begäranden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lagring av säkerhetskopiering av lagring|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Anteckningar

**Anmärkning 1:** Hyperskala är en flerskiktad arkitektur med separata beräknings- och lagringskomponenter: [Hyperskala servicenivåarkitektur](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Anmärkning 2**: Hyperskala flerskiktad arkitektur har cachelagring på flera nivåer. Effektiva IOPS beror på arbetsbelastningen.

**Anmärkning 3:** Svarstiden är 1-2 ms för data i RBPEX SSD-baserad cache på beräkningsrepliker, som cachelagrar de flesta använda datasidor. Högre svarstid för data som hämtas från sidservrar.

## <a name="general-purpose---provisioned-compute---gen4"></a>Allmänt ändamål - etablerad beräkning - Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna i östra eller södra Brasilien.

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräkningsgenerering (del 1)

|Beräkna storlek|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|1024|1024|1536|1536|1536|3072|
|Max loggstorlek (GB)|307|307|461|461|461|922|
|TempDB max datastorlek (GB)|32|64|96|128|160|192|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS *|320|640|960|1280|1600|1920|
|Max logghastighet (MBps)|3.75|7.5|11.25|15|18.75|22.5|
|Max samtidiga arbetstagare (begäranden)|200|400|600|800|1000|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräkningsgenerering (del 2)

|Beräkna storlek|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159.5|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|3072|3072|3072|3072|4096|4096|
|Max loggstorlek (GB)|922|922|922|922|1229|1229|
|TempDB max datastorlek (GB)|224|256|288|320|512|768|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)
|Max data IOPS *|2240|2560|2880|3200|5120|7680|
|Max logghastighet (MBps)|26.3|30|30|30|30|30|
|Max samtidiga arbetstagare (begäranden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Allmänt ändamål - etablerad beräkning - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräkningsgenerering (del 1)

|Beräkna storlek|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|2|4|6|8|10|12|14|
|Minne (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Max loggstorlek (GB)|307|307|461|461|461|922|922|
|TempDB max datastorlek (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS *|640|1280|1920|2560|3200|3840|4480|
|Max logghastighet (MBps)|7.5|15|22.5|30|30|30|30|
|Max samtidiga arbetstagare (begäranden)|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräkning generation (del 2)

|Beräkna storlek|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|16|18|20|24|32|40|80|
|Minne (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Max datastorlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max loggstorlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB max datastorlek (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS *|5120|5760|6400|7680|10240|12800|25600|
|Max logghastighet (MBps)|30|30|30|30|30|30|30|
|Max samtidiga arbetstagare (begäranden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|1|1|1|1|1|1|1|
|Flera AZ|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Allmänt ändamål - etablerad beräkning - Fsv2-serien

### <a name="fsv2-series-compute-generation-preview"></a>Beräkningsgenerering i Fsv2-serien (förhandsversion)

|Beräkna storlek|GP_Fsv2_72|
|:--- | --: |
|Beräkna generering|Fsv2-serien|
|vCores (virtuella)|72|
|Minne (GB)|136.2|
|Support för Columnstore|Ja|
|OLTP-lagring i minnet (GB)|Ej tillämpligt|
|Max datastorlek (GB)|4096|
|Max loggstorlek (GB)|1024|
|TempDB max datastorlek (GB)|333|
|Lagringstyp|Fjärr-SSD|
|IO-latens (ungefärlig)|5-7 ms (skriva)<br>5-10 ms (läs)|
|Max data IOPS *|12,800|
|Max logghastighet (MBps)|30|
|Max samtidiga arbetstagare (begäranden)|3600|
|Maximalt antal samtidiga inloggningar|3600|
|Maximalt antal samtidiga sessioner|30,000|
|Antal repliker|1|
|Flera AZ|Ej tillämpligt|
|Lässkalning|Ej tillämpligt|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Affärskritisk - etablerad beräkning - Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna i östra eller södra Brasilien.

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräkningsgenerering (del 1)

|Beräkna storlek|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|1|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Max datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Max loggstorlek (GB)|307|307|307|307|307|307|
|TempDB max datastorlek (GB)|32|64|96|128|160|192|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS *|4 000|8,000|12 000|16 000|20 000|24,000|
|Max logghastighet (MBps)|8|16|24|32|40|48|
|Max samtidiga arbetstagare (begäranden)|200|400|600|800|1000|1200|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1000|1200|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräkningsgenerering (del 2)

|Beräkna storlek|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräkna generering|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|Gen4 (På andra)|
|vCores (virtuella)|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159.5|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|7|8|9.5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Max datastorlek (GB)|1024|1024|1024|1024|1024|1024|
|Max loggstorlek (GB)|307|307|307|307|307|307|
|TempDB max datastorlek (GB)|224|256|288|320|512|768|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS |28,000|32,000|36,000|40 000|64,000|76 800|
|Max logghastighet (MBps)|56|64|64|64|64|64|
|Max samtidiga arbetstagare (begäranden)|1400|1600|1800|2000|3200|4800|
|Max samtidiga inloggningar (förfrågningar)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Affärskritisk - etablerad beräkning - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräkningsgenerering (del 1)

|Beräkna storlek|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|2|4|6|8|10|12|14|
|Minne (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|Max datastorlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Max loggstorlek (GB)|307|307|461|461|461|922|922|
|TempDB max datastorlek (GB)|64|128|192|256|320|384|448|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS *|8000|16 000|24,000|32,000|40 000|48,000|56,000|
|Max logghastighet (MBps)|24|48|72|96|96|96|96|
|Max samtidiga arbetstagare (begäranden)|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräkning generation (del 2)

|Beräkna storlek|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräkna generering|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|Gen5 (På andra)|
|vCores (virtuella)|16|18|20|24|32|40|80|
|Minne (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Support för Columnstore|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|OLTP-lagring i minnet (GB)|15,77|18.14|20.51|25.25|37.94|52.23|131.64|
|Max datastorlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Max loggstorlek (GB)|922|922|922|1229|1229|1229|1229|
|TempDB max datastorlek (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS *|64,000|72,000|80 000|96,000|128,000|160 000|204,800|
|Max logghastighet (MBps)|96|96|96|96|96|96|96|
|Max samtidiga arbetstagare (begäranden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga inloggningar|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Antal repliker|4|4|4|4|4|4|4|
|Flera AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Affärskritisk - etablerad beräkning - M-serien

### <a name="m-series-compute-generation-preview"></a>Beräkningsgenerering i M-serien (förhandsversion)

|Beräkna storlek|BC_M_128|
|:--- | --: |
|Beräkna generering|M-serien|
|vCores (virtuella)|128|
|Minne (GB)|3767.1|
|Support för Columnstore|Ja|
|OLTP-lagring i minnet (GB)|1768|
|Max datastorlek (GB)|4096|
|Max loggstorlek (GB)|2048|
|TempDB max datastorlek (GB)|4096|
|Lagringstyp|Lokal SSD|
|IO-latens (ungefärlig)|1-2 ms (skriva)<br>1-2 ms (läs)|
|Max data IOPS *|160 000|
|Max logghastighet (MBps)|264|
|Max samtidiga arbetstagare (begäranden)|12,800|
|Maximalt antal samtidiga inloggningar|12,800|
|Maximalt antal samtidiga sessioner|30000|
|Antal repliker|4|
|Flera AZ|Ja|
|Lässkalning|Ja|
|Inkluderad lagring av säkerhetskopiering|1X DB storlek|

\*Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktiska IOPS är arbetsbelastningsberoende. Mer information finns i [Data IO Governance](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra oanvänt utrymme. Mer information finns [i Hantera filutrymme i Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Nästa steg

- För DTU-resursgränser för en enskild databas finns i [resursgränser för enskilda databaser med hjälp av DTU-inköpsmodellen](sql-database-dtu-resource-limits-single-databases.md)
- För vCore-resursgränser för elastiska pooler finns i [resursgränser för elastiska pooler med hjälp av vCore-inköpsmodellen](sql-database-vcore-resource-limits-elastic-pools.md)
- För DTU-resursgränser för elastiska pooler finns i [resursgränser för elastiska pooler med DTU-inköpsmodellen](sql-database-dtu-resource-limits-elastic-pools.md)
- För resursgränser för hanterade instanser finns i [resursbegränsningar för hanterade instanser](sql-database-managed-instance-resource-limits.md).
- Information om allmänna Azure-gränser finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resursbegränsningar på en databasserver finns i [översikt över resursbegränsningar på en SQL Database-server](sql-database-resource-limits-database-server.md) för information om begränsningar på server- och prenumerationsnivå.
