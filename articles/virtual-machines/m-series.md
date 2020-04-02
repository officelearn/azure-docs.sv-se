---
title: M-serien – virtuella Azure-datorer
description: Specifikationer för virtuella datorer i M-serien.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 3e0552570d5bdb7f812852cd058710b833b7fdd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521328"
---
# <a name="m-series"></a>M-serien

M-serien erbjuder ett högt vCPU-antal (upp till 128 virtuella processorer) och en stor mängd minne (upp till 3,8 TiB). Det är också idealiskt för extremt stora databaser eller andra program som drar nytta av höga vCPU-antal och stora mängder minne. M-seriens storlekar stöds både&reg; på Intel&reg; Xeon CPU E7-8890 v3 @ 2,50 GHz och på Intel&reg; Xeon&reg; Platinum 8280M 2,7 GHz (Cascade Lake).

M-serien VM har&reg; Intel Hyper-Threading Technology.

ACU: 160–180

Premium-lagring: Stöds

Cachelagring av premiumlagring: Stöds

Live Migration: Stöds inte

Minneskonering av uppdateringar: Stöds inte

Skrivaccelerator: [Stöds](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temp-lagringsdataflöde: IOPS/MBps (cachestorlek i GiB) | Max oankopplat diskdataflöde: IOPS/MBps | Max nätverkskort/förväntad nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>2</sup>       | 8   | 218.75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437.5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1,2,3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8/32000 |

<sup>1</sup> Mer än 64 vCPU kräver en av dessa gäst-OPERATIVSYSTEM som stöds: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 och Red Hat Enterprise Linux, CentOS 7.3 eller Oracle Linux 7.3 med LIS 4.2.1.

<sup>2</sup> Begränsade kärnstorlekar tillgängliga.

<sup>3</sup> Instansen är isolerad till maskinvara som är avsedd för en enskild kund.

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
