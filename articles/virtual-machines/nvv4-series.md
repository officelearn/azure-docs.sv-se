---
title: NVv4-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i NVv4-serien.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3b1deceb8d5c6e2a22254b2719449e5adb069def
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565405"
---
# <a name="nvv4-series-preview"></a>NVv4-serien (för hands version)

De virtuella datorerna i NVv4-serien drivs av [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU-processorer och AMD EPYC 7V12 (Rom). Med NVv4-serien Azure introducerar virtuella datorer med delar av GPU: er. Välj den högra virtuella datorn för GPU-accelererade grafik program och virtuella skriv bord som börjar på 1/8 av en GPU med 2 GiB-bildruta-buffert till en fullständig GPU med 16 GiB-bildruteproportioner. Virtuella NVv4-datorer stöder för närvarande endast Windows gäst operativ system.

[Registrera dig och få till gång till de här datorerna under för hands versionen](https://aka.ms/nvv4signup).
<br>

Premium Storage: stöds

Premium Storage caching: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> virtuella datorer i NVv4-serien använder AMD samtidiga multitråds teknik

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien som kör Windows, måste NVIDIA-eller AMD GPU-drivrutinerna vara installerade.

[NVidia GPU-drivrutinen](/extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator med Windows N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Mer information om vilka operativ system och distributions steg som stöds finns i [dokumentationen för NVIDIA GPU-drivrutins tillägget](/extensions/hpccompute-gpu-windows.md) . Allmän information om VM-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](/extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [N-seriens installation av GPU-drivrutiner för Windows](/windows/n-series-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

Om du vill installera AMD GPU-drivrutiner manuellt, se [N-seriens installation av AMD GPU-drivrutiner för Windows](/windows/n-series-amd-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.