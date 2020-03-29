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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935809"
---
För nu, ultra diskar har ytterligare begränsningar, de är följande:

- Stöds i följande regioner, med varierande antal tillgänglighetszoner per region:
    - USA, östra 2
    - USA, östra
    - USA, västra 2
    - Sydostasien
    - Europa, norra
    - Europa, västra
    - Storbritannien, södra 
- Kan endast användas med tillgänglighetszoner (tillgänglighetsuppsättningar och enstaka VM-distributioner utanför zoner kommer inte att ha möjlighet att koppla en ultradisk)
- Stöds endast i följande VM-serie:
    - [ESv3 (på andra sätt)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3 (på andra sätt)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2 (på andra sätt)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 (på andra)](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Alla vm-storlekar är inte tillgängliga i alla regioner som stöds med ultradiskar
- Är endast tillgängliga som datadiskar och stöder endast 4k fysisk sektorstorlek. På grund av ultradiskens 4K-storlek i den inbyggda sektoren finns det vissa program som inte är kompatibla med ultradiskar. Ett exempel skulle vara Oracle Database, som kräver release 12.2 eller senare för att stödja ultra diskar.  
- Kan bara skapas som tomma diskar  
- Har ännu inte stöd för ögonblicksbilder av disk, VM-avbildningar, tillgänglighetsuppsättningar och Azure-diskkryptering
- Har ännu inte stöd för integrering med Azure Backup eller Azure Site Recovery
- Den nuvarande maximala gränsen för IOPS på virtuella ga-datorer är 80 000.
- Om du vill delta i en begränsad förhandsvisning av en virtuell dator som kan åstadkomma 160.000 IOPS med ultra diskar, vänligen e-post UltraDiskFeedback@microsoft .com