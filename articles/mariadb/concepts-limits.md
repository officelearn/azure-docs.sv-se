---
title: Begränsningar – Azure Database for MariaDB
description: I den här artikeln beskrivs begränsningar i Azure Database for MariaDB, till exempel antalet anslutnings-och lagrings motor alternativ.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/10/2020
ms.openlocfilehash: d217c579c5f2cb5c3b6b984c0f2e0c57f17df2c9
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669774"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Begränsningar i Azure Database for MariaDB
I följande avsnitt beskrivs kapacitet, stöd för lagrings motor, stöd för stöd för data manipulation och funktionella gränser i databas tjänsten.

## <a name="server-parameters"></a>Serverparametrar

> [!NOTE]
> Om du letar efter minsta/högsta-värden för Server parametrar som `max_connections` och `innodb_buffer_pool_size` har den här informationen flyttats till **[Server parametrar](./concepts-server-parameters.md)** -artikeln.

Azure Database for MySQL stöder justering av värdet för Server parametrar. Det minsta och högsta värdet för vissa parametrar (t. ex. `max_connections`, `join_buffer_size` , `query_cache_size` ) bestäms av pris nivån och virtuella kärnor för servern. Se [Server parametrar](./concepts-server-parameters.md) för mer information om dessa gränser. 

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
- Avrundning: kräver Super-behörighet för att skapa och är begränsad. Om du importerar data med hjälp av en säkerhets kopia tar du bort `CREATE DEFINER` kommandona manuellt eller genom att använda `--skip-definer` kommandot när du utför en mysqldump.

## <a name="data-manipulation-statement-support"></a>Stöd för data manipulations sats

### <a name="supported"></a>Stöds
- `LOAD DATA INFILE`stöds, men `[LOCAL]` parametern måste anges och dirigeras till en UNC-sökväg (Azure Storage monteras via SMB).

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

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.

### <a name="storage-size"></a>Lagrings storlek
- Se [pris nivåer](concepts-pricing-tiers.md) för lagrings storleks gränser per pris nivå.

## <a name="current-known-issues"></a>Aktuella kända problem
- MariaDB Server-instans visar fel Server version när anslutningen har upprättats. Använd kommandot för att få rätt version av Server instans motorn `select version();` .

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt i varje tjänst nivå](concepts-pricing-tiers.md)
- [MariaDB databas versioner som stöds](concepts-supported-versions.md)
