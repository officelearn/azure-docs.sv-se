---
title: Dv2- och Dsv2-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i Dv2 och Dsv2-serien.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 808b14f118e842cb9e52d110075f92ba25a343c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164431"
---
# <a name="dv2-and-dsv2-series"></a>Dv2- och DSv2-serien

Dv2- och Dsv2-serien, en uppföljning av den ursprungliga D-serien, har en mer kraftfull CPU och optimal CPU-till-minne-konfiguration som gör dem lämpliga för de flesta produktionsarbetsbelastningar. Dv2-serien är ca 35% snabbare än D-serien. Dv2-serien körs på Intel® Xeon® 8171M 2,1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

## <a name="dv2-series"></a>Dv2-serien

Dv2-seriens storlekar körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer med Intel Turbo Boost Technology 2.0.

ACU: 210–250

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Maximalt antal datadiskar | Dataflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3 000/46/23    | 4  | 4 × 500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6 000/93/46    | 8  | 8 × 500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12 000/187/93  | 16 | 16 × 500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24 000/375/187 | 32 | 32 × 500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48 000/750/375 | 64 | 64x500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2-serien

Storlekar i DSv2-serien körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer med Intel Turbo Boost Technology 2.0 och använda premiumlagring.

ACU: 210–250

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

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
