---
title: Begränsningar – Azure Database for MySQL
description: Den här artikeln beskriver begränsningar i Azure Database för MySQL, till exempel antalet anslutning och lagringsalternativ för motorn.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 6954f306e0d0a346bd8f39776d987af99f7574dd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299098"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Begränsningar i Azure Database for MySQL
I följande avsnitt beskrivs kapacitet, stödet för lagring, privilegier support, manipulering av instruktionen stöd och funktionella begränsningar i databastjänsten. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) som gäller för databas motorn MySQL.

## <a name="server-parameters"></a>Server parametrar

De lägsta och högsta värdena för flera populära Server parametrar bestäms av pris nivån och virtuella kärnor. Se de följande tabellerna som gränser.

### <a name="max_connections"></a>max_connections

|**Prisnivå**|**vCore (s)**|**Standardvärde**|**Minsta värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Generell användning|2|300|10|600|
|Generell användning|4|625|10|1250|
|Generell användning|8|1250|10|2500|
|Generell användning|16|2500|10|5000|
|Generell användning|32|5000|10|10000|
|Generell användning|64|10000|10|20000|
|Minnesoptimerad|2|600|10|800|
|Minnesoptimerad|4|1250|10|2500|
|Minnesoptimerad|8|2500|10|5000|
|Minnesoptimerad|16|5000|10|10000|
|Minnesoptimerad|32|10000|10|20000|

När anslutningar överskrider gränsen, kan följande felmeddelande visas:
> FEL 1040 (08004): För många anslutningar

> [!IMPORTANT]
> För bästa möjliga upplevelse rekommenderar vi att du använder en anslutningspool som ProxySQL för att effektivt hantera anslutningar.

Att skapa nya klient anslutningar till MySQL tar tid och när de har upprättats, använder dessa anslutningar databas resurser, även när de är inaktiva. De flesta program begär många anslutningar för kort period, vilket utvärderar den här situationen. Resultatet är färre resurser som är tillgängliga för den faktiska arbets belastningen, vilket leder till försämrade prestanda. En anslutningspool som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Mer information om hur du konfigurerar ProxySQL finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

## <a name="query_cache_size"></a>query_cache_size

Frågesyntaxen är inaktive rad som standard. Konfigurera `query_cache_type` parametern om du vill aktivera frågekörningen. 

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) .

> [!NOTE]
> Frågesyntaxen är föråldrad från MySQL-5.7.20 och har tagits bort i MySQL 8,0

|**Prisnivå**|**vCore (s)**|**Standardvärde**|**Minsta värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Generell användning|2|0|0|16777216|
|Generell användning|4|0|0|33554432|
|Generell användning|8|0|0|67108864|
|Generell användning|16|0|0|134217728|
|Generell användning|32|0|0|134217728|
|Generell användning|64|0|0|134217728|
|Minnesoptimerad|2|0|0|33554432|
|Minnesoptimerad|4|0|0|67108864|
|Minnesoptimerad|8|0|0|134217728|
|Minnesoptimerad|16|0|0|134217728|
|Minnesoptimerad|32|0|0|134217728|

## <a name="sort_buffer_size"></a>sort_buffer_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde**|**Minsta värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Generell användning|2|524288|32768|4194304|
|Generell användning|4|524288|32768|8388608|
|Generell användning|8|524288|32768|16777216|
|Generell användning|16|524288|32768|33554432|
|Generell användning|32|524288|32768|33554432|
|Generell användning|64|524288|32768|33554432|
|Minnesoptimerad|2|524288|32768|8388608|
|Minnesoptimerad|4|524288|32768|16777216|
|Minnesoptimerad|8|524288|32768|33554432|
|Minnesoptimerad|16|524288|32768|33554432|
|Minnesoptimerad|32|524288|32768|33554432|

## <a name="join_buffer_size"></a>join_buffer_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde**|**Minsta värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Generell användning|2|262144|128|268435455|
|Generell användning|4|262144|128|536870912|
|Generell användning|8|262144|128|1073741824|
|Generell användning|16|262144|128|2147483648|
|Generell användning|32|262144|128|4294967295|
|Generell användning|64|262144|128|4294967295|
|Minnesoptimerad|2|262144|128|536870912|
|Minnesoptimerad|4|262144|128|1073741824|
|Minnesoptimerad|8|262144|128|2147483648|
|Minnesoptimerad|16|262144|128|4294967295|
|Minnesoptimerad|32|262144|128|4294967295|

