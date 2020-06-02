---
title: Ddv4 och Ddsv4-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i DV4, Ddv4, Dsv4 och Ddsv4-serien.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 42b42a7477326196546ad445367691192f00569a
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84263253"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 och Ddsv4-serien

Ddv4 och Ddsv4-serien körs på Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar. Den har en allt högre klock hastighet på 3,4 GHz, [Intel &reg; Turbo Boost-teknik 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading-teknik](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) och [Intel &reg; Advanced Vector-tillägg 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Dessa nya VM-storlekar kommer att ha 50% större lokal lagring, samt bättre IOPS för lokala diskar för både läsning och skrivning jämfört med [dv3-/Dsv3-](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) storlekarna med [Gen2-VM](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2): ar.

Användnings fall i D-serien omfattar program i företags klass, Relations databaser, minnes intern cachelagring och analys.

> [!IMPORTANT]
> Om du distribuerar en ny virtuell dator med hjälp av Ddv4 eller Ddsv4-serien och tänker använda en Linux-avbildning måste du använda antingen RHEL 8. x, CentOS 8. x eller Oracle 7. x eller senare. Om du väljer RHEL 7. x, CentOS 7. x eller Orcale 6. x, kommer det att vara ett kernel-panik-fel. Microsoft distribuerar aktivt en åtgärd. Endast RHEL, CentOS och Oracle påverkas. 

## <a name="ddv4-series"></a>Ddv4-serien

Ddv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Ddv4-serien erbjuder en kombination av vCPU, minne och temporär disk för de flesta produktions arbets belastningar.

De nya Ddv4 VM-storlekarna omfattar snabb, större lokal SSD-lagring (upp till 2 400 GiB) och är utformad för program som drar nytta av låg latens, lokal lagring med hög hastighet, till exempel program som kräver snabb läsning/skrivning till temporär lagring eller som kräver temporär lagring för cacheminnen eller temporära filer. Du kan ansluta standard-SSD och standard-HDD-lagring till de virtuella Ddv4-datorerna. Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer.

ACU: 195-210

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt cacheminne för cachelagrad och temporär lagring: IOPS/Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2/1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2/2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4/4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8/8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8/16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8/24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8/30000 |

## <a name="ddsv4-series"></a>Ddsv4-serien

Ddsv4-serien körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Ddsv4-serien erbjuder en kombination av vCPU, minne och temporär disk för de flesta produktions arbets belastningar.

De nya Ddsv4 VM-storlekarna omfattar snabb, större lokal SSD-lagring (upp till 2 400 GiB) och är utformad för program som drar nytta av låg latens, lokal lagring med hög hastighet, till exempel program som kräver snabb läsning/skrivning till temporär lagring eller som kräver temporär lagring för cacheminnen eller temporära filer. 

 > [!NOTE]
 >Pris-och debiterings mätare för Ddsv4-storlekar är samma som för Ddv4-serien.

ACU: 195-210

Premium Storage: stöds

Premium Storage caching: stöds

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt cacheminne för cachelagrad och temporär lagring: IOPS/Mbit/s | Maximalt antal cachelagrade diskar: IOPS/MBps | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120 (50) | 3000/48 | 2/1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242 (100) | 6400/96 | 2/2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485 (200) | 12800/192 | 4/4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968 (400) | 25600/384 | 8/8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936 (800) | 51200/768 | 8/16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8/24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8/30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andra storlekar

- [Generellt syfte](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
