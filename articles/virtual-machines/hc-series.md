---
title: HC-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i HC-serien.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164788"
---
# <a name="hc-series"></a>HC-serien

Virtuella datorer i HC-serien är optimerade för applikationer som drivs av tät beräkning, till exempel implicit finita elementanalys, molekylär dynamik och beräkningskemi. HC VMs har 44 Intel Xeon Platinum 8168 processorkärnor, 8 GB RAM per CPU-kärna, och ingen hyperthreading. Intel Xeon Platinum-plattformen stöder Intels rika ekosystem av programvaruverktyg som Intel Math Kernel Library.

ACU: 297-315

Premium-lagring: Stöds

Cachelagring för premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Processor | Minne (GB) | Minnesbandbredd GB/s | Bas-CPU-frekvens (GHz) | All-cores frekvens (GHz, topp) | Enkärnfrekvens (GHz, topp) | RDMA-prestanda (Gb/s) | MPI-stöd | Temp-lagring (GB) | Maximalt antal datadiskar | Max Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platina 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Alla | 700 | 4 | 1 |

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