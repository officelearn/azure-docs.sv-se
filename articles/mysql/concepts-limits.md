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
ms.date: 10/26/2017
ms.openlocfilehash: b3fba38cacf5b5abcdea7f0def8c1d39e653f0a8
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Begränsningar i Azure-databas för MySQL (förhandsgranskning)
Azure-databasen för MySQL-tjänsten är tillgänglig som förhandsversion. I följande avsnitt beskrivs kapacitet och funktionella gränser i databastjänsten för. Se även [allmänna begränsningar](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) avser MySQL database engine.

## <a name="service-tier-maximums"></a>Tjänsten nivå maxkapacitet
Azure MySQL-databas har flera tjänstnivåer för att välja mellan när du skapar en server. Mer information finns i [förstå vad som är tillgängliga i varje tjänstnivå](concepts-service-tiers.md).  

Det finns ett maximalt antal anslutningar, Compute enheter och lagring i varje tjänstnivå under förhandsgranskningen gör på följande sätt: 

|                            |                   |
| :------------------------- | :---------------- |
| **Högsta antal anslutningar**        |                   |
| Grundläggande 50 beräknings-enheter     | 50-anslutningar    |
| Grundläggande 100 beräknings-enheter    | 100-anslutningar   |
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

## <a name="preview-functional-limitations"></a>Funktionella begränsningar i förhandsversionen

### <a name="scale-operations"></a>Skalningsåtgärder
- Dynamisk skalning av servrar över tjänstnivåer stöds inte för närvarande. Att växla mellan tjänstnivåerna Basic och Standard.
- Dynamisk på begäran ökningen av lagring i förväg skapade server stöds inte för närvarande.
- Minska lagringsstorleken för server stöds inte.

### <a name="server-version-upgrades"></a>Version serveruppgraderingarna
- Automatisk migrering mellan större database engine versioner stöds inte för närvarande.

### <a name="subscription-management"></a>Prenumerationshantering
- Dynamiskt flytta förskapade servrar över prenumeration och resursgrupp stöds inte för närvarande.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
- Är inte tillåtet att återställa till olika tjänstnivå och/eller Compute enheter och lagringsstorlek.
- Återställa en släppt server stöds inte.

## <a name="next-steps"></a>Nästa steg
- [Vad som är tillgängligt på respektive tjänstnivå](concepts-service-tiers.md)
- [Versioner som stöds MySQL-databas](concepts-supported-versions.md)
