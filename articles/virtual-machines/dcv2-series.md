---
title: DC-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i DC-serien.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: fd8f01f0fc7180d271404ffee4496ff9cbac7222
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205933"
---
# <a name="preview-dcv2-series"></a>För hands version: DCv2-serien


DCv2-serien kan hjälpa till att skydda konfidentialiteten och integriteten hos dina data och kod när de bearbetas i det offentliga molnet. De här datorerna backas upp av den senaste generationen Intel XEON E-2288G-processor med SGX-teknik. Med Intel Turbo Boost-tekniken kan de här datorerna gå upp till 5,0 GHz. DCv2 Series-instanser gör det möjligt för kunder att skapa säkra enklaven-baserade program för att skydda sin kod och sina data medan den används.

Exempel på användnings områden är konfidentiell data delning, bedrägeri identifiering, tvättning av pengar, blockchain, konfidentiell användnings analys, informations analys och konfidentiell maskin inlärning.

Premium Storage: stöds *

Premium Storage cachelagring: stöds *

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

\* Förutom Standard_DC8_v2



| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Virtuella datorer i DCv2-serien är virtuella [datorer i generation 2](./linux/generation-2.md#creating-a-generation-2-vm) och stöder bara `Gen2` avbildningar.
- För närvarande endast tillgängligt i Storbritannien, södra.
- Tidigare generation av konfidentiella beräknings datorer: [DC-serien](sizes-previous-gen.md)
- Skapa virtuella DCv2-datorer med Azure Portal [Skapa virtuell dator – Portal](./linux/quick-create-portal.md)



## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
