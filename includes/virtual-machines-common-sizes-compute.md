---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 5a4495dd675b662273715b5c13a5594adc87fceb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333851"
---
<!-- F-series, Fs-series* -->

Databearbetningen optimerad VM-storlekar har förhållandet hög CPU och minne och är bra för webbservrar med medelhög trafik, nätverkstillämpningar, batchprocesser och programservrar. Den här artikeln innehåller information om hur många virtuella processorer, datadiskar och nätverkskort samt storage dataflöde och nätverket bandbredden för varje storlek i den här grupperingen.

Fsv2-serien bygger på processorn Intel® Xeon® Platinum 8168, med en varaktig alla viktiga Turbo klockfrekvens 3,4 GHz och en största enkel kärna turbo frekvens som 3,7 GHz. Intel® AVX-512 instruktioner som är nytt på Intel skalbar processorer, ger upp till en 2 X prestandaökning för vektor bearbetning av arbetsbelastningar på både enkla och dubbla flyttalsåtgärder precision. De är alltså mycket snabbt för alla beräkningsarbetsbelastning. 

Ett lägre listpris per timme Fsv2-serien har har bästa prisprestanda i Azure-portföljen baserat på Azure Compute-beräkningsenhet (ACU) per virtuell processor. 

F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  

De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller temporär lagring per virtuell processor.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.

Fs-serien ger alla fördelar med F-serien, och Premium-lagring.

## <a name="fsv2-series-sup1sup"></a>Fsv2-serien <sup>1</sup>

ACU: 195 - 210

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

| Storlek             | virtuella processorer | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
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

## <a name="fs-series-sup1sup"></a>FS-serien <sup>1</sup>

ACU: 210 - 250

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000 / 32 (12) |3,200 / 48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8,000 / 64 (24) |6,400 / 96 |2/1 500 |
| Standard_F4s |4 |8 |16 |16 |16,000 / 128 (48) |12,800 / 192 |4/3 000 |
| Standard_F8s |8 |16 |32 |32 |32,000 / 256 (96) |25,600 / 384 |8/6 000 |
| Standard_F16s |16 |32 |64 |64 |64,000 / 512 (192) |51,200 / 768 |8 / 12000 |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

<sup>1</sup> det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i Fs-serien kan begränsas antal, storlek och striping av de anslutna diskarna.  Mer information finns i [designa för höga prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).


<br>

## <a name="f-series"></a>F-serien

ACU: 210 - 250

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3 000 / 46 / 23                                           | 4 / 4 x 500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000           |


<br>


