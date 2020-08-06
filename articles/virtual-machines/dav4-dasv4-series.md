---
title: Dav4- och Dasv4-serien
description: Specifikationer för virtuella datorer med Dav4 och Dasv4-serien.
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 570e2798cf2ae5a20d55973622cca9344ea4a4a3
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837150"
---
# <a name="dav4-and-dasv4-series"></a>Dav4- och Dasv4-serien

Dav4-serien och Dasv4-serien är nya storlekar som använder AMD: s 2.35 GHz EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 MB L3-cache DEDIKERA 8 MB av den L3-cachen till varje 8 kärnor som ökar kund alternativen för att köra sina generella arbets belastningar. Dav4-serien och Dasv4-serien har samma minnes-och diskkonfigurationer som D & Dsv3-serien.

## <a name="dav4-series"></a>Dav4-serien

ACU: 230-260

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

Dav4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz. Storlekarna i Dav4-serien erbjuder en kombination av vCPU, minne och tillfällig lagring för de flesta produktions arbets belastningar. Datadisklagring faktureras separat från virtuella datorer. Om du vill använda Premium SSD använder du Dasv4-storlekarna. Pris-och debiterings mätare för Dasv4-storlekar är samma som för Dav4-serien.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3 000 / 46 / 23   | 2 | 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6 000 / 93 / 46   | 2 | 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12 000 / 187 / 93 | 4 | 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24 000 / 375 / 187 |8 | 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48 000 / 750 / 375 |8 | 16000 |
| Standard_D48a_v4| 48 | 192| 1200 | 32 | 96000/1000/500 | 8 | 24000 |
| Standard_D64a_v4| 64 | 256 | 1600 | 32 | 96000/1000/500 | 8 | 30000 |
| Standard_D96a_v4| 96 | 384 | 2400 | 32 | 96000/1000/500 | 8 | 30000 |

## <a name="dasv4-series"></a>Dasv4-serien

ACU: 230-260

Premium Storage: stöds

Premium Storage caching: stöds

Direktmigrering: stöds

Minnes bebetjänings uppdateringar: stöds

Dasv4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz och använder Premium SSD. Storlekarna i Dasv4-serien erbjuder en kombination av vCPU, minne och tillfällig lagring för de flesta produktions arbets belastningar.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 | 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 | 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 | 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600/384|8 | 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 | 16000 |
| Standard_D48as_v4|48|192|384|32|96000/1020 (1200)|76800/1148|8 | 24000 |
| Standard_D64as_v4|64|256|512|32|128000/1020 (1600)|80000/1200|8 | 30000 | 
| Standard_D96as_v4|96|384|768|32|192000/1020 (2400)|80000/1200|8 | 30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

Pris kalkylator: [pris kalkylator](https://azure.microsoft.com/pricing/calculator/)

Mer information om disk typer: [disk typer](./linux/disks-types.md#ultra-disk)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.