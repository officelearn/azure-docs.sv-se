---
title: HBv2-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i HBv2-serien.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: 730065333fde7431b7018ef0a6e614e12727d7ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84673861"
---
# <a name="hbv2-series"></a>HBv2-serien

Virtuella datorer i HBv2-serien är optimerade för program som drivs av minnes bandbredd, till exempel flytande dynamik, analys av ändliga element och behållar simulering. HBv2 VM Feature 120 AMD EPYC 7742 processor kärnor, 4 GB RAM per CPU-kärna och ingen samtidig multitrådning. Varje HBv2 VM ger upp till 340 GB/s minnes bandbredd och upp till 4 teraFLOPS FP64-beräkning.

Premium Storage: stöds

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

| Storlek | Virtuell processor | Processor | Minne (GB) | Minnes bandbredd GB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GB/s) | MPI-stöd | Temp-lagring (GB) | Maximalt antal datadiskar | Högsta Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC-7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Alla | 480 + 960 | 8 | 1 |


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