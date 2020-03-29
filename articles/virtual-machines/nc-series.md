---
title: NC-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i NC-serien.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: eab7dfe79aa5cdf234c8bc9472387214f7df3563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164533"
---
# <a name="nc-series"></a>NC-serien

Nc-serien virtuella datorer drivs av [NVIDIA Tesla K80-kortet](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) och Intel Xeon E5-2690 v3 (Haswell) processor. Användare kan kritan genom data snabbare genom att utnyttja CUDA för energiprospektering applikationer, krasch simuleringar, ray spåras rendering, djupinlärning och mycket mer. NC24r-konfigurationen ger ett nätverksgränssnitt med låg latens och hög genomströmning som är optimerat för tätt kopplade parallella datorarbetsbelastningar.

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = ett halvt K80-kort.

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
