---
title: ta med fil
description: ta med fil
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 006a44e42ed209b6f0e614b92f97e43ec30b99ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468198"
---
Azure HPC-optimerade virtuella datorer är utformade för att leverera prestanda i ledande klass, MPI skalbarhet och kostnads effektivitet för många olika verkliga program.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>InfiniBand-nätverk för storskalig HPC
HBv2 VM-funktionen 200 GB/SEK Mellanox HDR InfiniBand, medan både HB-och HC-VM-funktionen 100 GB/s Mellanox EDR InfiniBand. Var och en av dessa VM-typer är anslutna i ett icke-blockerande fat-träd för optimerad och konsekvent RDMA-prestanda.

HBv2-VM: ar stöder anpassningsbar Routning och dynamisk ansluten transport (DCT, i tillägg till standard RC-och UD-transporter). Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användningen av dem rekommenderas starkt.  

HBv2-, HB-och HC-VM: ar stöder standard Mellanox/OFED-drivrutiner. Därför stöds alla RDMA-verb och MPI-typer. Var och en av dessa VM-typer stöder även maskinvarubaserad avlastning av MPI-Collectives för ökade program prestanda.
 
Ursprungliga VM-funktioner i H-serien 56 GB/SEK Mellanox FDR InfiniBand. För att dra nytta av InfiniBand-gränssnittet i H-serien måste kunderna distribuera med hjälp av de avbildningar som är officiellt kompatibla med den här VM-typen från Azure Marketplace. 


## <a name="hbv2-series"></a>HBv2-serien
Virtuella datorer i HBv2-serien är optimerade för program som drivs av minnes bandbredd, till exempel flytande dynamik, analys av ändliga element och behållar simulering. HBv2 VM Feature 120 AMD EPYC 7742 processor kärnor, 4 GB RAM per CPU-kärna och ingen samtidig multitrådning. Varje HBv2 VM ger upp till 340 GB/s minnes bandbredd och upp till 4 teraFLOPS FP64-beräkning. 

Premium Storage: stöds

| Storlek | vCPU | Processor | Minne (GB) | Minnes bandbredd GB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GB/s) | MPI-stöd | Temp-lagring (GB) | Max antal datadiskar | Högsta Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | Alla | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB-serien
Virtuella datorer i HB-serien är optimerade för program som drivs med minnesbandbredd, som vätskedynamik, explicit analys av finita element och vädermodellering. HB VM Feature 60 AMD EPYC 7551 processor kärnor, 4 GB RAM per CPU-kärna och ingen samtidig multitrådning. En virtuell HB-dator ger upp till 260 GB/s minnes bandbredd.  

ACU: 199-216

Premium Storage: stöds

Premium Storage caching: stöds

| Storlek | vCPU | Processor | Minne (GiB) | Minnes bandbredd GiB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GiB/s) | MPI-stöd | Temp-lagring (GiB) | Max antal datadiskar | Högsta Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Alla | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC-serien
Virtuella datorer i HC-serien är optimerade för program som drivs av kompakt beräkning, till exempel implicit ändlig analys av element, molekyl Dynamics och beräknings kemi. Virtuella HC-datorer har 44 Intel Xeon Platinum 8168-processorkärnor, 8 GB RAM per CPU-kärna och ingen hypertrådning. Intel Xeon platina-plattformen stöder Intels omfattande eko system med program varu verktyg som Intel Math kernel-bibliotek. 

ACU: 297-315

Premium Storage: stöds

Premium Storage caching: stöds


| Storlek | vCPU | Processor | Minne (GiB) | Minnes bandbredd GiB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GiB/s) | MPI-stöd | Temp-lagring (GiB) | Max antal datadiskar | Högsta Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon platina 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Alla | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H-serien
Virtuella datorer i H-serien är optimerade för program som drivs av höga CPU-frekvenser eller stora minnen per kärn krav. Virtuella datorer i H-serien 8 eller 16 Intel Xeon E5 2667 v3 processor kärnor, upp till 14 GB RAM-minne per CPU-kärna och ingen hyperthreading-funktion. En virtuell dator i H-serien ger upp till 80 GB/SEK minnes bandbredd.

ACU: 290–300

Premium Storage: stöds inte

Premium Storage caching: stöds inte

| Storlek | vCPU | Processor | Minne (GiB) | Minnes bandbredd GiB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GiB/s) | MPI-stöd | Temp-lagring (GiB) | Max antal datadiskar | Högsta Ethernet-nätverkskort |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1 000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> för MPI-program aktive ras dedicerade RDMA-backend-nätverk av FDR Infiniband-nätverket.

<br>
