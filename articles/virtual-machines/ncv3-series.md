---
title: NCv3-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i NCv3-serien.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ae3604a9ea82e6e50ba4d639d36572f7b052e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302805"
---
# <a name="ncv3-series"></a>NCv3-serien

Virtuella datorer i NCv3-serien drivs av [NVIDIA Tesla V100](https://www.nvidia.com/data-center/tesla-v100/) GPU:er. Dessa GPU:er kan ge 1,5 x beräkningsprestanda för NCv2-serien. Kunderna kan dra nytta av dessa uppdaterade GPU:er för traditionella HPC-arbetsbelastningar som reservoarmodellering, DNA-sekvensering, proteinanalys, Monte Carlo-simuleringar och andra. NC24rs v3-konfigurationen ger ett nätverksgränssnitt med låg latens och hög genomströmning som är optimerat för tätt kopplade parallella datorarbetsbelastningar. Förutom GPU: er, NCv3-serien virtuella datorer drivs också av Intel Xeon E5-2690 v4 (Broadwell) processorer.

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

> [!IMPORTANT]
> För den här VM-serien är vCPU-kvoten (core) i din prenumeration ursprungligen inställd på 0 i varje region. [Begär en ökning av vCPU-kvoten](../azure-supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgänglig region](https://azure.microsoft.com/regions/services/).
>
| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Max oankopplat diskdataflöde: IOPS/MBps | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = ett V100-kort.

*RDMA-stöd

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
