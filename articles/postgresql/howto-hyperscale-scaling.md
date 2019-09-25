---
title: Skala en Azure Database for PostgreSQL-storskalig (citus)-Server grupp
description: Justera minne, disk och processor resurser i Server gruppen för att hantera ökad belastning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262497"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skala en citus-Server grupp

Azure Database for PostgreSQL-Scale (citus) tillhandahåller självbetjänings skalning för att hantera ökad belastning. Azure Portal gör det enkelt att lägga till nya arbetsnoder.

Det gör du genom att gå till fliken **Konfigurera** i citus-Server gruppen.
Ändra värdet genom att dra skjutreglaget för **antal arbets noder** .

![Resurs skjutreglage](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klicka på Spara om du vill att det ändrade värdet ska börja gälla.

> [!NOTE]
> När du har ökat och sparat det går det inte att minska antalet arbetsnoder med skjutreglaget.
>
> Dessutom går det inte att justera virtuella kärnor och Storage än för koordinatorn eller arbets tagarna med det här användar gränssnittet. Öppna ett support ärende om du behöver skalnings beräkning för koordinatorn eller arbetsnoderna.

Om du vill dra nytta av nyligen tillagda noder måste du balansera om Distributed Table [Shards](concepts-hyperscale-distributed-data.md#shards), vilket innebär att vissa Shards flyttas från befintliga noder till de nya. Starta Shard-ombalanseringen genom att ansluta till noden kluster koordinatorer med psql och köra:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funktionen balanserar alla tabeller i den [samplacerings](concepts-hyperscale-colocation.md) gruppen i tabellen med namnet i argumentet. `rebalance_table_shards` Därför behöver du inte anropa funktionen för varje distribuerad tabell. anropa den bara på en representativ tabell från varje samplacerings grupp.

## <a name="next-steps"></a>Nästa steg

Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
