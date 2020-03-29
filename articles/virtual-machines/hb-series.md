---
title: HB-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i HB-serien.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164839"
---
# <a name="hb-series"></a>HB-serien

HB-serien virtuella datorer är optimerade för program som drivs av minnesbandbredd, till exempel vätskedynamik, explicit finit elementanalys och vädermodellering. HB virtuella datorer har 60 AMD EPYC 7551 processorkärnor, 4 GB RAM per CPU-kärna, och ingen samtidig multithreading. En virtuell HB-dator ger upp till 260 GB/sek minnesbandbredd.

ACU: 199-216

Premium-lagring: Stöds

Cachelagring för premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Processor | Minne (GB) | Minnesbandbredd GB/s | Bas-CPU-frekvens (GHz) | All-cores frekvens (GHz, topp) | Enkärnfrekvens (GHz, topp) | RDMA-prestanda (Gb/s) | MPI-stöd | Temp-lagring (GB) | Maximalt antal datadiskar | Max Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Alla | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.