---
title: Begränsningar i Azure-databas för MySQL
description: Den här artikeln beskriver begränsningar i Azure-databas för MySQL, till exempel antal anslutning och lagringsalternativ för motorn.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 2fc224445f89a0b0b4afdc0ef1d0eb1b25b45f36
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309927"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Begränsningar i Azure-databas för MySQL
I följande avsnitt beskrivs kapacitet, stödet för lagring, privilegium support, stöd för data manipulation instruktionen och funktionella gränser i databastjänsten för. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) avser MySQL database engine.

## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per prisnivå och vCores är följande: 

|**Prisnivå**|**vCore(s)**| **Högsta antal anslutningar**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Generellt syfte| 2| 300|
|Generellt syfte| 4| 625|
|Generellt syfte| 8| 1250|
|Generellt syfte| 16| 2500|
|Generellt syfte| 32| 5000|
|Minnesoptimerad| 2| 600|
|Minnesoptimerad| 4| 1250|
|Minnesoptimerad| 8| 2500|
|Minnesoptimerad| 16| 5000|

När anslutningar överskrider gränsen, får du följande fel:
> FEL 1040 (08004): För många anslutningar

## <a name="storage-engine-support"></a>Stödet för lagring

### <a name="supported"></a>Stöds
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MINNE](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Stöds inte
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [SVARTLISTADE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARKIV](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERERAD](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Stöd för behörighet

### <a name="unsupported"></a>Stöds inte
- DBA rollen: många parametrar för server och inställningar kan oavsiktligt försämra serverprestanda eller negera ACID DBMS-egenskaper. Därför för att upprätthålla tjänsten integritet och SLA på en produktnivå, visar tjänsten inte gränssnittshändelsen DBA-rollen. Standard-användarkonto som skapas när en ny databasinstans har skapats, kan användaren utföra de flesta av DDL och DML-instruktioner i hanterade databasinstansen. 
- SUPER privilegium: på samma sätt [SUPER privilegium](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) är också begränsad.

## <a name="data-manipulation-statement-support"></a>Stöd för data manipulation instruktionen

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE` stöds, men `[LOCAL]` parametern måste anges och dirigeras till en UNC-sökväg (Azure lagringsutrymmet via SMB).

### <a name="unsupported"></a>Stöds inte
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="scale-operations"></a>Skalningsåtgärder
- Dynamisk skalning till och från de grundläggande prisnivåerna stöds inte för närvarande.
- Minska lagringsstorleken för server stöds inte.

### <a name="server-version-upgrades"></a>Version serveruppgraderingarna
- Automatisk migrering mellan större database engine versioner stöds inte för närvarande.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder funktionen PITR, skapas den nya servern med samma konfiguration som den är baserad på-servern.
- Återställa en borttagen server stöds inte.

### <a name="subscription-management"></a>Prenumerationshantering
- Dynamiskt flytta förskapade servrar över prenumeration och resursgrupp stöds inte för närvarande.

## <a name="current-known-issues"></a>Aktuella kända problem
- MySQL-serverinstans visar fel serverversionen efter anslutningen har upprättats. För att få rätt server instans Motorversion kan använda den `select version();` kommando.

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt på respektive tjänstnivå](concepts-pricing-tiers.md)
- [Versioner som stöds MySQL-databas](concepts-supported-versions.md)
