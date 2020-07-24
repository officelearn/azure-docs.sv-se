---
title: Data behandling med höga prestanda – Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om data behandling med höga prestanda i Azure.
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
ms.openlocfilehash: 723419b97dc024a700d860dd3fe61ff48073a587
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87020006"
---
# <a name="optimization-for-linux"></a>Optimering för Linux

Den här artikeln visar några viktiga metoder för att optimera din operativ system avbildning. Läs mer om hur du [aktiverar InfiniBand](enable-infiniband.md) och OPTIMERAr OS-avbildningarna.

## <a name="update-lis"></a>Uppdatera LIS

Om du distribuerar med en anpassad avbildning (till exempel ett äldre operativ system som CentOS/RHEL 7,4 eller 7,5) uppdaterar du LIS på den virtuella datorn.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Frigör minne

Förbättra effektiviteten genom att automatiskt frigöra minne för att undvika åtkomst till fjärrminne.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Så här gör du detta kvar efter omstarter av virtuella datorer:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Inaktivera brand Väggs-och SELinux

Inaktivera brand Väggs-och SELinux.

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

Inaktivera cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du [aktiverar InfiniBand](enable-infiniband.md) och OPTIMERAr OS-avbildningar.

* Lär dig mer om [HPC](/azure/architecture/topics/high-performance-computing/) i Azure.
