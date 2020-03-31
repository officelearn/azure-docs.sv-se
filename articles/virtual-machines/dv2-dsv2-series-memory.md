---
title: Minnesoptimerade virtuella dv2- och DSv2-serie- azure-virtuella datorer
description: Specifikationer för virtuella datorer i Dv2- och DSv2-serien.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 66e415070e60676df5602078aff50c7b68920f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914049"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Minnesoptimerad Dv2- och Dsv2-serie

Dv2 och Dsv2-serien, en uppföljning av den ursprungliga D-serien, har en mer kraftfull CPU. Storlekar i DSv2-serien körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-5 2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

## <a name="dv2-series-11-15"></a>Dv2-serien 11-15

Dv2-seriens storlekar körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2 673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer.

ACU: 210–250

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Max temp lagringsdataflöde: IOPS/Läs MBps/Write MBps | Max datadiskar/dataflöde: IOPS | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6 000/93/46    | 8/8x500   | 2/1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12 000/187/93  | 16/16x500 | 4/3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24 000/375/187 | 32/32x500 | 8/6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48 000/750/375 | 64/64x500 | 8/12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8/25000 <sup>2</sup> |

<sup>1</sup> Instans är isolerad till hårdvara tillägnad en enda kund.
<sup>2</sup> 25000 Mbps med accelererat nätverkande.

## <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

Storlekar i DSv2-serien körs på Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-5 2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processorer.

ACU: 210 - 250 <sup>1</sup>

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2/1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4/3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8/6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8/12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8/25000 <sup>4</sup> |

<sup>1</sup> Det maximala diskgenomströmningsflödet (IOPS eller MBps) som är möjligt med en VIRTUELL DSV2-serie kan begränsas av antalet, storleken och stripingen av de anslutna diskarna.  Mer information finns i [Designa för hög prestanda](./windows/premium-storage-performance.md).
<sup>2</sup> Instans är isolerad till Intel Haswell-baserad hårdvara och tillägnad en enda kund.  
<sup>3</sup> Begränsade kärnstorlekar tillgängliga.  
<sup>4</sup> 25000 Mbps med accelererat nätverkande.

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
