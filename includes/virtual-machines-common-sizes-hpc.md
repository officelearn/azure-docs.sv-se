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
ms.openlocfilehash: c0383fd2ca348cd69f07ed61a7935e4fec7999b9
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538026"
---
Azure H-serien virtuella datorer (VM) är utformade för att leverera prestanda för ledarskap klassen, MPI skalbarhet och kostnadseffektivitet för en mängd verklig HPC-arbetsbelastningar.

Virtuella datorer i HB-serien är optimerade för program som drivs med minnesbandbredd, som vätskedynamik, explicit analys av finita element och vädermodellering. Virtuella HB-datorer har 60 AMD EPYC 7551-processorkärnor, 4 GB RAM per CPU-kärna och ingen hypertrådning. AMD EPYC-plattformen tillhandahåller minnesbandbredd på över 260 GB/sek.

Virtuella datorer HC-serien är optimerade för program som styrs av kompakta beräkning, som implicit begränsad elementanalys, molekylär dynamics och databaserad kemi. Virtuella HC-datorer har 44 Intel Xeon Platinum 8168-processorkärnor, 8 GB RAM per CPU-kärna och ingen hypertrådning. Intel Xeon Platina-plattformen stöder Intels omfattande ekosystem av verktyg, till exempel Intel Math Kernel-biblioteket.

Både HB och HC virtuella datorer har 100 Gb/sek Mellanox EDR InfiniBand i en icke-blockerande fat trädet konfiguration för konsekvent RDMA-prestanda. HB och HC virtuella datorer stöder standard Mellanox/OFED drivrutiner så att alla MPI-typer och versioner samt RDMA verb, stöds också.

Virtuella datorer i H-serien är optimerade för program som styrs av hög CPU-frekvenser eller stort minne per kärna-krav. H-serien virtuella datorer funktionen 8 eller 16 Intel Xeon E5-2667 v3-processor-kärnor, 7 eller 14 GB RAM-minne per CPU-kärna och inga hypertrådning. H-serien har 56 Gb/sek Mellanox FDR InfiniBand i en icke-blockerande fat trädet konfiguration för konsekvent RDMA-prestanda. Virtuella datorer i H-serien stöder Intel MPI 5.x och MS-MPI.

## <a name="hb-series"></a>HB-serien

ACU: 199-216

Premium-lagring: Stöds

Premium Storage Caching: Stöds

| Size | Virtuell processor | Processor | Minne (GB) | Minnesbandbredd GB/s | Grundläggande CPU-frekvens (GHz) | Alla kärnor frekvens (GHz, högsta) | Enkel kärna frekvens (GHz, högsta) | RDMA-prestanda (Gb/s) | MPI-stöd | Temporär lagring (GB) | Maximalt antal datadiskar | Max Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Alla | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC-serien

ACU: 297-315

Premium-lagring: Stöds

Premium Storage Caching: Stöds


| Size | Virtuell processor | Processor | Minne (GB) | Minnesbandbredd GB/s | Grundläggande CPU-frekvens (GHz) | Alla kärnor frekvens (GHz, högsta) | Enkel kärna frekvens (GHz, högsta) | RDMA-prestanda (Gb/s) | MPI-stöd | Temporär lagring (GB) | Maximalt antal datadiskar | Max Ethernet-nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Alla | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H-serien

ACU: 290-300

Premium-lagring:  Stöds inte

Premium Storage Caching:  Stöds inte

| Size | Virtuell processor | Processor | Minne (GB) | Minnesbandbredd GB/s | Grundläggande CPU-frekvens (GHz) | Alla kärnor frekvens (GHz, högsta) | Enkel kärna frekvens (GHz, högsta) | RDMA-prestanda (Gb/s) | MPI-stöd | Temporär lagring (GB) | Maximalt antal datadiskar | Max Ethernet-nätverkskort |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon® E5-2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon® E5-2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon® E5-2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon® E5-2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon® E5-2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon® E5-2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> för MPI-program, dedikerade RDMA backend-nätverket är aktiverat som FDR InfiniBand-nätverk.

<br>
