---
title: Begränsningar i Azure-databas för MariaDB
description: Den här artikeln beskriver begränsningar i Azure Database for MariaDB, till exempel antalet anslutning och lagringsalternativ för motorn.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 79d6e185b64fdaf332f877718487809ba6273441
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895796"
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
- [SVARTLISTADE](https://mariadb.com/kb/en/library/blackhole/)
- [ARKIV](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Stöd för behörighet

### <a name="unsupported"></a>Stöds inte
- DBA-roll: Många serverparametrar och inställningar kan du oavsiktligt försämra serverprestanda eller negera ACID-egenskaper för DBMS. Därför för att upprätthålla integriteten för tjänsten och SLA på en produktnivå, exponerar den här tjänsten inte DBA-rollen. Standardanvändarkontot som skapas när en ny databasinstans har skapats, kan användaren utföra de flesta av DDL och DML-instruktioner i hanterade databasinstansen.
- SUPER privilegium: På samma sätt [SUPER behörighet](https://mariadb.com/kb/en/library/grant/#global-privileges) är också begränsad.
- DEFINER: Kräver super behörighet för att skapa och är begränsad. Om du importerar data med hjälp av en säkerhetskopia, ta bort den `CREATE DEFINER` kommandon manuellt eller genom att använda den `--skip-definer` kommandot när du utför en mysqldump.

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

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

## <a name="current-known-issues"></a>Aktuella kända problem
- MariaDB-serverinstans visar felaktig serverversionen när anslutningen har upprättats. För att få rätt server instans Motorversion kan använda den `select version();` kommando.

## <a name="next-steps"></a>Nästa steg
- [Vad är tillgängliga i varje tjänstnivå](concepts-pricing-tiers.md)
- [MariaDB databas-versioner som stöds](concepts-supported-versions.md)
