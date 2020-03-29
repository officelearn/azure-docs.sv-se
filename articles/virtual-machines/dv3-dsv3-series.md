---
title: Dv3- och Dsv3-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i Dv3 och Dsv3-serien.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164414"
---
# <a name="dv3-and-dsv3-series"></a>Dv3- och Dsv3-serien

Dv3-serien körs på Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer i en flertrådad konfiguration, ger ett bättre värdeerbjudande för de flesta allmänna arbetsbelastningar. Minnet har utökats (från ~3.5 GiB/vCPU till 4 GiB/vCPU) medan disk- och nätverksgränser har justerats per kärna för att anpassa sig till övergången till hyperthreading. Dv3-serien har inte längre den höga minnes-VM-storleken på D/Dv2-serien, de har flyttats till minnet optimerade [Ev3 och Esv3-serien](ev3-esv3-series.md).

Exempel på användningsfall i D-serien omfattar program i företagsklass, relationsdatabaser, cachelagring i minnet och analyser.

## <a name="dv3-series"></a>Dv3-serien

Dv3-seriens storlekar körs på Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 000 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer med Intel Turbo Boost Technology 2.0. Storlekarna i Dv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du Dsv3-storlekarna. Pris- och debiteringsmätarna för Dsv3-storlekar är samma som för Dv3-serien.

Virtuella datorer i Dv3-serien har Intel® Teknik för flertrådsteknik.

ACU: 160–190

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Max nätverkskort/nätverksbandbredd |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3 000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6 000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12 000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24 000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48 000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96 000/1 000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96 000/1 000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-serien

Storlekar i Dsv3-serien körs på Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2 .3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer med Intel Turbo Boost Technology 2.0 och använda premiumlagring. Storlekarna i Dsv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.

Virtuella datorer i Dsv3-serien har Intel® Flertrådsteknik.

ACU: 160–190

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

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
