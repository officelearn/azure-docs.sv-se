---
title: Högpresterande data på virtuella datorer i H-serien – Azure Virtual Machines | Microsoft Docs
description: Läs mer om funktioner och egenskaper i H-serien virtuella datorer som optimerats för HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800066"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Högpresterande data på virtuella datorer i H-serien

Högpresterande Datorsystem på HB-serien och HC-seriens virtuella datorer kan de mest optimerade HPC-prestandan på virtuella datorer på Azure. HPC optimerade virtuella datorer används för att lösa några av de svåraste matematiska problem som: fluiddynamik, olja och gas simuleringar och vädermodellering.

Den här artikeln beskriver några viktiga funktioner i HB-serien och HC-seriens virtuella datorer, varför dessa virtuella datorer fungerar väl med HPC-scenarier och hur du kommer igång.

## <a name="features-and-capabilities"></a>Funktioner och egenskaper

HB-serien och virtuella datorer HC-serien är utformad för att ge bästa HPC prestanda message passing interface (MPI) skalbarhet och kostnadseffektivitet för HPC-arbetsbelastningar.

### <a name="message-passing-interface"></a>Meddelandet passerar gränssnittet

HB- och HC-serien stöder nästan alla typer av MPI och versioner. Några av de vanliga, stöds MPI typer är: Åtkomst till (RDMA)-verb OpenMPI, MVAPICH2, plattform MPI, Intel MPI och alla direkt fjärråtkomst till minne. Mer information finns i [konfigurera Message Passing Interface för HPC](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA and InfiniBand

RDMA-gränssnitt är standard på HB-serien och HC-seriens virtuella datorer. RDMA-kompatibla förekomster kommunicerar via ett InfiniBand-nätverk fungerar enligt priserna för datalagringen (EDR) för HB- och HC-serien virtuella datorer. RDMA-kompatibla förekomster kan öka skalbarheten och prestandan i vissa MPI-program.

InfiniBand-konfigurationen stöder HB-serien och virtuella datorer HC-serien är icke-blockerande fat träd med en låg diameter design för RDMA.

Se [aktivera InfiniBand](enable-infiniband.md) mer information om hur du konfigurerar InfiniBand på HB-serien eller HC-seriens virtuella datorer.

## <a name="get-started"></a>Kom igång

Bestäm först vilken H-serien virtuell dator som du ska använda. Information om HPC optimerade virtuella datorer, finns i [översikt över HB serie](hb-series-overview.md) och [HC-serien översikt](hc-series-overview.md). Specifikationer, se [högpresterande compute VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

När du har valt och skapat en virtuell dator för ditt program, måste du konfigurera genom att aktivera InfiniBand. Läs hur du aktiverar InfiniBand på både Windows och Linux-datorer i [aktivera InfiniBand](enable-infiniband.md).

En kritisk komponent för HPC-arbetsbelastningar är MPI. HB- och HC-serien stöder nästan alla typer av MPI och versioner. Mer information finns i [konfigurera Message Passing Interface för HPC](setup-mpi.md).

När du har valt VM-serien, ställa in Infiniband och MPI, är du redo att börja bygga ditt HPC-arbetsbelastningar.

## <a name="next-steps"></a>Nästa steg

- Granska den [översikt över HB serie](hb-series-overview.md) och [HC-serien översikt](hc-series-overview.md) och se hur viktiga skillnader och specifikationer.

- En högre nivå, arkitektoniska vy över kör HPC-arbetsbelastningar, finns i [HPC High Performance Computing () på Azure](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
