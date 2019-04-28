---
title: ta med fil
description: ta med fil
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 04/11/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: d89a9c4c4498e249dbfbd453ef9772d18ffd213f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541632"
---
Det här avsnittet innehåller information om äldre generationer storlekar på virtuella datorer. Dessa storlekar stöds fortfarande, men får inte ytterligare kapacitet. Det finns nyare eller alternativa storlekar som är allmänt tillgängliga. Se [storlekar för Windows-datorer i Azure](../articles/virtual-machines/windows/sizes.md) eller [storlekar för Linux-datorer i Azure](../articles/virtual-machines/linux/sizes.md) för att välja den virtuella datorn de storlekar som kommer bäst passar dina behov.  

Mer information om att ändra storlek på en Linux-VM finns i [ändra storlek på en Linux-dator med Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Om du använder Windows virtuella datorer och föredrar att använda PowerShell, se [ändra storlek på en virtuell Windows-dator](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Nyare storlek rekommendation**: [Av2-serien](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

Storlekarna på den grundläggande nivån är främst avsedda för utvecklingsarbetsbelastningar och andra program som inte kräver belastningsutjämning, automatisk skalning eller minnesintensiva virtuella datorer.

|Storlek – Storlek\namn | Virtuell processor |Minne|Nätverkskort (max.)|Högsta temporär diskstorlek |Max. datadiskar (1 023 GB som är var)|Max. IOPS (300 per disk)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 MB|2| 20 GB|1|1 × 300|
|A1\Basic_A1|1|1,75 GB|2| 40 GB |2|2 × 300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4 × 300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8 × 300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16 × 300|

<br>

### <a name="a-series"></a>A-serien  

**Nyare storlek rekommendation**: [Av2-serien](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50-100

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0.768 |20 |1 |1 × 500 |2/100 |
| Standard_A1 |1 |1.75 |70 |2 |2 × 500 |2/500  |
| Standard_A2 |2 |3.5 |135 |4 |4 × 500 |2/500 |
| Standard_A3 |4 |7 |285 |8 |8 × 500 |2/1 000 |
| Standard_A4 |8 |14 |605 |16 |16 × 500 |4/2 000 |
| Standard_A5 |2 |14 |135 |4 |4 × 500 |2/500 |
| Standard_A6 |4 |28 |285 |8 |8 × 500 |2/1 000 |
| Standard_A7 |8 |56 |605 |16 |16 × 500 |4/2 000 |

<sup>1</sup> the A0-storleken har andel prenumerationer på den fysiska maskinvaran. För just den här storleken kan andra kunddistributioner påverka prestanda för arbetsbelastningen som körs. Nedan beskrivs relativa prestanda som den förväntade baslinjen, som har en ungefärlig variation på 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser  

**Nyare storlek rekommendation**: [Av2-serien](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32 × 500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 × 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup>för MPI-program, dedikerade RDMA backend-nätverket är aktiverat som FDR InfiniBand-nätverk, som har extremt korta svarstider och hög bandbredd.  

<br>

### <a name="d-series"></a>D-serien  

**Nyare storlek rekommendation**: [Dv3-serien](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3 000 / 46 / 23                                           | 4 / 4 x 500                         | 2/500                 |
| Standard_D2  | 2         | 7           | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 000                     |
| Standard_D3  | 4         | 14          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/2 000                     |
| Standard_D4  | 8         | 28          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/4 000                     |

<sup>1</sup> VM-serie kan köras på en av följande CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,3 GHz Intel XEON® eller E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-serien – minnesoptimerade  

**Nyare storlek rekommendation**: [Dv3-serien](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 000                     |
| Standard_D12 | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/2 000                     |
| Standard_D13 | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/4 000                     |
| Standard_D14 | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 8000                |

<sup>1</sup> VM-serie kan köras på en av följande CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,3 GHz Intel XEON® eller E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS-serien  

**Nyare storlek rekommendation**: [DSv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4,000 / 32 (43) |3,200 / 32 |2/500 |
| Standard_DS2 |2 |7 |14 |8 |8,000 / 64 (86) |6,400 / 64 |2/1 000 |
| Standard_DS3 |4 |14 |28 |16 |16,000 / 128 (172) |12,800 / 128 |4/2 000 |
| Standard_DS4 |8 |28 |56 |32 |32,000 / 256 (344) |25,600 / 256 |8/4 000 |

<sup>1</sup> VM-serie kan köras på en av följande CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,3 GHz Intel XEON® eller E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-serien – minnesoptimerade  

**Nyare storlek rekommendation**: [DSv3-serien](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1,2</sup>

Premium-lagring:  Stöds

Premium Storage Caching:  Stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2/1 000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4/2 000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8/4 000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i DS-serien kan begränsas antal, storlek och striping av de anslutna diskarna.  Mer information finns i [designa för höga prestanda](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> VM-serie kan köras på en av följande CPU: 2.2 GHz Intel Xeon® E5 2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) 2,3 GHz Intel XEON® eller E5-2673 v4 (Broadwell)  

<br>
