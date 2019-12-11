---
title: Skalnings Server grupp – storskalig (citus)-Azure Database for PostgreSQL
description: Justera minne, disk och processor resurser i Server gruppen för att hantera ökad belastning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 5d8bbe493887c5340f0943a585eb6ff250bd3728
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977564"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skala en citus-Server grupp

Azure Database for PostgreSQL-Scale (citus) tillhandahåller självbetjänings skalning för att hantera ökad belastning. Azure Portal gör det enkelt att lägga till nya arbetsnoder och öka kapaciteten för befintliga noder.

## <a name="add-worker-nodes"></a>Lägg till arbetsnoder

Om du vill lägga till noder går du till fliken **Konfigurera** i citus-Server gruppen.  Om du drar skjutreglaget för **antal arbets noder** ändras värdet.

![Resurs skjutreglage](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klicka på knappen **Spara** om du vill att det ändrade värdet ska börja gälla.

> [!NOTE]
> När du har ökat och sparat det går det inte att minska antalet arbetsnoder med skjutreglaget.

### <a name="rebalance-shards"></a>Balansera om Shards

Om du vill dra nytta av nyligen tillagda noder måste du balansera om Distributed Table [Shards](concepts-hyperscale-distributed-data.md#shards), vilket innebär att vissa Shards flyttas från befintliga noder till de nya. Kontrol lera först att de nya arbets tagarna har slutfört etableringen. Starta sedan Shard-ombalanseringen genom att ansluta till noden kluster koordinatorer med psql och köra:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funktionen `rebalance_table_shards` balanserar om alla tabeller i den [samplacerings](concepts-hyperscale-colocation.md) gruppen i tabellen med namnet i argumentet. Därför behöver du inte anropa funktionen för varje distribuerad tabell. anropa den bara på en representativ tabell från varje samplacerings grupp.

## <a name="increase-vcores-or-storage-space"></a>Öka virtuella kärnor eller lagrings utrymme

Förutom att lägga till nya noder kan du öka funktionerna i befintliga noder. Gå till fliken **Konfigurera** i citus-Server gruppen och dra skjutreglaget för **virtuella kärnor** och **Storage** för att ändra värdena för alla arbetsnoder. Se till att klicka på **Spara** för att tillämpa ändringarna.

## <a name="next-steps"></a>Nästa steg

Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
