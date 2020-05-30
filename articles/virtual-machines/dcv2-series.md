---
title: DC-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i DC-serien.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 955b3d967d2e62e1158f329a58f040121ca8b416
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220973"
---
# <a name="dcsv2-series"></a>DCsv2-serien


DCsv2-serien kan hjälpa till att skydda konfidentialiteten och integriteten hos dina data och kod när de bearbetas i det offentliga molnet. De här datorerna backas upp av den senaste generationen Intel XEON E-2288G-processor med SGX-teknik. Med Intel Turbo Boost-tekniken kan de här datorerna gå upp till 5,0 GHz. DCsv2 Series-instanser gör det möjligt för kunder att skapa säkra enklaven-baserade program för att skydda sin kod och sina data medan den används.

Exempel på användnings områden är: konfidentiell data delning för multidelar, bedrägeri identifiering, tvättning av pengar, blockchain, konfidentiell användnings analys, informations analys och konfidentiell maskin inlärning.

Premium Storage: stöds *

Premium Storage cachelagring: stöds *

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

* Förutom Standard_DC8_v2



| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) | EPC-minne (MiB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- Virtuella datorer i DCsv2-serien är virtuella [datorer i generation 2](./linux/generation-2.md#creating-a-generation-2-vm) och stöder bara `Gen2` avbildningar.
- För närvarande endast tillgängligt i Storbritannien, södra, Kanada, centrala och östra USA.
- Tidigare generation av konfidentiella beräknings datorer: [DC-serien](sizes-previous-gen.md#preview-dc-series)
- Skapa virtuella DCsv2-datorer med hjälp av [Azure Portal](./linux/quick-create-portal.md) eller [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview)



## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
