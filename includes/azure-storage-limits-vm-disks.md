---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "68669852"
---
Du kan koppla ett antal data diskar till en virtuell Azure-dator. Baserat på skalbarhets-och prestanda målen för en virtuell dators data diskar kan du fastställa det antal och den typ av disk som du behöver för att uppfylla dina prestanda-och kapacitets krav.

> [!IMPORTANT]
> För optimala prestanda begränsar du antalet mycket använda diskar som är anslutna till den virtuella datorn för att undvika möjlig begränsning. Om alla anslutna diskar inte används på samma gång kan den virtuella datorn stödja ett större antal diskar.

**För Azure Managed disks:**

I följande tabell visas standard-och Max gränserna för antalet resurser per region per prenumeration. Det finns ingen gräns för antalet Managed Disks, ögonblicks bilder och avbildningar per resurs grupp.  

> | Resurs | Standardgräns  | Övre gräns |
> | --- | --- | --- |
> | Standard Managed disks | 50 000 | 50 000 |
> | Standard SSD Managed disks | 50 000 | 50 000 |
> | Premium Managed disks | 50 000 | 50 000 |
> | Standard_LRS ögonblicks bilder | 50 000 | 50 000 |
> | Standard_ZRS ögonblicks bilder | 50 000 | 50 000 |
> | Hanterad avbildning | 50 000 | 50 000 |

* **För standard lagrings konton:** Ett standard lagrings konto har en högsta totala begär ande hastighet på 20 000 IOPS. Det totala antalet IOPS på alla virtuella dator diskar på ett standard lagrings konto bör inte överskrida den här gränsen.
  
    Du kan välja att beräkna antalet mycket använda diskar som stöds av ett enda standard lagrings konto baserat på gränsen för begär ande frekvens. För till exempel en virtuell dator med en grundläggande nivå är det högsta antalet mycket använda diskar som är cirka 66, vilket är 20000/300 IOPS per disk. Det maximala antalet mycket använda diskar för en virtuell dator på standard nivån är cirka 40, vilket är 20000/500 IOPS per disk. 

* **För Premium Storage-konton:** Ett Premium Storage-konto har en högsta totala data flödes hastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

