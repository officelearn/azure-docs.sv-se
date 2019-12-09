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
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935809"
---
För närvarande har Ultra disks ytterligare begränsningar, de är följande:

- Stöds i följande regioner med ett varierande antal tillgänglighets zoner per region:
    - USA, östra 2
    - USA, östra
    - USA, västra 2
    - Asien, Sydostasien
    - Europa, norra
    - Europa, västra
    - Storbritannien, södra 
- Kan endast användas med tillgänglighets zoner (tillgänglighets uppsättningar och enskilda VM-distributioner utanför zoner kan inte ansluta en Ultra disk)
- Stöds endast i följande VM-serien:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Alla VM-storlekar är inte tillgängliga i alla regioner som stöds med Ultra disks
- Är bara tillgängliga som data diskar och stöder endast fysisk sektor storlek för 4K. På grund av den virtuella 4K-sektorns storlek på Ultra disk finns det vissa program som inte är kompatibla med Ultra disks. Ett exempel skulle vara Oracle Database, vilket kräver version 12,2 eller senare för att stödja Ultra disks.  
- Kan bara skapas som tomma diskar  
- Har ännu inte stöd för ögonblicks bilder av diskar, VM-avbildningar, tillgänglighets uppsättningar och Azure Disk Encryption
- Har ännu inte stöd för integrering med Azure Backup eller Azure Site Recovery
- Den aktuella maximala gränsen för IOPS på GA-VM: ar är 80 000.
- Om du vill delta i en begränsad för hands version av en virtuell dator som kan utföra 160 000 IOPS med Ultra disks, kan du e-posta UltraDiskFeedback@microsoft. com