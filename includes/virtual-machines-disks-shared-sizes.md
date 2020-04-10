---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/06/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0b185d545e129c941d5df2e8ce86ee684174b666
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008372"
---
För tillfället kan endast ultradiskar och premium-SSD-enheter aktivera delade diskar. Olika diskstorlekar kan `maxShares` ha en annan gräns, `maxShares` som du inte kan överskrida när du anger värdet. För premium SSD är diskstorlekarna som stöder delning av diskarna P15 och större.

För varje disk kan `maxShares` du definiera ett värde som representerar det maximala antalet noder som samtidigt kan dela disken. Om du till exempel planerar att ställa in ett redundanskluster för tvånoder anger `maxShares=2`du . Det maximala värdet är en övre gräns. Noder kan ansluta till eller lämna klustret (montera eller avmontera disken) `maxShares` så länge antalet noder är lägre än det angivna värdet.

> [!NOTE]
> Värdet `maxShares` kan bara anges eller redigeras när disken är fristående från alla noder.

### <a name="premium-ssd-ranges"></a>Premium SSD-intervall

I följande tabell visas de `maxShares` tillåtna maxvärdena för efter premiumdiskstorlekar:

|Diskstorlekar  |maxShares gräns  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

IOPS- och bandbreddsgränserna för en `maxShares` disk påverkas inte av värdet. Till exempel är max IOPS för en P15-disk 1100 om maxShares = 1 eller maxShares > 1.

### <a name="ultra-disk-ranges"></a>Ultra diskintervall

Minimivärdet `maxShares` är 1, medan `maxShares` det maximala värdet är 5. Det finns inga storleksbegränsningar för ultradiskar, vilken `maxShares`storlek ultradisk som helst kan använda valfritt värde för , upp till och med det högsta värdet.