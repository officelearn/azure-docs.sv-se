---
title: Högpresterande datoranvändning – virtuella Azure-datorer | Microsoft-dokument
description: Lär dig mer om högpresterande datoranvändning på Azure.
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
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707813"
---
# <a name="optimization-for-linux"></a>Optimering för Linux

Den här artikeln visar några viktiga tekniker för att optimera din OS-avbildning. Läs mer om [hur du aktiverar InfiniBand](enable-infiniband.md) och optimerar OS-avbildningarna.

## <a name="update-lis"></a>Uppdatera LIS

Om du distribuerar med en anpassad avbildning (till exempel ett äldre operativsystem som CentOS/RHEL 7.4 eller 7.5) uppdaterar du LIS på den virtuella datorn.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Återta minne

Förbättra effektiviteten genom att automatiskt hämta minne för att undvika åtkomst till fjärrminne.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Så här kvarstår detta efter att den virtuella datorn har startats om:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Inaktivera brandvägg och SELinux

Inaktivera brandvägg och SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Inaktivera cpupower

Inaktivera cpu-kraft.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur du aktiverar InfiniBand](enable-infiniband.md) och optimerar OS-bilder.

* Läs mer om [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) på Azure.
