---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 36d59638984c31a98067b95fded9626f31a7f278
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "34669365"
---
Azure virtuella datorer i H-serien är senast inom databehandling med höga prestanda virtuella datorer som riktar sig till bearbetningsbehov, som molekylär modellering och beräkningsströmningsdynamik. Dessa 8 och 16 vCPU virtuella datorer är baserade på Intel Haswell E5-2667 V3-processor teknik DDR4-minne och SSD-baserad temporär lagring. 

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

<sup>1</sup> för MPI-program, dedikerade RDMA backend-nätverket är aktiverat som FDR InfiniBand-nätverk, som har extremt korta svarstider och hög bandbredd.

<br>






