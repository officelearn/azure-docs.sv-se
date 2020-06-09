---
title: Begränsningar – Azure Database for MySQL
description: I den här artikeln beskrivs begränsningar i Azure Database for MySQL, till exempel antalet anslutnings-och lagrings motor alternativ.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/5/2020
ms.openlocfilehash: 8e2b6f43dcbf38e0e412b817937721d44a65308b
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559212"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Begränsningar i Azure Database for MySQL
I följande avsnitt beskrivs kapacitet, stöd för lagrings motor, stöd för stöd för data manipulation och funktionella gränser i databas tjänsten. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) som gäller för databas motorn MySQL.

## <a name="storage-engine-support"></a>Stöd för lagrings motor

### <a name="supported"></a>Stöds
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MINNESOPTIMERADE](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Stöd saknas
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARKIVATTRIBUTET](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [EXTERNT](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Behörighets stöd

### <a name="unsupported"></a>Stöd saknas
- DBA-roll: många Server parametrar och inställningar kan oavsiktligt försämra serverns prestanda eller negera syre egenskaper i DBMS. För att upprätthålla tjänste integriteten och service avtalet på en produkt nivå exponerar inte den här tjänsten DBA-rollen. Standard användar kontot, som skapas när en ny databas instans skapas, gör att användaren kan utföra de flesta DDL-och DML-instruktioner i den hanterade databas instansen. 
- SUPER Privilege: liknande [superbehörighet](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) är också begränsad.
- Avrundning: kräver Super-behörighet för att skapa och är begränsad. Om du importerar data med hjälp av en säkerhets kopia tar du bort `CREATE DEFINER` kommandona manuellt eller genom att använda `--skip-definer` kommandot när du utför en mysqldump.

## <a name="server-parameters"></a>Serverparametrar
Azure Database for MySQL stöder justering av värdet för Server parametrar. Det minsta och högsta värdet för vissa parametrar bestäms av pris nivån och virtuella kärnor för servern. Se [Server parametrar](./concepts-server-parameters.md) för mer information om dessa gränser. 

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
- Automatisk migrering mellan huvud versioner av databas motorn stöds inte för närvarande. Om du vill uppgradera till nästa högre version kan du göra en [dumpning och återställa](./concepts-migrate-dump-restore.md) den till en server som har skapats med den nya motor versionen.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder funktionen PITR skapas den nya servern med samma konfigurationer som den server den baseras på.
- Det finns inte stöd för att återställa en borttagen Server.

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.

### <a name="storage-size"></a>Lagrings storlek
- Se [pris nivåer](concepts-pricing-tiers.md) för lagrings storleks gränser per pris nivå.

## <a name="current-known-issues"></a>Aktuella kända problem
- MySQL Server-instansen visar fel Server version när anslutningen har upprättats. Använd kommandot för att få rätt version av Server instans motorn `select version();` .

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt i varje tjänst nivå](concepts-pricing-tiers.md)
- [MySQL-databas versioner som stöds](concepts-supported-versions.md)
