---
title: "Begränsningar i Azure-databas för MySQL | Microsoft Docs"
description: "Beskriver begränsningar i förhandsversionen i Azure-databas för MySQL."
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 12/09/2017
ms.openlocfilehash: e16982e4e57ba9f2f11e9ee59f88f24b3fe3fe3f
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="limitations-in-azure-database-for-mysql"></a>Begränsningar i Azure-databas för MySQL
Azure-databasen för MySQL-tjänsten är tillgänglig som förhandsversion. I följande avsnitt beskrivs kapacitet, stödet för lagring, privilegium support, stöd för data manipulation instruktionen och funktionella gränser i databastjänsten för. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) avser MySQL database engine.

## <a name="service-tier-maximums"></a>Tjänsten nivå maxkapacitet
Azure MySQL-databas har flera tjänstnivåer för att välja mellan när du skapar en server. Mer information finns i [förstå vad som är tillgängliga i varje tjänstnivå](concepts-service-tiers.md).  

Det finns ett maximalt antal anslutningar, Compute enheter och lagring i varje tjänstnivå under förhandsgranskningen gör på följande sätt: 

|                            |                   |
| :------------------------- | :---------------- |
| **Högsta antal anslutningar**        |                   |
| Grundläggande 50 beräknings-enheter     | 50-anslutningar    |
| Grundläggande 100 beräknings-enheter    | 100 anslutningar   |
| Standardenheter 100 beräkning | 200-anslutningar   |
| Standardenheter 200 beräkning | 400-anslutningar   |
| Standardenheter 400 beräkning | 800 anslutningar   |
| Standard 800 beräknings-enheter | 1600 anslutningar  |
| **Max beräknings-enheter**      |                   |
| Grundläggande tjänstenivå         | 100 enheter för beräkning |
| Standardtjänstenivå      | 800 beräknings-enheter |
| **Maximalt lagringsutrymme**            |                   |
| Grundläggande tjänstenivå         | 1 TB              |
| Standardtjänstenivå      | 1 TB              |

När för många anslutningar har uppnåtts, får du följande fel:
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
- DBA rollen många sever parametrar och inställningar av misstag kan försämra serverprestanda eller negera ACID DBMS-egenskaper. Därför för att upprätthålla våra service integritet och SLA på en produktnivå exponerar vi inte rollen DBA till kunder. Standard-användarkonto som skapas när en ny databasinstans har skapats, kan kunder utföra de flesta av DDL och DML-instruktioner i hanterade databasinstansen. 
- SUPER privilegium på samma sätt [SUPER privilegium](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) är också begränsad.

## <a name="data-manipulation-statement-support"></a>Stöd för data manipulation instruktionen

### <a name="supported"></a>Stöds
- Läs in DATA INFIL - stöds, men det måste ange parametern [lokala] som är riktad till en UNC-sökväg (Azure lagringsutrymmet via XSMB).

### <a name="unsupported"></a>Stöds inte
- VÄLJ... I UTFIL

## <a name="preview-functional-limitations"></a>Funktionella begränsningar i förhandsversionen

### <a name="scale-operations"></a>Skalningsåtgärder
- Dynamisk skalning av servrar över tjänstnivåer stöds inte för närvarande. Att växla mellan tjänstnivåerna Basic och Standard.
- Dynamisk på begäran ökningen av lagring i förväg skapade server stöds inte för närvarande.
- Minska lagringsstorleken för server stöds inte.

### <a name="server-version-upgrades"></a>Version serveruppgraderingarna
- Automatisk migrering mellan större database engine versioner stöds inte för närvarande.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- Är inte tillåtet att återställa till olika tjänstnivå och/eller Compute enheter och lagringsstorlek.
- Återställa en borttagen server stöds inte.

## <a name="functional-limitations"></a>Funktionella begränsningar

### <a name="subscription-management"></a>Prenumerationshantering
- Dynamiskt flytta förskapade servrar över prenumeration och resursgrupp stöds inte för närvarande.

## <a name="current-known-issues"></a>Aktuella kända problem:
- MySQL-serverinstans visar fel serverversionen efter anslutningen har upprättats. För att få rätt server-instans versionshantering, använder du väljer version(); kommandot MySQL i Kommandotolken.

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt på respektive tjänstnivå](concepts-service-tiers.md)
- [Versioner som stöds MySQL-databas](concepts-supported-versions.md)
