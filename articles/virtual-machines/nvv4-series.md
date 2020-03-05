---
title: NVv4-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i NVv4-serien.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273853"
---
# <a name="nvv4-series"></a>NVv4-serien 

De virtuella datorerna i NVv4-serien drivs av [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU-processorer och AMD EPYC 7V12 (Rom). Med NVv4-serien Azure introducerar virtuella datorer med delar av GPU: er. Välj den högra virtuella datorn för GPU-accelererade grafik program och virtuella skriv bord som börjar på 1/8 av en GPU med 2 GiB-bildruta-buffert till en fullständig GPU med 16 GiB-bildruteproportioner. Virtuella NVv4-datorer stöder för närvarande endast Windows gäst operativ system.

<br>

Premium Storage: stöds

Premium Storage caching: stöds

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> virtuella datorer i NVv4-serien använder AMD samtidiga multitråds teknik

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer med Azure NVv4-serien som kör Windows måste du installera AMD GPU-drivrutinerna.

Om du vill installera AMD GPU-drivrutiner manuellt, se [N-seriens installation av AMD GPU-drivrutiner för Windows](./windows/n-series-amd-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
