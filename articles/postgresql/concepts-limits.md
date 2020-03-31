---
title: Gränser - Azure-databas för PostgreSQL - Single Server
description: I den här artikeln beskrivs begränsningar i Azure Database för PostgreSQL - Single Server, till exempel antal anslutnings- och lagringsmotoralternativ.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 047e722a0e0ade60d1eb93a48e37333fffafd674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76836464"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Begränsningar i Azure-databas för PostgreSQL - Single Server
I följande avsnitt beskrivs kapacitets- och funktionsgränser i databastjänsten. Om du vill lära dig mer om resursnivåer (beräkning, minne, lagring) läser du artikeln [om prisnivåer.](concepts-pricing-tiers.md)


## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per prisnivå och virtuella kärnor visas nedan. Azure-systemet kräver fem anslutningar för att övervaka Azure-databasen för PostgreSQL-server. 

|**Prisnivå**| **vCore(er)**| **Max antal anslutningar** | **Max användaranslutningar** |
|---|---|---|---|
|Basic| 1| 55 | 50|
|Basic| 2| 105 | 100|
|Generell användning| 2| 150| 145|
|Generell användning| 4| 250| 245|
|Generell användning| 8| 480| 475|
|Generell användning| 16| 950| 945|
|Generell användning| 32| 1500| 1495|
|Generell användning| 64| 1900| 1895|
|Minnesoptimerad| 2| 300| 295|
|Minnesoptimerad| 4| 500| 495|
|Minnesoptimerad| 8| 960| 955|
|Minnesoptimerad| 16| 1900| 1895|
|Minnesoptimerad| 32| 1987| 1982|

När anslutningar överskrider gränsen kan följande felmeddelande visas:
> FATAL: tyvärr, för många kunder redan

> [!IMPORTANT]
> För bästa upplevelse rekommenderar vi att du använder en anslutningspooler som pgBouncer för att effektivt hantera anslutningar.

En PostgreSQL-anslutning, även inaktiv, kan uppta ca 10 MB minne. Det tar också tid att skapa nya anslutningar. De flesta program begär många kortlivade anslutningar, vilket förvärrar denna situation. Resultatet är färre resurser tillgängliga för din faktiska arbetsbelastning som leder till minskade prestanda. En anslutningspooler som minskar inaktiva anslutningar och återanvänder befintliga anslutningar hjälper till att undvika detta. Om du vill veta mer, besök vårt [blogginlägg](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

## <a name="functional-limitations"></a>Funktionella begränsningar
### <a name="scale-operations"></a>Skala åtgärder
- Dynamisk skalning till och från grundläggande prisnivåer stöds för närvarande inte.
- Det går inte att minska serverlagringsstorleken.

### <a name="server-version-upgrades"></a>Uppgraderingar av serverversion
- Automatiserad migrering mellan större databasmotorversioner stöds för närvarande inte. Om du vill uppgradera till nästa huvudversion tar du en [soptipp och återställer](./howto-migrate-using-dump-and-restore.md) den till en server som skapades med den nya motorversionen.

> Observera att innan PostgreSQL version 10, [PostgreSQL versionshantering politik](https://www.postgresql.org/support/versioning/) anses vara en _större version_ uppgradering vara en ökning av det första _eller_ andra numret (till exempel 9,5 till 9,6 ansågs vara en _större_ version uppgradering).
> Från och med version 10 anses endast en ändring av det första numret vara en huvudversionsuppgradering (till exempel är 10.0 till 10.1 en uppgradering _av en delversion_ och 10 till 11 är en huvudversionsuppgradering). _major_

### <a name="vnet-service-endpoints"></a>VNet-tjänstslutpunkter
- Stöd för slutpunkter för VNet-tjänst är endast för servrar med allmänt syfte och minnesoptimerade.

### <a name="restoring-a-server"></a>Återställa en server
- När du använder PITR-funktionen skapas den nya servern med samma prisnivåkonfigurationer som den server den baseras på.
- Den nya servern som skapades under en återställning har inte de brandväggsregler som fanns på den ursprungliga servern. Brandväggsregler måste ställas in separat för den nya servern.
- Det går inte att återställa en borttagen server.

### <a name="utf-8-characters-on-windows"></a>UTF-8-tecken i Windows
- I vissa fall stöds INTE UTF-8-tecken fullt ut i PostgreSQL med öppen källkod i Windows, vilket påverkar Azure Database för PostgreSQL. Se tråden på [Bug #15476 i postgresql-arkivet](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) för mer information.

## <a name="next-steps"></a>Nästa steg
- Förstå [vad som är tillgängligt på varje prisnivå](concepts-pricing-tiers.md)
- Läs mer om [postgreSQL-databasversioner som stöds](concepts-supported-versions.md)
- Granska [hur du säkerhetskopierar och återställer en server i Azure Database för PostgreSQL med Azure-portalen](howto-restore-server-portal.md)
