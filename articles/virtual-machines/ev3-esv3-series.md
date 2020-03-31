---
title: Ev3-serien och Esv3-serien – virtuella Azure-datorer
description: Specifikationer för ev3- och Esv3-seriens virtuella datorer.
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: aad4610a44c59ed95cf1ad9777329097886c5bed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164380"
---
# <a name="ev3-and-esv3-series"></a>Ev3- och Esv3-serien

Ev3- och Esv3-serien har Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) i en hypertrådad konfiguration, vilket ger ett bättre värde för de flesta allmänna arbetsbelastningar och ger Ev3 i linje med det allmänna syftet virtuella datorer i de flesta andra moln.  Minnet har utökats (från 7 GiB/vCPU till 8 GiB/vCPU) medan disk- och nätverksgränser har justerats per kärna för att anpassa sig till övergången till hyperthreading. Ev3 är uppföljningen av det höga minnet VM storlekar av D / Dv2 familjer.

## <a name="ev3-series"></a>Ev3-serien

Ev3-serien instanser är baserade på funktionen Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) processor och Intel Turbo Boost Technology 2.0. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du ESv3-storlekarna. Pris- och debiteringsmätarna för ESv3-storlekar är samma som för Ev3-serien.

Ev3-serien VM har Intel® Hyper-Threading Technology.

ACU: 160–190

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal nätverkskort/nätverksbandbredd |
|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3 000/46/23     | 2/1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6 000/93/46     | 2/2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12 000/187/93   | 4/4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24 000/375/187  | 8/8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8/10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48 000/750/375  | 8/16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96 000/1 000/500 | 8/24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96 000/1 000/500 | 8/30000 |
| Standard_E64i_v3 <sup>1,2</sup> | 64 | 432 | 1600 | 32 | 96 000/1 000/500 | 8/30000 |

<sup>1</sup> Begränsade kärnstorlekar finns tillgängliga.

<sup>2</sup> Instansen är isolerad till maskinvara som är avsedd för en enskild kund.

## <a name="esv3-series"></a>Esv3-serien

Esv3-serien instanser är baserade på funktionen Intel® Xeon® 8171M 2.1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) processor, Intel Turbo Boost Technology 2.0, och använda premium lagring. Esv3-serien instanser är idealiska för minnesintensiva företagsprogram.

VM-funktionen Intel® Hyper-Threading Technology i Esv3-serien.

ACU: 160–190

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2/2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4/4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8/8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

<sup>1</sup> Begränsade kärnstorlekar finns tillgängliga.

<sup>2</sup> Instansen är isolerad till maskinvara som är avsedd för en enskild kund.

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
