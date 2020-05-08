---
title: Skalnings Server grupp – storskalig (citus)-Azure Database for PostgreSQL
description: Justera minne, disk och processor resurser i Server gruppen för att hantera ökad belastning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: f8e8e1672f754e843a3bd1c75d496599d31e5f11
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583994"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skala en citus-Server grupp

Azure Database for PostgreSQL-Scale (citus) tillhandahåller självbetjänings skalning för att hantera ökad belastning. Azure Portal gör det enkelt att lägga till nya arbetsnoder och öka virtuella kärnor för befintliga noder.

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

`rebalance_table_shards` Funktionen balanserar alla tabeller i den [samplacerings](concepts-hyperscale-colocation.md) gruppen i tabellen med namnet i argumentet. Därför behöver du inte anropa funktionen för varje distribuerad tabell. anropa den bara på en representativ tabell från varje samplacerings grupp.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Öka eller minska virtuella kärnor på noder

> [!NOTE]
> Den här funktionen finns för närvarande som en förhandsversion. [Kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill begära en ändring i virtuella kärnor för noder i din server grupp.

Förutom att lägga till nya noder kan du öka funktionerna i befintliga noder. Det kan vara bra att justera beräknings kapaciteten för prestanda experiment samt kortsiktiga eller långsiktiga ändringar av trafik kraven.

Om du vill ändra virtuella kärnor för alla arbetsnoder justerar du skjutreglaget **virtuella kärnor** under **konfiguration (per nod)**. Koordinator nodens virtuella kärnor kan justeras oberoende av varandra. Klicka på länken **ändra konfiguration** under **noden koordinator**. En dialog ruta visas med skjutreglage för virtuella kärnor och lagrings kapaciteten för koordinatorn. Ändra skjutreglagena efter behov och välj **OK**.

## <a name="next-steps"></a>Nästa steg

Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
