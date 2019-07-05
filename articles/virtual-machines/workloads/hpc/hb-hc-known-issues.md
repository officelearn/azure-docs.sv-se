---
title: Kända problem med HB-serien och HC-seriens virtuella datorer – Azure Virtual Machines | Microsoft Docs
description: Läs om kända problem med HB-seriens storlekar i Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: a41155b90257f7eaec85c3adbd975a0a37e24d91
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560406"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Kända problem med virtuella datorer i HB-serien och HC-serien

Den här artikeln innehåller de vanligaste problem och lösningar vid användning av HB-serien och HC-seriens virtuella datorer.

## <a name="dram-on-hb-series"></a>DRAM på HB-serien

HB-seriens virtuella datorer kan bara användas för att exponera 228 GB RAM för virtuella gästdatorer just nu. Detta beror på en känd begränsning av hypervisor som Azure för att förhindra sidor tilldelas till den lokala DRAM av AMD CCX'S (NUMA domäner) reserverade för den Virtuella gästdatorn.

## <a name="accelerated-networking"></a>Accelererat nätverk

Azure Accelererat nätverk är inte aktiverat just nu men kommer när vi går igenom utvärderingsperioden. Vi meddelar kunder när den här funktionen stöds.

## <a name="qp0-access-restriction"></a>qp0 åtkomstbegränsning

0 är inte tillgänglig för virtuella gästdatorer om du vill förhindra på låg nivå maskinvara åtkomst som kan leda till säkerhetsproblem, kö-par. Detta bör endast påverka åtgärder vanligtvis kopplade till administration av ConnectX-5-nätverkskortet och vissa InfiniBand diagnostikverktyg som ibdiagnet, men inte slutanvändare själva programmen körs.

## <a name="ud-transport"></a>UD-Transport

HB - och HC-serien stöder inte dynamiskt ansluten Transport (DCT) vid start. Stöd för DCT implementeras över tid. Tillförlitlig anslutning (RC) och otillförlitliga Datagram (UD) transporter stöds.

## <a name="gss-proxy"></a>GSS Proxy

GSS-Proxy har ett känt fel i CentOS/RHEL 7.5 som kan visas som en betydande prestanda och svarstider påföljd när det används med NFS. Detta kan undvikas med:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Rensa cache

På HPC-system är det ofta bra att rensa minnet när ett jobb har slutförts innan nästa användare tilldelas samma nod. När du har kört program i Linux kan vara att det tillgängliga minnet minskar när din bufferten minne ökar, trots att inte köra alla program.

![Skärmbild av Kommandotolken](./media/known-issues/cache-cleaning-1.png)

Med hjälp av `numactl -H` visar vilka NUMAnode(s) minnet buffras med (eventuellt alla). I Linux, användare rensa cacheminnen i tre sätt att returnera buffras eller cachelagrad minne till ”kostnadsfria”. Du måste vara en rot eller sudo-behörighet.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Skärmbild av Kommandotolken](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-varningar

Du kan se följande kernel varningsmeddelandena när du startar en HB-serien virtuell dator under Linux.

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

Du kan ignorera den här varningen. Detta beror på en känd begränsning i Azure hypervisor-programmet som kommer att åtgärdas med tiden.

## <a name="next-steps"></a>Nästa steg

Läs mer om [databehandling med höga prestanda](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
