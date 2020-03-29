---
title: HBv2-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i HBv2-serien.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164805"
---
# <a name="hbv2-series"></a>HBv2-serien

HBv2-serien virtuella datorer är optimerade för program som drivs av minnesbandbredd, såsom vätskedynamik, finit elementanalys och reservoarsimulering. HBv2 virtuella datorer har 120 AMD EPYC 7742 processorkärnor, 4 GB RAM per CPU-kärna och ingen samtidig flertrådsteknik. Varje virtuell HBv2-dator ger upp till 340 GB/sek minnesbandbredd och upp till 4 teraFLOPS för FP64-beräkning.

Premium-lagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

| Storlek | Virtuell processor | Processor | Minne (GB) | Minnesbandbredd GB/s | Bas-CPU-frekvens (GHz) | All-cores frekvens (GHz, topp) | Enkärnfrekvens (GHz, topp) | RDMA-prestanda (Gb/s) | MPI-stöd | Temp-lagring (GB) | Maximalt antal datadiskar | Max Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3.1 | 3.3 | 200 | Alla | 480 + 960 | 8 | 1 |


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