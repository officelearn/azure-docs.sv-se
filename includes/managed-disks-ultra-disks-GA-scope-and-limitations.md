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
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008661"
---
För nu, ultra diskar har ytterligare begränsningar, de är följande:

De enda alternativ för redundans för infrastruktur som för närvarande är tillgängliga för ultradiskar är tillgänglighetszoner. Virtuella datorer som använder andra redundansalternativ kan inte ansluta en ultradisk.

I följande tabell beskrivs de regioner som ultradiskar är tillgängliga i, samt motsvarande tillgänglighetsalternativ:

> [!NOTE]
> Vissa tillgänglighetszon inom dessa regioner erbjuder inte ultradiskar.

|Regioner  |Ingen infrastruktur redundans  |Tillgänglighetszoner  |
|---------|---------|---------|
|USA, västra     |Ja         |Inga         |
|USA, västra 2    |Inga         |Ja         |
|USA, östra     |Inga         |Ja         |
|USA, östra 2     |Inga         |Ja         |
|Sydostasien     |Inga         |Ja         |
|Europa, norra     |Inga         |Ja         |
|Europa, västra     |Inga         |Ja         |
|Storbritannien, södra     |Inga         |Ja         |

- Stöds endast i följande VM-serie:
    - [ESv3 (på andra sätt)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3 (på andra sätt)](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2 (på andra sätt)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2 (på andra)](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Alla vm-storlekar är inte tillgängliga i alla regioner som stöds med ultradiskar
- Är endast tillgängliga som datadiskar och stöder endast 4k fysisk sektorstorlek. På grund av ultradiskens 4K-storlek i den inbyggda sektoren finns det vissa program som inte är kompatibla med ultradiskar. Ett exempel skulle vara Oracle Database, som kräver release 12.2 eller senare för att stödja ultra diskar.  
- Kan bara skapas som tomma diskar  
- Stöder för närvarande inte ögonblicksbilder av disk, VM-avbildningar, tillgänglighetsuppsättningar, Azure Dedicated Hosts eller Azure-diskkryptering
- Stöder för närvarande inte integrering med Azure Backup eller Azure Site Recovery
- Den nuvarande maximala gränsen för IOPS på virtuella ga-datorer är 80 000.

Azure ultra diskar erbjuder upp till 16 TiB per region per prenumeration som standard, men ultra diskar stöder högre kapacitet på begäran. Om du vill begära en kapacitetsökning kontaktar du Azure Support.