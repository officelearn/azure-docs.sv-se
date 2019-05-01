---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: f1f02f8b5488609a0c69a6d335c96d3cc9266c71
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744627"
---
<!-- F-series, Fs-series* -->

Databearbetningen optimerad VM-storlekar har förhållandet hög CPU och minne och är bra för webbservrar med medelhög trafik, nätverkstillämpningar, batchprocesser och programservrar. Den här artikeln innehåller information om hur många virtuella processorer, datadiskar och nätverkskort samt storage dataflöde och nätverket bandbredden för varje storlek i den här grupperingen.

Fsv2-serien bygger på processorn Intel® Xeon® Platinum 8168, med en varaktig alla viktiga Turbo klockfrekvens 3,4 GHz och en största enkel kärna turbo frekvens som 3,7 GHz. Intel® AVX-512 instruktioner som är nytt på Intel skalbar processorer, ger upp till en 2 X prestandaökning för vektor bearbetning av arbetsbelastningar på både enkla och dubbla flyttalsåtgärder precision. De är alltså mycket snabbt för alla beräkningsarbetsbelastning. 

Ett lägre listpris per timme Fsv2-serien har har bästa prisprestanda i Azure-portföljen baserat på Azure Compute-beräkningsenhet (ACU) per virtuell processor.

## <a name="fsv2-series-sup1sup"></a>Fsv2-serien <sup>1</sup>

ACU: 195 - 210

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

| Storlek             | virtuella processorer | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 / 31 (32)           | 3200 / 47                | 2 / 875                 |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1,750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3,500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7,000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14,000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28,000              |
| Standard_F72s_v2<sup>2, 3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8 / 30,000              |


<sup>1</sup> Fsv2-serien Virtuella datorns funktion Intel® Hyper-Threading Technology

<sup>2</sup> högst 64 virtuella processorer kräver något av följande gästoperativsystem som stöds: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 och Red Hat Enterprise Linux, CentOS 7.3 eller Oracle Linux 7.3 med LIS 4.2.1

<sup>3</sup> instansen är isolerad till maskinvara som är dedikerad till en enda kund.