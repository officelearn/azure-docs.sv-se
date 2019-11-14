---
title: Azure Windows VM-storlekar – GPU
description: Visar en lista över de olika GPU-optimerade storlekar som är tillgängliga för virtuella Windows-datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 5b2da3076f3970d2f11301b3ea412ce52c257111
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075356"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-optimerade storlekar på virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien som kör Windows måste NVIDIA GPU-drivrutiner vara installerade. [NVidia GPU-drivrutinen](../extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Mer information om vilka operativ system och distributions steg som stöds finns i [dokumentationen för NVIDIA GPU-drivrutins tillägget](../extensions/hpccompute-gpu-windows.md) . Allmän information om VM-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](../extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [N-seriens installation av GPU-drivrutiner för Windows](n-series-driver-setup.md) för operativ system, driv rutiner och installations-och verifierings steg som stöds.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.

