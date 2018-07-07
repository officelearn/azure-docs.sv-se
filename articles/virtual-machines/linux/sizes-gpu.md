---
title: Azure Linux VM-storlekar - GPU | Microsoft Docs
description: Visar olika GPU optimerad storlekar för Linux-datorer i Azure. Visar information om hur många virtuella processorer, diskar och nätverkskort samt lagring dataflöde och nätverket bandbredd för storlekar i den här serien.
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
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 435dc3a4815d9fe554ccba57a49ed9a772b53529
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904087"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-optimerad storlekar för virtuella datorer

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Distributioner och drivrutiner som stöds

Om du vill dra nytta av GPU-funktioner för Azure N-serien virtuella datorer som kör Linux, måste NVIDIA GPU-drivrutiner installeras. Den [NVIDIA GPU-drivrutinen tillägget](../extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA CUDA- eller NÄTVERKSBASERADE drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg som Azure CLI eller Azure Resource Manager-mallar. Se den [NVIDIA GPU-drivrutinen tillägget dokumentation](../extensions/hpccompute-gpu-linux.md) för distributioner som stöds och distributionssteg. Allmän information om VM-tillägg finns i [Azure virtuella datorer, tillägg och funktioner](../extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [drivrutinsinstallationer för N-serien GPU för Linux](n-series-driver-setup.md) distributioner som stöds, drivrutiner och anvisningar för installation och verifiering.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Du bör inte installera X server eller andra system som använder den `Nouveau` drivrutinen på Ubuntu Nätverksstyrenhetens virtuella datorer. Innan du installerar NVIDIA GPU-drivrutiner, måste du inaktivera den `Nouveau` drivrutinen.  

## <a name="other-sizes"></a>Andra storlekar
- [Generellt syfte](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare versioner](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur [Azure-beräkningsenheter (ACU)](acu.md) kan hjälpa dig att jämföra prestanda för databearbetning mellan Azure SKU: er.