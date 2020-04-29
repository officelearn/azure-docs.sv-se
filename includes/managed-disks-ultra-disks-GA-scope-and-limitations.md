---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008661"
---
För närvarande har Ultra disks ytterligare begränsningar, de är följande:

De enda alternativen för infrastruktur redundans som för närvarande är tillgängliga för Ultra disks är tillgänglighets zoner. Virtuella datorer som använder andra alternativ för redundans kan inte ansluta en Ultra disk.

Följande tabell beskriver regionerna Ultra disks finns i, samt motsvarande tillgänglighets alternativ:

> [!NOTE]
> Vissa tillgänglighets zoner i dessa regioner erbjuder inte Ultra disks.

|Regioner  |Ingen redundans för infrastruktur  |Tillgänglighetszoner  |
|---------|---------|---------|
|USA, västra     |Ja         |Nej         |
|USA, västra 2    |Nej         |Ja         |
|USA, östra     |Nej         |Ja         |
|USA, östra 2     |Nej         |Ja         |
|Asien, Sydostasien     |Nej         |Ja         |
|Europa, norra     |Nej         |Ja         |
|Europa, västra     |Nej         |Ja         |
|Storbritannien, södra     |Nej         |Ja         |

- Stöds endast i följande VM-serien:
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Alla VM-storlekar är inte tillgängliga i alla regioner som stöds med Ultra disks
- Är bara tillgängliga som data diskar och stöder endast fysisk sektor storlek för 4K. På grund av den virtuella 4K-sektorns storlek på Ultra disk finns det vissa program som inte är kompatibla med Ultra disks. Ett exempel skulle vara Oracle Database, vilket kräver version 12,2 eller senare för att stödja Ultra disks.  
- Kan bara skapas som tomma diskar  
- Stöder för närvarande inte disk ögonblicks bilder, VM-avbildningar, tillgänglighets uppsättningar, Azure-dedikerade värdar eller Azure Disk Encryption
- Stöder för närvarande inte integrering med Azure Backup eller Azure Site Recovery
- Den aktuella maximala gränsen för IOPS på GA-VM: ar är 80 000.

Azure Ultra disks erbjuder upp till 16 TiB per region per prenumeration som standard, men Ultra disks stöder högre kapacitet på begäran. Kontakta Azure-supporten om du vill begära en ökning av kapaciteten.