## <a name="max_heap_table_size"></a>max_heap_table_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde**|**Minsta värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Generell användning|2|16777216|16384|268435455|
|Generell användning|4|16777216|16384|536870912|
|Generell användning|8|16777216|16384|1073741824|
|Generell användning|16|16777216|16384|2147483648|
|Generell användning|32|16777216|16384|4294967295|
|Generell användning|64|16777216|16384|4294967295|
|Minnesoptimerad|2|16777216|16384|536870912|
|Minnesoptimerad|4|16777216|16384|1073741824|
|Minnesoptimerad|8|16777216|16384|2147483648|
|Minnesoptimerad|16|16777216|16384|4294967295|
|Minnesoptimerad|32|16777216|16384|4294967295|

## <a name="tmp_table_size"></a>tmp_table_size

Läs mer om den här parametern i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) .

|**Prisnivå**|**vCore (s)**|**Standardvärde**|**Minsta värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på Basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Generell användning|2|16777216|1024|67108864|
|Generell användning|4|16777216|1024|134217728|
|Generell användning|8|16777216|1024|268435456|
|Generell användning|16|16777216|1024|536870912|
|Generell användning|32|16777216|1024|1073741824|
|Generell användning|64|16777216|1024|1073741824|
|Minnesoptimerad|2|16777216|1024|134217728|
|Minnesoptimerad|4|16777216|1024|268435456|
|Minnesoptimerad|8|16777216|1024|536870912|
|Minnesoptimerad|16|16777216|1024|1073741824|
|Minnesoptimerad|32|16777216|1024|1073741824|

## <a name="storage-engine-support"></a>Stödet för lagring

### <a name="supported"></a>Stöds
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MINNESOPTIMERADE](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Stöd saknas
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARKIVATTRIBUTET](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [EXTERNT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Stöd för behörighet

### <a name="unsupported"></a>Stöd saknas
- DBA-roll: många serverparametrar och inställningar kan oavsiktligt försämra serverprestanda eller negera ACID-egenskaper för DBMS. Därför för att upprätthålla integriteten för tjänsten och SLA på en produktnivå, exponerar den här tjänsten inte DBA-rollen. Standardanvändarkontot som skapas när en ny databasinstans har skapats, kan användaren utföra de flesta av DDL och DML-instruktioner i hanterade databasinstansen. 
- SUPER Privilege: liknande [superbehörighet](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) är också begränsad.
- Avrundning: kräver Super-behörighet för att skapa och är begränsad. Om du importerar data med hjälp av en säkerhets kopia tar du bort de `CREATE DEFINER` kommandona manuellt eller genom att använda kommandot `--skip-definer` när du utför en mysqldump.

## <a name="data-manipulation-statement-support"></a>Stöd för manipulering av instruktionen

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE` stöds, men `[LOCAL]`-parametern måste anges och dirigeras till en UNC-sökväg (Azure Storage monteras via SMB).

### <a name="unsupported"></a>Stöd saknas
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="scale-operations"></a>Skalningsåtgärder
- Dynamisk skalning till och från grundläggande prisnivåerna stöds för närvarande inte.
- Minska lagringsstorlek server stöds inte.

### <a name="server-version-upgrades"></a>Server-versionsuppgraderingar
- Automatisk migrering mellan större database engine-versioner stöds för närvarande inte. Om du vill uppgradera till nästa högre version kan du göra en [dumpning och återställa](./concepts-migrate-dump-restore.md) den till en server som har skapats med den nya motor versionen.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder funktionen PITR, skapas den nya servern med samma konfiguration som den är baserad på-servern.
- När du återställer en borttagen server stöds inte.

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

### <a name="storage-size"></a>Lagrings storlek
- Se [pris nivåer](concepts-pricing-tiers.md) för lagrings storleks gränser per pris nivå.

## <a name="current-known-issues"></a>Aktuella kända problem
- MySQL-serverinstans visar fel serverversionen när anslutningen har upprättats. För att få rätt version av Server instans motorn använder du kommandot `select version();`.

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt i varje tjänst nivå](concepts-pricing-tiers.md)
- [MySQL-databas versioner som stöds](concepts-supported-versions.md)
