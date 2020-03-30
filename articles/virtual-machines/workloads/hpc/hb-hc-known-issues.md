---
title: Kända problem med virtuella datorer i HB-serien och HC-serien – virtuella Azure-datorer | Microsoft-dokument
description: Lär dig mer om kända problem med VM-storlekar i HB-serien i Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707785"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Kända problem med virtuella datorer i HB-serien och HC-serien

Den här artikeln innehåller de vanligaste problemen och lösningarna när du använder virtuella datorer i HB-serien och HC-serien.

## <a name="dram-on-hb-series"></a>DRAM på HB-serien

HB-serien virtuella datorer kan bara exponera 228 GB RAM för gäst virtuella datorer just nu. Detta beror på en känd begränsning av Azure hypervisor för att förhindra att sidor tilldelas till den lokala DRAM för AMD CCX(NUMA-domäner) som är reserverade för gästdatorn.

## <a name="accelerated-networking"></a>Accelererat nätverk

Azure Accelerated Networking är inte aktiverat just nu, men kommer när vi går igenom förhandsgranskningsperioden. Vi meddelar kunderna när den här funktionen stöds.

## <a name="qp0-access-restriction"></a>qp0 Åtkomstbegränsning

För att förhindra maskinvaruåtkomst på låg nivå som kan leda till säkerhetsproblem är köpar 0 inte tillgängligt för gäst-virtuella datorer. Detta bör endast påverka åtgärder som vanligtvis är associerade med administration av ConnectX-5-nätverkskortet och köra vissa InfiniBand-diagnostik som ibdiagnet, men inte själva slutanvändarprogrammen.

## <a name="ud-transport"></a>UD Transport

Vid lanseringen stöder HB- och HC-serien inte dynamiskt ansluten transport (DCT). Stöd för DCT kommer att genomföras med tiden. Reliable Connection (RC) och Unreliable Datagram (UD) transporter stöds.

## <a name="gss-proxy"></a>GSS-proxy

GSS Proxy har en känd bugg i CentOS/RHEL 7.5 som kan manifesteras som en betydande prestanda och svarstider straff när den används med NFS. Detta kan mildras med:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Cache rengöring

På HPC-system är det ofta användbart att rensa minnet när ett jobb har slutförts innan nästa användare tilldelas samma nod. Efter att ha kört program i Linux kan det hända att det tillgängliga minnet minskar medan buffertminnet ökar, trots att du inte kör några program.

![Skärmbild av kommandotolken](./media/known-issues/cache-cleaning-1.png)

Med `numactl -H` hjälp visas vilka NUMAnode(s) minnet buffras med (eventuellt alla). I Linux kan användare rensa cacheminnena på tre sätt för att returnera buffrat eller cachelagrat minne till "gratis". Du måste vara root eller har sudo behörigheter.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Skärmbild av kommandotolken](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Varningar för kärna

Följande kernel-varningsmeddelanden kan visas när du startar en virtuell HB-serie under Linux.

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

Du kan ignorera den här varningen. Detta beror på en känd begränsning av Azure hypervisor som kommer att åtgärdas över tiden.

## <a name="next-steps"></a>Nästa steg

Läs mer om [högpresterande datoranvändning](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) i Azure.
