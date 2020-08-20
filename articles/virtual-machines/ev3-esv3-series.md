---
title: Ev3-serien och Esv3-serien
description: Specifikationer för virtuella datorer med Ev3 och Esv3-serien.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f9ef048fa7cc4b795f409326988e3276743b6992
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648963"
---
# <a name="ev3-and-esv3-series"></a>Ev3- och Esv3-serien

Ev3-och Esv3-serien körs på Intel® Xeon® platina 8272CL (överlappande sjö), eller Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz-processorn (Broadwell) i en Hyper-threadd konfiguration, vilket ger en bättre värdes justering för de flesta allmänna arbets belastningar, och att Ev3 kan anpassas till de flesta virtuella datorer i de flesta andra moln.  Minnet har utökats (från 7 GiB/vCPU till 8 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras med flytten till hyperthreading. Ev3 är en uppföljning av de virtuella datorerna med hög minnes storlek för D/Dv2-familjer.

## <a name="ev3-series"></a>Ev3-serien

Instanserna i Ev3-serien körs på Intel® Xeon® platina 8272CL (överlappande sjö), Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) och funktionen Intel Turbo Boost Technology 2,0. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du ESv3-storlekarna. Pris- och debiteringsmätarna för ESv3-storlekar är samma som för Ev3-serien.

Ev3 för virtuella datorer i serien Intel® Hyper-Threading-teknik.

ACU: 160–190

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal nätverkskort | Nätverksbandbredd |
|---|---|---|---|---|---|---|---|
| Standard_E2_v3  | 2  | 16  | 50   | 4  | 3 000/46/23     | 2|1000  |
| Standard_E4_v3  | 4  | 32  | 100  | 8  | 6 000/93/46     | 2|2000  |
| Standard_E8_v3  | 8  | 64  | 200  | 16 | 12 000/187/93   | 4|4000  |
| Standard_E16_v3 | 16 | 128 | 400  | 32 | 24 000/375/187  | 8|8000  |
| Standard_E20_v3 | 20 | 160 | 500  | 32 | 30000/469/234  | 8|10000 |
| Standard_E32_v3 | 32 | 256 | 800  | 32 | 48 000/750/375  | 8|16000 |
| Standard_E48_v3 | 48 | 384 | 1200 | 32 | 96 000/1 000/500 | 8|24000 |
| Standard_E64_v3 | 64 | 432 | 1600 | 32 | 96 000/1 000/500 | 8|30000 |
| Standard_E64i_v3 <sup>1, 2</sup> | 64 | 432 | 1600 | 32 | 96 000/1 000/500 | 8|30000 |

<sup>1</sup> begränsad kärn storlek är tillgänglig.

<sup>2</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.

## <a name="esv3-series"></a>Esv3-serien

Instanserna i Esv3-serien körs på Intel® Xeon® platina 8272CL (överlappande sjö), Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), funktion Intel Turbo Boost Technology 2,0 och Använd Premium Storage. Esv3-seriens instanser är idealiska för minnes intensiva företags program.

Esv3 för virtuella datorer i serien Intel® Hyper-Threading-teknik.

ACU: 160–190

Premium Storage: stöds

Premium Storage caching: stöds

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2s_v3 | 2 | 16 | 32 | 4 | 4000/32 (50) | 3200/48 | 2/1000 |
| Standard_E4s_v3 <sup>1</sup>  | 4  | 32  | 64  | 8  | 8000/64 (100)   | 6400/96   | 2|2000 |
| Standard_E8s_v3 <sup>1</sup>  | 8  | 64  | 128 | 16 | 16000/128 (200) | 12800/192 | 4|4000 |
| Standard_E16s_v3 <sup>1</sup> | 16 | 128 | 256 | 32 | 32000/256 (400) | 25600/384 | 8|8000 |
| Standard_E20s_v3 | 20 | 160 | 320 | 32 | 40000/320 (400) | 32000/480 | 8/10000 |
| Standard_E32s_v3 <sup>1</sup>  | 32 | 256 | 512 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |
| Standard_E48s_v3 <sup>1</sup>  | 48 | 384 | 768 | 32 | 96000/768 (1200)   | 76800/1152 | 8|24000 |
| Standard_E64s_v3 <sup>1</sup>  | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |
| Standard_E64is_v3 <sup>2</sup> | 64 | 432 | 864 | 32 | 128000/1024 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> begränsad kärn storlek är tillgänglig.

<sup>2</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

Pris kalkylator: [pris kalkylator](https://azure.microsoft.com/pricing/calculator/)

Mer information om disk typer: [disk typer](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.