---
title: Högpresterande datorsystem på virtuella datorer i H-serien – virtuella Azure-datorer
description: Lär dig mer om funktionerna i virtuella datorer i HPC i HPC-serien.
author: vermagit
ms.author: amverma
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.openlocfilehash: b3d5d003db89a11a013c3236a3afbe03ffe68557
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271025"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Högpresterande datorsystem på virtuella datorer i H-serien

HPC (High-Performance Computing) på virtuella datorer i HB-serien och HC-serien möjliggör den mest optimerade HPC-prestandan för alla virtuella datorer på Azure. HPC-optimerade virtuella datorer används för att lösa några av de svåraste matematiska problemen, till exempel: vätskedynamik, olje- och gassimuleringar och vädermodellering.

Den här artikeln innehåller några viktiga funktioner i virtuella datorer i HB-serien och HC-serien, varför dessa virtuella datorer presterar bra i HPC-scenarier och hur du kommer igång.

## <a name="features-and-capabilities"></a>Funktioner

HB-serien och HC-serien virtuella datorer är utformade för att ge bästa HPC-prestanda, MPI-skalbarhet (Message Passing Interface) och kostnadseffektivitet för HPC-arbetsbelastningar.

### <a name="message-passing-interface"></a>Gränssnitt för meddelandepassning

HB-serien och HC-serien stöder nästan alla MPI-typer och versioner. Några av de vanligaste MPI-typerna som stöds stöds är: OpenMPI, MVAPICH2, Platform MPI, Intel MPI och alla RDMA-verb (Remote Direct Memory Access). Mer information finns i [Konfigurera gränssnitt för meddelandepassning för HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA och InfiniBand

RDMA-gränssnittet är standard på virtuella datorer i HB-serien och HC-serien. RDMA-kompatibla instanser kommunicerar via ett InfiniBand-nätverk, som arbetar med förbättrade datahastigheter (EDR) för virtuella datorer i HB-serien och HC-serien. RDMA-kompatibla instanser kan öka skalbarheten och prestandan för vissa MPI-program.

InfiniBand-konfigurationen som stöder virtuella datorer i HB-serien och HC-serien är icke-blockerande fettträd med låg diameterdesign för konsekvent RDMA-prestanda.

Se [Aktivera InfiniBand](enable-infiniband.md) om du vill veta mer om hur du konfigurerar InfiniBand på dina virtuella datorer i HB-serien eller HC-serien.

## <a name="get-started"></a>Komma igång

Bestäm först vilken virtuell dator i H-serien som ska användas. Mer information om HPC-optimerade virtuella datorer finns i [översikt över HB-serien](hb-series-overview.md) och [översikt över HC-serien](hc-series-overview.md). Specifikationer finns i [Hög prestanda beräkna VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

När du har valt och skapat en virtuell dator för ditt program måste du konfigurera den genom att aktivera InfiniBand. Mer information om hur du aktiverar InfiniBand på både Virtuella Windows- och Linux-datorer finns i [Aktivera InfiniBand](enable-infiniband.md).

En viktig komponent i HPC-arbetsbelastningar är MPI. HB-serien och HC-serien stöder nästan alla MPI-typer och versioner. Mer information finns i [Konfigurera gränssnitt för meddelandepassning för HPC](setup-mpi.md).

När du har valt din VM-serie, konfigurera Infiniband och MPI, är du redo att börja bygga dina HPC-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över HB-serien](hb-series-overview.md) och [översikten över HC-serien](hc-series-overview.md) om du vill veta mer om viktiga skillnader och specifikationer.

- För en högre nivå, arkitektur vy av att köra HPC-arbetsbelastningar, se [Hög prestanda Computing (HPC) på Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
