---
title: Begränsningar i Azure-databas för MariaDB
description: Den här artikeln beskriver begränsningar i Azure Database for MariaDB, till exempel antalet anslutning och lagringsalternativ för motorn.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 4fb774341b19034d3905341a99be2114a0d1e18b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835717"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Begränsningar i Azure-databas för MariaDB
I följande avsnitt beskrivs kapacitet, stödet för lagring, privilegier support, manipulering av instruktionen stöd och funktionella begränsningar i databastjänsten.

## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per prisnivå och virtuella kärnor är följande:

|**Prisnivå**|**virtuella kärnor**| **Högsta antal anslutningar**|
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

När anslutningar överskrider gränsen, kan följande felmeddelande visas:
> FEL 1040 (08004): För många anslutningar

## <a name="storage-engine-support"></a>Stödet för lagring

### <a name="supported"></a>Stöds
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MINNE](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Stöds inte
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [SVARTLISTADE](https://mariadb.com/kb/en/library/blackhole/l)
- [ARKIV](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Stöd för behörighet

### <a name="unsupported"></a>Stöds inte
- DBA-roll: många serverparametrar och inställningar kan oavsiktligt försämra serverprestanda eller negera ACID-egenskaper för DBMS. Därför för att upprätthålla integriteten för tjänsten och SLA på en produktnivå, exponerar den här tjänsten inte DBA-rollen. Standardanvändarkontot som skapas när en ny databasinstans har skapats, kan användaren utföra de flesta av DDL och DML-instruktioner i hanterade databasinstansen.
- SUPER behörighet: på samma sätt [SUPER behörighet](https://mariadb.com/kb/en/library/grant/#global-privileges) är också begränsad.

## <a name="data-manipulation-statement-support"></a>Stöd för manipulering av instruktionen

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE` stöds, men `[LOCAL]` parametern måste anges och dirigeras till en UNC-sökväg (Azure storage monterad via SMB).

### <a name="unsupported"></a>Stöds inte
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="scale-operations"></a>Skalningsåtgärder
- Dynamisk skalning till och från grundläggande prisnivåerna stöds för närvarande inte.
- Minska lagringsstorlek server stöds inte.

### <a name="server-version-upgrades"></a>Server-versionsuppgraderingar
- Automatisk migrering mellan större database engine-versioner stöds för närvarande inte.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder funktionen PITR, skapas den nya servern med samma konfiguration som den är baserad på-servern.
- När du återställer en borttagen server stöds inte.

### <a name="subscription-management"></a>Prenumerationshantering
- Dynamiskt flytta förinställda servrar i prenumerationen och resursgruppen stöds för närvarande inte.

## <a name="current-known-issues"></a>Aktuella kända problem
- MariaDB-serverinstans visar felaktig serverversionen när anslutningen har upprättats. För att få rätt server instans Motorversion kan använda den `select version();` kommando.

## <a name="next-steps"></a>Nästa steg
- [Vad är tillgängliga i varje tjänstnivå](concepts-pricing-tiers.md)
- [MariaDB databas-versioner som stöds](concepts-supported-versions.md)
