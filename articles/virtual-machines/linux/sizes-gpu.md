---
title: Azure Linux VM-storlekar - GPU | Microsoft Docs
description: Visar olika GPU optimerad storlekar som finns tillgängliga för Linux virtuella datorer i Azure. Visar information om antalet vCPUs, diskar och nätverkskort samt lagring genomflöde och nätverket bandbredd för storlekar i den här serien.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: 5b856ec14febefc96e77d3c131b746e597a3aa5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653628"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU optimerade storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Drivrutinen installation och verifiering anvisningar finns [N-serien drivrutinsinstallation för Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Du bör inte installera X server eller andra system som använder den `Nouveau` drivrutinen på Ubuntu NC virtuella datorer. Innan du installerar NVIDIA GPU drivrutiner, måste du inaktivera den `Nouveau` drivrutin.  

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare versioner](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur [Azure compute-enheter (ACU)](acu.md) kan hjälpa dig att jämföra beräkning prestanda över Azure SKU: er.