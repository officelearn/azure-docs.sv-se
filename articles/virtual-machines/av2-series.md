---
title: Av2-serien
description: Specifikationer för virtuella datorer i AV2-serien.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: fcbcb89505130dddb42d97c153b172a22a9b29b2
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831489"
---
# <a name="av2-series"></a>Av2-serien

De virtuella datorerna i AV2-serien kan distribueras på olika typer av maskin vara och processorer. De virtuella datorerna i AV2-serien har CPU-prestanda och minnes konfiguration som passar bäst för arbets belastningar som utveckling och testning på Start nivå. Storleken begränsas till att erbjuda konsekvent processor prestanda för den aktiva instansen, oavsett vilken maskin vara den är distribuerad på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn. Exempel på användnings områden är utvecklings-och test servrar, webb servrar med låg trafik, små till medel stora databaser, koncept koncept och kod databaser.

ACU: 100

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds


| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt data flöde för temporär lagring: IOPS/Read Mbit/s/Write Mbit/s | Maximalt antal data diskar/data flöde: IOPS | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

Pris kalkylator: [pris kalkylator](https://azure.microsoft.com/pricing/calculator/)

Mer information om disk typer: [disk typer](./linux/disks-types.md#ultra-disk)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.