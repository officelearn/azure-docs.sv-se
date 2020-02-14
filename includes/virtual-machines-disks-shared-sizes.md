---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9000cab026ec08bd78d8a8f334c8967a8a747c95
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202299"
---
För närvarande kan endast Premium-SSD aktivera delade diskar. Disk storlekarna som har stöd för den här funktionen är p15 och större. Olika disk storlekar kan ha olika `maxShares` gränser, som du inte kan överskrida när du anger värdet för `maxShares`.

För varje disk kan du definiera ett `maxShares`-värde som representerar det maximala antalet noder som samtidigt kan dela disken. Om du till exempel planerar att konfigurera ett redundanskluster med två noder kan du ange `maxShares=2`. Det maximala värdet är ett övre gräns värde. Noder kan ansluta till eller lämna klustret (montera eller demontera disken) så länge antalet noder är lägre än det angivna `maxShares` svärdet.

> [!NOTE]
> `maxShares`-värdet kan bara ställas in eller redige ras när disken kopplas från alla noder.

I följande tabell visas de tillåtna Max värdena för `maxShares` efter disk storlek:

|Disk storlekar  |maxShares-gräns  |
|---------|---------|
|P15, P20     |2         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Begränsningarna för IOPS och bandbredd för en disk påverkas inte av `maxShares` svärdet. Till exempel är Max IOPS för en p15-disk 1100 om maxShares = 1 eller maxShares > 1.