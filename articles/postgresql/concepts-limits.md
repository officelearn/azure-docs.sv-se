---
title: "Begränsningar i Azure-databas för PostgreSQL | Microsoft Docs"
description: "Beskriver begränsningar i Azure-databas för PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 06/01/2017
ms.openlocfilehash: 38988fc5c0dc05331ea078534cd1a05e9eca2493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Begränsningar i Azure-databas för PostgreSQL
Azure-databasen för PostgreSQL-tjänsten är tillgänglig som förhandsversion. I följande avsnitt beskrivs kapacitet och funktionella gränser i databastjänsten för.

## <a name="service-tier-maximums"></a>Tjänsten nivå maxkapacitet
Azure-databas för PostgreSQL har flera tjänstnivåer som du kan välja mellan när du skapar en server. Mer information finns i [förstå vad som är tillgängliga i varje tjänstnivå](concepts-service-tiers.md).  

Det finns ett maximalt antal anslutningar, enheter för beräkning och lagring i varje tjänstnivå under förhandsgranskningen tjänsten enligt följande: 

|                            |                   |
| :------------------------- | :---------------- |
| **Högsta antal anslutningar**        |                   |
| Grundläggande 50 beräknings-enheter     | 50-anslutningar    |
| Grundläggande 100 beräknings-enheter    | 100-anslutningar   |
| Standardenheter 100 beräkning | 200-anslutningar   |
| Standardenheter 200 beräkning | 300 anslutningar   |
| Standardenheter 400 beräkning | 400-anslutningar   |
| Standard 800 beräknings-enheter | 500-anslutningar   |
| **Max beräknings-enheter**      |                   |
| Grundläggande tjänstenivå         | 100 enheter för beräkning |
| Standardtjänstenivå      | 800 beräknings-enheter |
| **Maximalt lagringsutrymme**            |                   |
| Grundläggande tjänstenivå         | 1 TB              |
| Standardtjänstenivå      | 1 TB              |

När för många anslutningar har uppnåtts, får du följande fel:
> Oåterkalleligt fel: Det gick tyvärr redan för många klienter

## <a name="preview-functional-limitations"></a>Funktionella begränsningar i förhandsversionen
### <a name="scale-operations"></a>Skalningsåtgärder
1.  Dynamisk skalning av servrar över tjänstnivåer stöds inte för närvarande. Att växla mellan tjänstnivåerna Basic och Standard.
2.  Dynamisk på begäran ökningen av lagring i förväg skapade server stöds inte för närvarande.
3.  Minska lagringsstorleken för server stöds inte.

### <a name="server-version-upgrades"></a>Version serveruppgraderingarna
- Automatisk migrering mellan större database engine versioner stöds inte för närvarande.

### <a name="subscription-management"></a>Prenumerationshantering
- Dynamiskt flytta förskapade servrar över prenumeration och resursgrupp stöds inte för närvarande.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
1.  Är inte tillåtet att återställa till olika tjänstnivå och/eller Compute enheter och lagringsstorlek.
2.  Återställa en släppt server stöds inte.

## <a name="next-steps"></a>Nästa steg
- Förstå [vad som är tillgängligt i varje prisnivå](concepts-service-tiers.md)
- Förstå [PostgreSQL-databas-versioner som stöds](concepts-supported-versions.md)
- Granska [säkerhetskopiera och återställa en server i Azure-databas för PostgreSQL med Azure-portalen](howto-restore-server-portal.md)
