---
title: Begränsningar – Azure Database for MariaDB
description: I den här artikeln beskrivs begränsningar i Azure Database for MariaDB, till exempel antalet anslutnings-och lagrings motor alternativ.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: df44cbefaec943a2df483f4804650b939c796cb5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191153"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Begränsningar i Azure Database for MariaDB
I följande avsnitt beskrivs kapacitet, stödet för lagring, privilegier support, manipulering av instruktionen stöd och funktionella begränsningar i databastjänsten.

## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per prisnivå och virtuella kärnor är följande:

|**Prisnivå**|**vCore (s)**| **Högsta antal anslutningar**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Generell användning| 2| 600|
|Generell användning| 4| 1250|
|Generell användning| 8| 2500|
|Generell användning| 16| 5000|
|Generell användning| 32| 10000|
|Generell användning| 64| 20000|
|Minnesoptimerad| 2| 800|
|Minnesoptimerad| 4| 2500|
|Minnesoptimerad| 8| 5000|
|Minnesoptimerad| 16| 10000|
|Minnesoptimerad| 32| 20000|

När anslutningar överskrider gränsen, kan följande felmeddelande visas:
> FEL 1040 (08004): För många anslutningar

> [!IMPORTANT]
> För bästa möjliga upplevelse rekommenderar vi att du använder en anslutningspool som ProxySQL för att effektivt hantera anslutningar.

Att skapa nya klient anslutningar till MariaDB tar tid och när de har upprättats, tar dessa anslutningar med databas resurser, även när de är inaktiva. De flesta program begär många anslutningar för kort period, vilket utvärderar den här situationen. Resultatet är färre resurser som är tillgängliga för den faktiska arbets belastningen, vilket leder till försämrade prestanda. En anslutningspool som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Mer information om hur du konfigurerar ProxySQL finns i vårt [blogg inlägg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

## <a name="storage-engine-support"></a>Stödet för lagring

### <a name="supported"></a>Stöds
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MINNESOPTIMERADE](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Stöd saknas
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARKIVATTRIBUTET](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Stöd för behörighet

### <a name="unsupported"></a>Stöd saknas
- DBA-roll: många serverparametrar och inställningar kan oavsiktligt försämra serverprestanda eller negera ACID-egenskaper för DBMS. Därför för att upprätthålla integriteten för tjänsten och SLA på en produktnivå, exponerar den här tjänsten inte DBA-rollen. Standardanvändarkontot som skapas när en ny databasinstans har skapats, kan användaren utföra de flesta av DDL och DML-instruktioner i hanterade databasinstansen.
- SUPER Privilege: liknande [superbehörighet](https://mariadb.com/kb/en/library/grant/#global-privileges) är också begränsad.
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
- Automatisk migrering mellan större database engine-versioner stöds för närvarande inte.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- När du använder funktionen PITR, skapas den nya servern med samma konfiguration som den är baserad på-servern.
- När du återställer en borttagen server stöds inte.

### <a name="subscription-management"></a>Prenumerationshantering
- Det finns för närvarande inte stöd för att flytta tidigare skapade servrar över prenumerationen och resurs gruppen.

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

### <a name="storage-size"></a>Lagrings storlek
- Se [pris nivåer](concepts-pricing-tiers.md) för lagrings storleks gränser per pris nivå.

## <a name="current-known-issues"></a>Aktuella kända problem
- MariaDB Server-instans visar fel Server version när anslutningen har upprättats. För att få rätt version av Server instans motorn använder du kommandot `select version();`.

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt i varje tjänst nivå](concepts-pricing-tiers.md)
- [MariaDB databas versioner som stöds](concepts-supported-versions.md)
