---
title: Begränsningar - Azure Database för MySQL
description: I den här artikeln beskrivs begränsningar i Azure Database for MySQL, till exempel antal anslutnings- och lagringsmotoralternativ.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 6ca09ab0578fb88e443d6e9e1f920c22457eb042
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548470"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Begränsningar i Azure Database för MySQL
I följande avsnitt beskrivs kapacitet, stöd för lagringsmotor, behörighetsstöd, stöd för datamanipuleringsuttalande och funktionsgränser i databastjänsten. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) som gäller för MySQL-databasmotorn.

## <a name="server-parameters"></a>Serverparametrar

Minimi- och maximivärdena för flera populära serverparametrar bestäms av prisnivån och virtuella kärnor. Se nedanstående tabeller för gränser.

### <a name="max_connections"></a>max_connections

|**Prisnivå**|**vCore(er)**|**Standardvärde**|**Min värde**|**Max värde**|
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

När anslutningar överskrider gränsen kan följande felmeddelande visas:
> FEL 1040 (08004): För många anslutningar

> [!IMPORTANT]
> För bästa möjliga upplevelse rekommenderar vi att du använder en anslutningspooler som ProxySQL för att effektivt hantera anslutningar.

Det tar tid och när de väl har upprättats att skapa nya klientanslutningar till MySQL upptar dessa anslutningar databasresurser, även när de är inaktiva. De flesta program begär många kortlivade anslutningar, vilket förvärrar denna situation. Resultatet är färre resurser tillgängliga för din faktiska arbetsbelastning som leder till minskade prestanda. En anslutningspooler som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Om du vill veta mer om att inrätta ProxySQL, besök vårt [blogginlägg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### <a name="query_cache_size"></a>query_cache_size

Frågecachen är inaktiverad som standard. Konfigurera parametern `query_cache_type` om du vill aktivera frågecachen. 

Läs [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) om du vill veta mer om den här parametern.

> [!NOTE]
> Frågecachen är föråldrad från och med MySQL 5.7.20 och har tagits bort i MySQL 8.0

|**Prisnivå**|**vCore(er)**|**Standardvärde**|**Min värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

Läs [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) om du vill veta mer om den här parametern.

|**Prisnivå**|**vCore(er)**|**Standardvärde**|**Min värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
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

### <a name="join_buffer_size"></a>join_buffer_size

Läs [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) om du vill veta mer om den här parametern.

|**Prisnivå**|**vCore(er)**|**Standardvärde**|**Min värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
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

### <a name="max_heap_table_size"></a>max_heap_table_size

Läs [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) om du vill veta mer om den här parametern.

|**Prisnivå**|**vCore(er)**|**Standardvärde**|**Min värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
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

### <a name="tmp_table_size"></a>tmp_table_size

Läs [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) om du vill veta mer om den här parametern.

|**Prisnivå**|**vCore(er)**|**Standardvärde**|**Min värde**|**Max värde**|
|---|---|---|---|---|
|Basic|1|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
|Basic|2|Kan inte konfigureras på basic-nivå|Ej tillämpligt|Ej tillämpligt|
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

### <a name="time_zone"></a>time_zone

Tidszonstabellerna kan fyllas i `mysql.az_load_timezone` genom att anropa den lagrade proceduren från ett verktyg som mysql-kommandoraden eller MySQL Workbench. Se [Azure-portalen](howto-server-parameters.md#working-with-the-time-zone-parameter) eller [Azure CLI-artiklarna](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) för hur du anropar den lagrade proceduren och anger tidszoner på global nivå eller sessionsnivå.

## <a name="storage-engine-support"></a>Stöd för lagringsmotor

### <a name="supported"></a>Stöds
- [Innodb](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [Minne](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Stöd saknas
- [Myisam](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [Blackhole](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [Arkiv](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [Federerade](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Stöd för privilegier

### <a name="unsupported"></a>Stöd saknas
- DBA-roll: Många serverparametrar och inställningar kan oavsiktligt försämra serverns prestanda eller förneka ACID-egenskaperna för DBMS. För att upprätthålla tjänstens integritet och serviceavtalet på produktnivå exponerar den här tjänsten inte DBA-rollen. Standardanvändarkontot, som skapas när en ny databasinstans skapas, gör att användaren kan utföra de flesta DDL- och DML-satser i den hanterade databasinstansen. 
- SUPER privilegium: På samma sätt [SUPER privilegium](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) är också begränsad.
- DEFINER: Kräver superbehörighet för att skapa och är begränsad. Om du importerar data med `CREATE DEFINER` hjälp av en säkerhetskopia `--skip-definer` tar du bort kommandona manuellt eller med kommandot när du utför en mysqldump.

## <a name="data-manipulation-statement-support"></a>Stöd för datamanipuleringsuttalande

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE`stöds, men `[LOCAL]` parametern måste anges och dirigeras till en UNC-sökväg (Azure-lagring monterad via SMB).

### <a name="unsupported"></a>Stöd saknas
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="scale-operations"></a>Skala åtgärder
- Dynamisk skalning till och från grundläggande prisnivåer stöds för närvarande inte.
- Det går inte att minska storleken på serverns lagring.

### <a name="server-version-upgrades"></a>Uppgraderingar av serverversion
- Automatiserad migrering mellan större databasmotorversioner stöds för närvarande inte. Om du vill uppgradera till nästa huvudversion tar du en [soptipp och återställer](./concepts-migrate-dump-restore.md) den till en server som skapades med den nya motorversionen.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder PITR-funktionen skapas den nya servern med samma konfigurationer som den server den baseras på.
- Det går inte att återställa en borttagen server.

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för slutpunkter för VNet-tjänst är endast för servrar med allmänt syfte och minnesoptimerade.

### <a name="storage-size"></a>Lagringsstorlek
- Se prisnivåer för [lagringsstorleksgränserna](concepts-pricing-tiers.md) per prisnivå.

## <a name="current-known-issues"></a>Aktuella kända problem
- MySQL-serverinstansen visar fel serverversion när anslutningen har upprättats. Använd kommandot `select version();` om du vill hämta rätt serverinstansmotorversion.

## <a name="next-steps"></a>Nästa steg
- [Vad är tillgängligt på varje tjänstnivå](concepts-pricing-tiers.md)
- [MySQL-databasversioner som stöds](concepts-supported-versions.md)
