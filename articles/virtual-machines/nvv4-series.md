---
title: NVv4-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i NVv4-serien.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273853"
---
# <a name="nvv4-series"></a>NVv4-serien 

De virtuella datorerna i NVv4-serien drivs av [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU:er och AMD EPYC 7V12-processorer(Rom). Med NVv4-serien introducerar Azure virtuella datorer med partiella GPU:er. Välj rätt storlek virtuell maskin för GPU accelererade grafikprogram och virtuella skrivbord börjar på 1/8th av en GPU med 2 GiB ram buffert till en full GPU med 16 GiB ram buffert. Virtuella NVv4-datorer stöder för närvarande endast Windows gästoperativsystem.

<br>

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> NVv4-serien virtuella datorer har AMD samtidig multithreading Technology

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella azure NVv4-serie virtuella datorer som kör Windows måste AMD GPU-drivrutiner installeras.

Information om hur du installerar AMD GPU-drivrutiner manuellt finns i [AMD GPU-drivrutinsinställningar i N-serien för Windows](./windows/n-series-amd-driver-setup.md) för operativsystem, drivrutiner, installations- och verifieringssteg.

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
