---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ee145e5784ae6da6cce22f1b21f9a5d45335ea8b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz.  Den här artikeln innehåller information om antalet vCPUs, datadiskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

Azure H-serien virtuella datorer är senast inom högpresterande datorbearbetning VMs syftar till att avancerad beräkningar behov som molekylär utformning och beräkningsprogram. Dessa 8 och 16 vCPU VMs bygger på Intel Haswell E5 2667 V3 processor-teknik med DDR4 minne och SSD-baserad tillfällig lagring. 

Förutom den imponerande processorkraften erbjuder H-serien olika alternativ för RDMA-nätverk med låg fördröjning med FDR InfiniBand och flera minneskonfigurationer som ger stöd för minnesintensiva beräkningskrav.



## <a name="h-series"></a>H-serien

ACU: 290–300

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 × 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 × 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 × 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 × 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 × 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 × 500 |4 |

<sup>1</sup> MPI program dedikerade RDMA backend-nätverket aktiveras av FDR InfiniBand-nätverk, vilket ger extremt lägsta latens och hög bandbredd.

<br>



## <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser

ACU: 225

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 <sup>1</sup> |8 |56 |382 |32 |32 × 500 |2 |
| Standard_A9 <sup>1</sup> |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 × 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

<sup>1</sup>MPI program dedikerade RDMA backend-nätverket aktiveras av FDR InfiniBand-nätverk, vilket ger extremt lägsta latens och hög bandbredd.

<br>



