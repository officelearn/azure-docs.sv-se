---
title: Begränsningar i Azure Database för PostgreSQL
description: Den här artikeln beskriver begränsningar i Azure Database för PostgreSQL, till exempel antalet anslutning och lagringsalternativ för motorn.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/12/2018
ms.openlocfilehash: 4616ab535e7edca6d5f919824e9cadaf90886d5f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548570"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Begränsningar i Azure Database för PostgreSQL
I följande avsnitt beskrivs kapacitet och funktionella begränsningar i databastjänsten.

## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per prisnivå och virtuella kärnor är följande: 

|**Prisnivå**| **virtuella kärnor**| **Högsta antal anslutningar** |
|---|---|---|
|Basic| 1| 50 |
|Basic| 2| 100 |
|Generellt syfte| 2| 150|
|Generellt syfte| 4| 250|
|Generellt syfte| 8| 480|
|Generellt syfte| 16| 950|
|Generellt syfte| 32| 1500|
|Generellt syfte| 64| 1900|
|Minnesoptimerad| 2| 300|
|Minnesoptimerad| 4| 500|
|Minnesoptimerad| 8| 960|
|Minnesoptimerad| 16| 1900|
|Minnesoptimerad| 32| 3000|

När anslutningar överskrider gränsen, kan följande felmeddelande visas:
> Oåterkalleligt fel: tyvärr redan för många klienter

Azure-systemet kräver fem anslutningar att övervaka Azure Database for PostgreSQL-server. 

## <a name="functional-limitations"></a>Funktionella begränsningar
### <a name="scale-operations"></a>Skalningsåtgärder
- Dynamisk skalning till och från grundläggande prisnivåerna stöds för närvarande inte.
- Minska lagringsstorlek server stöds för närvarande inte.

### <a name="server-version-upgrades"></a>Server-versionsuppgraderingar
- Automatisk migrering mellan större database engine-versioner stöds för närvarande inte. Om du vill uppgradera till nästa huvudversion kan ta en [dumpa och Återställ](./howto-migrate-using-dump-and-restore.md) den till en server som har skapats med ny version.

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.

### <a name="restoring-a-server"></a>När du återställer en server
- När du använder funktionen PITR, skapas den nya servern med samma prisnivå nivå diskkonfigurationer som den är baserad på-servern.
- Den nya servern som skapades under en återställning har inte de brandväggsregler som fanns på den ursprungliga servern. Brandväggsregler måste konfigureras separat för den här nya servern.
- När du återställer en borttagen server stöds inte.

### <a name="utf-8-characters-on-windows"></a>UTF-8 tecken på Windows
- I vissa situationer UTF-8 tecken stöds inte helt i öppen PostgreSQL på Windows, vilket påverkar Azure Database för PostgreSQL. Finns tråden om [bugg #15476 i arkivet postgresql](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) för mer information.

## <a name="next-steps"></a>Nästa steg
- Förstå [vad som finns i varje prisnivå](concepts-pricing-tiers.md)
- Lär dig mer om [PostgreSQL-databas-versioner som stöds](concepts-supported-versions.md)
- Granska [hur du säkerhetskopierar och återställer en server i Azure Database for PostgreSQL med Azure-portalen](howto-restore-server-portal.md)
