---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c847b110cb821ebe3ca7e706fcaec592dcc58fa8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73600978"
---
För närvarande har Ultra disks ytterligare begränsningar, de är följande:

- Stöds i följande regioner med ett varierande antal tillgänglighets zoner per region:
    - USA, östra 2
    - Östra USA
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