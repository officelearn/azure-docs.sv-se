---
title: NVv3-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i NVv3-serien.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: d74b00a4bade956d3a511a47b0a6b0011b9fb212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267416"
---
# <a name="nvv3-series"></a>NVv3-serien

De virtuella datorerna i NVv3-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU:er och NVIDIA GRID-teknik med Intel E5-2690 v4 (Broadwell) processorer och Intel Hyper-Threading Technology. Dessa virtuella datorer är inriktade på GPU-accelererade grafikprogram och virtuella skrivbord där kunderna vill visualisera sina data, simulera resultat för att visa, arbeta med CAD eller återge och strömma innehåll. Dessutom kan dessa virtuella datorer köra arbetsbelastningar med enkel precision, till exempel kodning och rendering. NVv3 virtuella datorer stöder Premium Storage och levereras med dubbelt systemminne (RAM) jämfört med sin föregångare NV-serien.  

Varje GPU i NVv3-instanser levereras med en GRID-licens. Den här licensen ger dig flexibiliteten att använda en NV-instans som en virtuell arbetsstation för en enskild användare, eller så kan 25 samtidiga användare ansluta till den virtuella datorn för ett virtuellt programscenario.

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Max oankopplat diskdataflöde: IOPS/MBps | Maximalt antal nätverkskort | Virtuella arbetsstationer | Virtuella program |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = ett halvt M60-kort.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste NVIDIA GPU-drivrutiner installeras.

[NVIDIA GPU Driver Extension](./extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA CUDA- eller GRID-drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg som Azure PowerShell- eller Azure Resource Manager-mallar. Se [dokumentationen](./extensions/hpccompute-gpu-windows.md) för NVIDIA GPU Driver Extension för operativsystem och driftsättningssteg som stöds. Allmän information om vm-tillägg finns i [Azure-tillägg och funktioner för virtuella datorer](./extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt läser du [GPU-drivrutinsinställningarna i N-serien för](./windows/n-series-driver-setup.md) [GPU-drivrutinsinställningar i](./linux/n-series-driver-setup.md) Windows eller N-serien för Linux för operativsystem, drivrutiner, installations- och verifieringssteg.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
