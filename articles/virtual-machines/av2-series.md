---
title: Av2-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i Av2-serien.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: fe27a1cc78d1f37d535f364c03803a5196090a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163955"
---
# <a name="av2-series"></a>Av2-serien

Virtuella datorer i Av2-serien kan distribueras på en mängd olika maskinvarutyper och processorer. Virtuella datorer i Av2-serien har processorprestanda och minneskonfigurationer som är bäst lämpade för arbetsbelastningar på ingångsnivå som utveckling och test. Storleken begränsas för att erbjuda konsekvent processorprestanda för den löpinstansen, oavsett vilken maskinvara den distribueras på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn. Några exempel på användningsfall är utvecklings- och testservrar, webbservrar med låg trafik, små till medelstora databaser, konceptbevis och koddatabaser.

ACU: 100

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Max datadiskar/dataflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2/250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2/500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4/1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8/2000 |

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
