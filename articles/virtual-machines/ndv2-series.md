---
title: NDv2-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i NDv2-serien.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247290"
---
# <a name="updated-ndv2-series"></a>Uppdaterad NDv2-serien

Den virtuella datorn i NDv2-serien är ett nytt tillägg till GPU-familjen som är utformad för behoven hos de mest krävande GPU-accelererade AI-arbetsbelastningarna, maskininlärningen, simuleringen och HPC-arbetsbelastningarna.

NDv2 drivs av 8 NVIDIA Tesla V100 NVLINK-anslutna GPU:er, var och en med 32 GB GPU-minne. Varje NDv2 VM har också 40 icke-HyperThreaded Intel Xeon Platinum 8168 (Skylake) kärnor och 672 GiB av systemminne.

NDv2-instanser ger utmärkt prestanda för HPC- och AI-arbetsbelastningar som använder CUDA GPU-optimerade beräkningskärnor, och de många AI-, ML- och analysverktyg som stöder GPU-acceleration "out-of-box", som TensorFlow, Pytorch, Caffe, RAPIDS och andra Ramar.

Kritiskt är NDv2 byggd för både beräkningsmässigt intensiv skala upp (utnyttja 8 GPU per virtuell dator) och skala ut (utnyttja flera virtuella datorer som arbetar tillsammans) arbetsbelastningar. NDv2-serien stöder nu 100-Gigabit InfiniBand EDR backend-nätverk, liknande det som finns på HB-serien av HPC VM, för att möjliggöra högpresterande kluster för parallella scenarier inklusive distribuerad utbildning för AI och ML. Det här backend-nätverket stöder alla större InfiniBand-protokoll, inklusive de som används av NVIDIA:s NCCL2-bibliotek, vilket möjliggör sömlös klustring av GPU:er.

> När [du aktiverar InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) på den virtuella datorn ND40rs_v2 använder du drivrutinen 4.7-1.0.0.1 Mellanox OFED.
>
> På grund av ökat GPU-minne kräver den nya ND40rs_v2 virtuella datorn användning av [virtuella datorer med generation 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) och marknadsplatsavbildningar. 
>
> Observera: Den ND40s_v2 med 16 GB minne per GPU är inte längre tillgänglig för förhandsversion och har ersatts av den uppdaterade ND40rs_v2.

<br>

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

InfiniBand: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temp lagring (SSD): Gib | GPU | GPU-minne: Gib | Maximalt antal datadiskar | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Max nätverksbandbredd | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mbit/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste NVIDIA GPU-drivrutiner installeras.

[NVIDIA GPU Driver Extension](./extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA CUDA- eller GRID-drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg som Azure PowerShell- eller Azure Resource Manager-mallar. Allmän information om vm-tillägg finns i [Azure-tillägg och funktioner för virtuella datorer](./extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt läser du [GPU-drivrutinsinställningar i N-serien för Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
