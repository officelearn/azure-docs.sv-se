---
title: DC-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i DC-serien.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256937"
---
# <a name="preview-dcsv2-series"></a>Förhandsgranskning: DCsv2-serien


DCsv2-serien kan skydda sekretessen och integriteten för dina data och din kod medan den bearbetas i det offentliga molnet. Dessa maskiner backas upp av den senaste generationen av Intel XEON E-2288G-processor med SGX-teknik. Med Intel Turbo Boost Technology kan dessa maskiner gå upp till 5,0 GHz. DCsv2-serieinstanser gör det möjligt för kunder att skapa säkra enklavbaserade program för att skydda sin kod och sina data när de används.

Exempel på användningsfall är konfidentiell datadelning i flera delar, bedrägeriidentifiering, bekämpning av penningtvätt, blockkedje, konfidentiell användningsanalys, underrättelseanalys och konfidentiell maskininlärning.

Premium-lagring: Stöds*

Cachelagring för premiumlagring: Stöds*

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

*Med undantag för Standard_DC8_v2



| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Max nätverkskort / Förväntad nätverksbandbredd (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Virtuella datorer i DCsv2-serien är virtuella `Gen2` generationer och stöder endast avbildningar. [generation 2 VMs](./linux/generation-2.md#creating-a-generation-2-vm)
- Endast för närvarande finns i Uk South och Canada Central.
- Tidigare generering av virtuella datorer med konfidentiell beräkning: [DC-serien](sizes-previous-gen.md)
- Skapa virtuella DCsv2-datorer med Azure Portal [Create VM - Portal](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
