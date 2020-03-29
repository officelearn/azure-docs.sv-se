---
title: Dav4- och Dasv4-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i Dav4 och Dasv4-serien.
services: virtual-machines
author: migerdes
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 809843bddc4216df4dab44330172d36778248b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162187"
---
# <a name="dav4-and-dasv4-series"></a>Dav4- och Dasv4-serien

Dav4-serien och Dasv4-serien är nya storlekar som använder AMD:s 2,35 Ghz EPYC<sup>TM</sup> 7452-processor i en flertrådad konfiguration med upp till 256 MB L3-cache som dedikerar 8 GB av den L3-cachen till var 8-kärnor som ökar kundalternativen för att köra sina arbetsbelastningar för allmänna ändamål. Dav4-serien och Dasv4-serien har samma minnes- och diskkonfigurationer som D & Dsv3-serien.

## <a name="dav4-series"></a>Dav4-serien

ACU: 230-260

Premium-lagring: Stöds inte

Cachelagring av premiumlagring: Stöds inte

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

Storlekar i Dav4-serien är baserade på 2,35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en maximal maximal frekvens på 3,35 GHz. Storlekar i Dav4-serien erbjuder en kombination av vCPU, minne och tillfällig lagring för de flesta produktionsarbetsbelastningar. Datadisklagring faktureras separat från virtuella datorer. Använd Dasv4-storlekarna om du vill använda premium-SSD. Pris- och faktureringsmätare för Dasv4-storlekar är desamma som Dav4-serien.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Max nätverkskort / Förväntad nätverksbandbredd (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3 000 / 46 / 23   | 2/1 000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6 000 / 93 / 46   | 2/2 000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12 000 / 187 / 93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24 000 / 375 / 187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48 000 / 750 / 375 |8/16 000 |
| Standard_D48a_v4<sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4<sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4<sup>**</sup> | 96 | 384 | 2400 | 32 | | |

<sup>**</sup>Dessa storlekar finns i förhandsversion.  Om du är intresserad av att prova dessa [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)större storlekar, registrera dig på .

## <a name="dasv4-series"></a>Dasv4-serien

ACU: 230-260

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

Dasv4-seriens storlekar är baserade på 2,35 GHz AMD EPYC<sup>TM</sup> 7452-processor som kan uppnå en ökad maximal frekvens på 3,35 GHz och använda premium SSD. Dasv4-seriens storlekar erbjuder en kombination av vCPU, minne och tillfällig lagring för de flesta produktionsarbetsbelastningar.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Max nätverkskort / Förväntad nätverksbandbredd (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000 / 32 (50)|3200 / 48|2/1 000 |
| Standard_D4as_v4|4|16|32|8|8000 / 64 (100)|6400 / 96|2/2 000 |
| Standard_D8as_v4|8|32|64|16|16000 / 128 (200)|12800 / 192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000 / 255 (400)|25600 / 384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000 / 510 (800)|51200 / 768|8/16 000 |
| Standard_D48as_v4<sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4<sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4<sup>**</sup>|96|384|768|32| | | 

<sup>**</sup>Dessa storlekar finns i förhandsversion.  Om du är intresserad av att prova dessa [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)större storlekar, registrera dig på .

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
