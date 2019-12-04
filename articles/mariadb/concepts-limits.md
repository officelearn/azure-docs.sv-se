---
title: Begränsningar – Azure Database for MariaDB
description: I den här artikeln beskrivs begränsningar i Azure Database for MariaDB, till exempel antalet anslutnings-och lagrings motor alternativ.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fc89b6233602c81ea622a528c223adf2003f0f68
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74772504"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Begränsningar i Azure Database for MariaDB
I följande avsnitt beskrivs kapacitet, stöd för lagrings motor, stöd för stöd för data manipulation och funktionella gränser i databas tjänsten.

## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per pris nivå och virtuella kärnor är följande:

|**Prisnivå**|**vCore (s)**| **Högsta antal anslutningar**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Generellt syfte| 2| 300|
|Generellt syfte| 4| 625|
|Generellt syfte| 8| 1250|
|Generellt syfte| 16| 2500|
|Generellt syfte| 32| 5000|
|Generellt syfte| 64| 10000|
|Minnesoptimerad| 2| 600|
|Minnesoptimerad| 4| 1250|
|Minnesoptimerad| 8| 2500|
|Minnesoptimerad| 16| 5000|
|Minnesoptimerad| 32| 10000|

När anslutningar överskrider gränsen kan följande fel meddelande visas:
> FEL 1040 (08004): för många anslutningar

## <a name="storage-engine-support"></a>Stöd för lagrings motor

### <a name="supported"></a>Stöds
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MINNESOPTIMERADE](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Stöd saknas
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARKIVATTRIBUTET](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Behörighets stöd

### <a name="unsupported"></a>Stöd saknas
- DBA-roll: många Server parametrar och inställningar kan oavsiktligt försämra serverns prestanda eller negera syre egenskaper i DBMS. För att upprätthålla tjänste integriteten och service avtalet på en produkt nivå exponerar inte den här tjänsten DBA-rollen. Standard användar kontot, som skapas när en ny databas instans skapas, gör att användaren kan utföra de flesta DDL-och DML-instruktioner i den hanterade databas instansen.
- SUPER Privilege: liknande [superbehörighet](https://mariadb.com/kb/en/library/grant/#global-privileges) är också begränsad.
- Avrundning: kräver Super-behörighet för att skapa och är begränsad. Om du importerar data med hjälp av en säkerhets kopia tar du bort de `CREATE DEFINER` kommandona manuellt eller genom att använda kommandot `--skip-definer` när du utför en mysqldump.

## <a name="data-manipulation-statement-support"></a>Stöd för data manipulations sats

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE` stöds, men `[LOCAL]`-parametern måste anges och dirigeras till en UNC-sökväg (Azure Storage monteras via SMB).

### <a name="unsupported"></a>Stöd saknas
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="scale-operations"></a>Skalnings åtgärder
- Dynamisk skalning till och från de grundläggande pris nivåerna stöds inte för närvarande.
- Det finns inte stöd för att minska lagrings storleken för servern.

### <a name="server-version-upgrades"></a>Uppgraderingar av Server version
- Automatisk migrering mellan huvud versioner av databas motorn stöds inte för närvarande.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder funktionen PITR skapas den nya servern med samma konfigurationer som den server den baseras på.
- Det finns inte stöd för att återställa en borttagen Server.

### <a name="subscription-management"></a>Prenumerationshantering
- Det finns för närvarande inte stöd för att flytta tidigare skapade servrar över prenumerationen och resurs gruppen.

### <a name="vnet-service-endpoints"></a>VNet-tjänstens slut punkter
- Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.

### <a name="storage-size"></a>Lagrings storlek
- Se [pris nivåer](concepts-pricing-tiers.md) för lagrings storleks gränser per pris nivå.

## <a name="current-known-issues"></a>Aktuella kända problem
- MariaDB Server-instans visar fel Server version när anslutningen har upprättats. För att få rätt version av Server instans motorn använder du kommandot `select version();`.

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt i varje tjänst nivå](concepts-pricing-tiers.md)
- [MariaDB databas versioner som stöds](concepts-supported-versions.md)
