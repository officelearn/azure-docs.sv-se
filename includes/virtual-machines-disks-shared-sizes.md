---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 34699ed89e79448d66343021dd624cb872d0172d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471725"
---
För tillfället kan endast premium-SSD-enheter aktivera delade diskar. Diskstorlekarna som stöder den här funktionen är P15 och större. Olika diskstorlekar kan `maxShares` ha en annan gräns, `maxShares` som du inte kan överskrida när du anger värdet.

För varje disk kan `maxShares` du definiera ett värde som representerar det maximala antalet noder som samtidigt kan dela disken. Om du till exempel planerar att ställa in ett redundanskluster för tvånoder anger `maxShares=2`du . Det maximala värdet är en övre gräns. Noder kan ansluta till eller lämna klustret (montera eller avmontera disken) `maxShares` så länge antalet noder är lägre än det angivna värdet.

> [!NOTE]
> Värdet `maxShares` kan bara anges eller redigeras när disken är fristående från alla noder.

I följande tabell visas de `maxShares` tillåtna maxvärdena för diskstorlek:

|Diskstorlekar  |maxShares gräns  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

IOPS- och bandbreddsgränserna för en `maxShares` disk påverkas inte av värdet. Till exempel är max IOPS för en P15-disk 1100 om maxShares = 1 eller maxShares > 1.