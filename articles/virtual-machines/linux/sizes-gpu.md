---
title: VIRTUELLA Azure Linux-storlekar – accelererad beräkning
description: Visar en lista över de olika GPU-optimerade storlekar som är tillgängliga för virtuella Linux-datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 983e85dc0ebefb27804c0f3a794360def6050ba9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034912"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-optimerade storlekar på virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien som kör Linux måste NVIDIA GPU-drivrutiner vara installerade. [NVidia GPU-drivrutinen](../extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure CLI eller Azure Resource Manager mallar. Se [dokumentationen för NVIDIA GPU-drivrutin](../extensions/hpccompute-gpu-linux.md) för distributioner som stöds och distributions steg. Allmän information om VM-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](../extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [N-seriens installation av GPU-drivrutiner för Linux](n-series-driver-setup.md) för distributioner, driv rutiner och installations-och verifierings steg som stöds.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Du bör inte installera X-Server eller andra system som använder `Nouveau` driv rutinen på Ubuntu NC-VM: ar. Innan du installerar NVIDIA GPU-drivrutiner måste du inaktivera `Nouveau` driv rutinen.  

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.