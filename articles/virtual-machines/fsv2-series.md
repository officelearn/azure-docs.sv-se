---
title: Fsv2-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i Fsv2-serien.
services: virtual-machines
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: f28c6b61aee3c8cbc078db1c2cfb48ed1fba4554
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164856"
---
# <a name="fsv2-series"></a>Fsv2-serien

Fsv2-serien är baserad på Intel® Xeon® Platinum 8168-processorn. Den har en ihållande all core Turbo klockfrekvens på 3,4 GHz och en maximal single-core turbo frekvens på 3,7 GHz. Intel® AVX-512-instruktioner är nya på Intel Scalable-processorer. Dessa instruktioner ger upp till en 2X prestandaökning till arbetsbelastningar för vektorbearbetning på flyttalsoperationer med både enkel och dubbel precision. Med andra ord, de är riktigt snabba för alla beräkningsarbetsbelastning.

Virtuella datorer i Fsv2-serien har Intel® Teknik för flertrådsteknik.

ACU: 195 - 210

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds

Minneskonering av uppdateringar: Stöds

| Storlek | vCPU:s | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2/875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2/1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4/3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4/7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8/14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8/21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8/28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8/30000 |

<sup>1</sup> Användningen av mer än 64 vCPU kräver ett av dessa gästoperativsystem som stöds:

- Windows Server 2016 eller senare
- Ubuntu 16.04 LTS eller senare, med Azure-trimmad kärna (4.15-kernel eller senare)
- SLES 12 SP2 eller senare
- RHEL- eller CentOS version 6.7 till 6.10, med LIS-paket 4.3.1 (eller senare) installerat
- RHEL- eller CentOS version 7.3, med LIS-paket 4.2.1 (eller senare) installerat
- RHEL eller CentOS version 7.6 eller senare
- Oracle Linux med UEK4 eller senare
- Debian 9 med backportskärnan, Debian 10 eller senare
- CoreOS med en 4.14-kärna eller senare

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
