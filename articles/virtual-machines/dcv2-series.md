---
title: DC-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i DC-serien.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085730"
---
# <a name="dcsv2-series"></a>DCsv2-serien


DCsv2-serien kan skydda sekretessen och integriteten för dina data och din kod medan den bearbetas i det offentliga molnet. Dessa maskiner backas upp av den senaste generationen av Intel XEON E-2288G-processor med SGX-teknik. Med Intel Turbo Boost Technology kan dessa maskiner gå upp till 5,0 GHz. DCsv2-serieinstanser gör det möjligt för kunder att skapa säkra enklavbaserade program för att skydda sin kod och sina data när de används.

Exempel på användningsfall är: konfidentiell flerpartsdatadelning, bedrägeriidentifiering, bekämpning av penningtvätt, blockchain, konfidentiell användningsanalys, underrättelseanalys och konfidentiell maskininlärning.

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
- Endast tillgängligt i Storbritannien syd, Kanada Central och USA East.
- Tidigare generation av virtuella datorer med konfidentiella beräkningar: [DC-serien](sizes-previous-gen.md#preview-dc-series)
- Skapa virtuella DCsv2-datorer med [Azure-portalen](./linux/quick-create-portal.md) eller [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure compute units (ACU)](acu.md) kan hjälpa dig att jämföra beräkningsprestanda över Azure SKU:er.
