---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa9327bd0ba6763aa4e89630611aabb3c5195655
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85448729"
---
För närvarande har Ultra disks ytterligare begränsningar, de är följande:

De enda alternativen för infrastruktur redundans som för närvarande är tillgängliga för Ultra disks är tillgänglighets zoner. Virtuella datorer som använder andra alternativ för redundans kan inte ansluta en Ultra disk.

Följande tabell beskriver regionerna Ultra disks finns i, samt motsvarande tillgänglighets alternativ:

> [!NOTE]
> Om en region i följande lista inte har några tillgänglighets zoner med Ultra disk-kapacitet måste de virtuella datorerna i regionen distribueras utan några alternativ för replikering av infrastrukturen för att ansluta en Ultra disk.

|Regioner  |Antal tillgänglighets zoner som stöder Ultra disks  |
|---------|---------|
|US Gov, Virginia     |Ingen         |
|USA, södra centrala     |Ingen         |
|USA, centrala     |Tre zoner         |
|USA, västra     |Ingen         |
|USA, västra 2    |Tre zoner         |
|USA, östra     |Tre zoner         |
|USA, östra 2     |Två zoner         |
|Asien, Sydostasien     |Tre zoner         |
|Europa, norra     |Tre zoner          |
|Europa, västra     |Tre zoner          |
|Storbritannien, södra     |Tre zoner          |
|Japan, östra     |Två zoner         |
|Frankrike, centrala    |Två zoner        |


- Stöds endast i följande VM-serien:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Alla VM-storlekar är inte tillgängliga i alla regioner som stöds med Ultra disks
- Är bara tillgängliga som data diskar och stöder endast fysisk sektor storlek för 4K. På grund av den virtuella 4K-sektorns storlek på Ultra disk finns det vissa program som inte är kompatibla med Ultra disks. Ett exempel skulle vara Oracle Database, vilket kräver version 12,2 eller senare för att stödja Ultra disks.  
- Kan bara skapas som tomma diskar  
- Stöder för närvarande inte disk ögonblicks bilder, VM-avbildningar, tillgänglighets uppsättningar, Azure-dedikerade värdar eller Azure Disk Encryption
- Stöder för närvarande inte integrering med Azure Backup eller Azure Site Recovery
- Stöder endast icke-cachelagrade läsningar och icke-cachelagrade skrivningar
- Den aktuella maximala gränsen för IOPS på GA-VM: ar är 80 000.

Azure Ultra disks erbjuder upp till 16 TiB per region per prenumeration som standard, men Ultra disks stöder högre kapacitet på begäran. Kontakta Azure-supporten om du vill begära en ökning av kapaciteten.