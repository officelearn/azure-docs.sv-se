---
title: Mv2-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i Mv2-serien.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6fb0f93d3ac124b21bbc52ddc57bc720de6406e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163904"
---
# <a name="mv2-series"></a>Mv2-serien

Mv2-serien har hög genomströmning, låg latens plattform som körs på en hyper-threaded Intel® Xeon® Platinum 8180M 2.5GHz (Skylake) processor med en all core basfrekvens på 2,5 GHz och en max turbo frekvens på 3,8 GHz. Alla storlekar i Mv2-seriens virtuella dator kan använda både vanliga och premiumbeständiga diskar. Mv2-serien instanser är minne optimerade VM storlekar ger oöverträffad beräkningsprestanda för att stödja stora i minnet databaser och arbetsbelastningar, med ett högt minne-till-CPU-förhållande som är idealisk för relationella databasservrar, stora cachar och i minnet Analytics.

Mv2-serien VM funktion Intel® Hyper-Threading Technology

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

Skrivaccelerator: [Stöds](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Max nätverkskort / Förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8/16 000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8/16 000 |
| Standard_M416ms_v2<sup>1, 2</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |
| Standard_M416s_v2<sup>1, 2</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8 / 32000 |

<sup>1</sup> Mv2-serien virtuella datorer är generation 2 bara. Om du använder Linux läser du [Support för virtuella generationer 2 på Azure](./linux/generation-2.md) för instruktioner om hur du hittar och väljer en avbildning.

<sup>2</sup> För M416ms_v2 och M416s_v2 storlek, observera att det finns initialt stöd för följande avbildning: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 för SAP-program."

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
