---
title: "Begränsningar i Azure-databas för MySQL"
description: "Den här artikeln beskriver begränsningar i Azure-databas för MySQL, till exempel antal anslutning och lagringsalternativ för motorn."
services: mysql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 85e57170c1cbd977d2de6e7e614916333c79e047
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Begränsningar i Azure-databas för MySQL
Azure-databasen för MySQL-tjänsten är tillgänglig som förhandsversion. I följande avsnitt beskrivs kapacitet, stödet för lagring, privilegium support, stöd för data manipulation instruktionen och funktionella gränser i databastjänsten för. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) avser MySQL database engine.

## <a name="service-tier-maximums"></a>Tjänsten nivå maxkapacitet
Azure MySQL-databas har flera tjänstnivåer för att välja mellan när du skapar en server. Mer information finns i [Azure-databas för MySQL prisnivåer](concepts-pricing-tiers.md).  

Det finns ett maximalt antal anslutningar, Compute enheter och lagring i varje tjänstnivå under förhandsgranskningen gör på följande sätt: 

|**Prisnivå**| **Compute-generering**|**vCore(s)**| **Högsta antal anslutningar**|
|---|---|---|---|
|Basic| Gen 4| 1| 50|
|Basic| Gen 4| 2| 100|
|Basic| Gen 5| 1| 50|
|Basic| Gen 5| 2| 100|
|Generellt syfte| Gen 4| 2| 200|
|Generellt syfte| Gen 4| 4| 400|
|Generellt syfte| Gen 4| 8| 800|
|Generellt syfte| Gen 4| 16| 1600|
|Generellt syfte| Gen 4| 32| 3200|
|Generellt syfte| Gen 5| 2| 200|
|Generellt syfte| Gen 5| 4| 400|
|Generellt syfte| Gen 5| 8| 800|
|Generellt syfte| Gen 5| 16| 1600|
|Generellt syfte| Gen 5| 32| 3200|
|Minnesoptimerad| Gen 5| 2| 600|
|Minnesoptimerad| Gen 5| 4| 1250|
|Minnesoptimerad| Gen 5| 8| 2500|
|Minnesoptimerad| Gen 5| 16| 5000|
|Minnesoptimerad| Gen 5| 32| 10000| 

När för många anslutningar har uppnåtts, får du följande fel:
> FEL 1040 (08004): För många anslutningar

## <a name="storage-engine-support"></a>Stödet för lagring

### <a name="supported"></a>Stöds
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MINNE](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Stöds inte
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [SVARTLISTADE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERERAD](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Stöd för behörighet

### <a name="unsupported"></a>Stöds inte
- DBA rollen: många parametrar för server och inställningar kan oavsiktligt försämra serverprestanda eller negera ACID DBMS-egenskaper. Därför för att upprätthålla tjänsten integritet och SLA på en produktnivå, visar tjänsten inte gränssnittshändelsen DBA-rollen. Standard-användarkonto som skapas när en ny databasinstans har skapats, kan användaren utföra de flesta av DDL och DML-instruktioner i hanterade databasinstansen. 
- SUPER privilegium: på samma sätt [SUPER privilegium](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) är också begränsad.

## <a name="data-manipulation-statement-support"></a>Stöd för data manipulation instruktionen

### <a name="supported"></a>Stöds
- Läs in DATA INFIL - stöds, men det måste ange parametern [lokala] som är riktad till en UNC-sökväg (Azure lagringsutrymmet via XSMB).

### <a name="unsupported"></a>Stöds inte
- VÄLJ... I UTFIL

## <a name="preview-functional-limitations"></a>Funktionella begränsningar i förhandsversionen

### <a name="scale-operations"></a>Skalningsåtgärder
- Dynamisk skalning av servrar över prisnivåer stöds inte för närvarande. Att växla mellan Basic generella och Minnesoptimerade prisnivåer.
- Minska lagringsstorleken för server stöds inte.

### <a name="server-version-upgrades"></a>Version serveruppgraderingarna
- Automatisk migrering mellan större database engine versioner stöds inte för närvarande.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- Är inte tillåtet att återställa till olika tjänstnivå och/eller Compute enheter och lagringsstorlek.
- Återställa en borttagen server stöds inte.

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="subscription-management"></a>Prenumerationshantering
- Dynamiskt flytta förskapade servrar över prenumeration och resursgrupp stöds inte för närvarande.

## <a name="current-known-issues"></a>Aktuella kända problem
- MySQL-serverinstans visar fel serverversionen efter anslutningen har upprättats. För att få rätt server-instans versionshantering, använder du väljer version(); kommandot MySQL i Kommandotolken.

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt på respektive tjänstnivå](concepts-pricing-tiers.md)
- [Versioner som stöds MySQL-databas](concepts-supported-versions.md)
