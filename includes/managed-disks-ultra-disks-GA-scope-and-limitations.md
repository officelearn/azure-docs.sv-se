---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: df488c2a3033015175943f7d092beaaa41e34046
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260892"
---
För närvarande har Ultra disks ytterligare begränsningar, de är följande:

- Stöds i följande regioner med ett varierande antal tillgänglighets zoner per region:
    - USA, östra 2
    - East US
    - Västra USA 2
    - Asien, Sydostasien
    - Norra Europa
    - Västra Europa
    - Storbritannien, södra 
- Kan endast användas med tillgänglighets zoner (tillgänglighets uppsättningar och enskilda VM-distributioner utanför zoner kan inte ansluta en Ultra disk)
- Stöds endast i följande VM-serien:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Alla VM-storlekar är inte tillgängliga i alla regioner som stöds med Ultra disks.
- Är bara tillgängliga som data diskar och stöder endast fysisk sektor storlek för 4K  
- Kan bara skapas som tomma diskar  
- Har ännu inte stöd för ögonblicks bilder av diskar, VM-avbildningar, tillgänglighets uppsättningar och Azure Disk Encryption
- Har ännu inte stöd för integrering med Azure Backup eller Azure Site Recovery
- Den aktuella maximala gränsen för IOPS på GA-VM: ar är 80 000.
- Om du vill delta i en begränsad för hands version av en virtuell dator som kan utföra 160 000 IOPS med Ultra disks, kan du e-posta UltraDiskFeedback@microsoft. com