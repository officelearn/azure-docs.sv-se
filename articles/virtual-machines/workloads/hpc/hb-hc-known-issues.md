---
title: Kända problem med virtuella datorer i HB-serien och HC-serien – Azure Virtual Machines | Microsoft Docs
description: Läs om kända problem med VM-storlekar i HB-serien i Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707785"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Kända problem med virtuella datorer i HB-serien och HC-serien

Den här artikeln innehåller de vanligaste problemen och lösningarna när du använder virtuella datorer i HB-serien och HC-serien.

## <a name="dram-on-hb-series"></a>DRAM på HB-serien

Virtuella datorer i HB-serien kan bara exponera 228 GB RAM-minne för virtuella gäst datorer för tillfället. Detta beror på en känd begränsning i Azure hypervisor för att förhindra att sidor tilldelas till det lokala DRAM av AMD CCX (NUMA-domäner) som är reserverade för den virtuella gäst datorn.

## <a name="accelerated-networking"></a>Accelererat nätverk

Azure-accelererat nätverk är inte aktiverat för tillfället, men det kommer att förloppet i för hands perioden. Vi kommer att meddela kunder när den här funktionen stöds.

## <a name="qp0-access-restriction"></a>Åtkomst begränsning för qp0

För att förhindra maskin vara på låg nivå som kan leda till säkerhets risker, är Queue-paret 0 inte tillgängligt för virtuella gäst datorer. Detta bör endast påverka åtgärder som vanligt vis är kopplade till administration av ConnectX-5-NÄTVERKSKORTet och som kör en viss InfiniBand-diagnostik som ibdiagnet, men inte slutanvändarens program.

## <a name="ud-transport"></a>UD-transport

Vid lanseringen stöder HB-och HC-serien inte dynamiskt ansluten transport (DCT). Stöd för DCT kommer att implementeras över tid. Det finns stöd för pålitliga anslutnings anslutningar (RC) och otillförlitliga datagram (UD).

## <a name="gss-proxy"></a>GSS-proxy

GSS proxy har en känd bugg i CentOS/RHEL 7,5 som kan identifiera en betydande prestanda och svars tid när den används med NFS. Detta kan minimeras med:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Rensning av cache

På HPC-system är det ofta användbart att rensa minnet när ett jobb har avslut ATS innan nästa användare tilldelas samma nod. När du har kört program i Linux kan du se att det tillgängliga minnet minskar medan buffertutrymme ökar, trots att inga program körs.

![Skärm bild av kommando tolken](./media/known-issues/cache-cleaning-1.png)

Om `numactl -H` du använder visas vilka NUMAnode som minnet buffras med (möjligen alla). I Linux kan användare rensa cacheminnen på tre sätt för att returnera buffrat eller cachelagrat minne till ledigt. Du måste vara rot eller ha sudo-behörigheter.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Skärm bild av kommando tolken](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-varningar

Du kan se följande kernel-varnings meddelanden när du startar en virtuell dator med HB-serien under Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Du kan ignorera den här varningen. Detta beror på en känd begränsning i Azure-hypervisorn som kommer att åtgärdas över tid.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [data behandling med höga prestanda](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
