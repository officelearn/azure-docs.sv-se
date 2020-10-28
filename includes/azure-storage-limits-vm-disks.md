---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755680"
---
Du kan koppla ett antal data diskar till en virtuell Azure-dator. Baserat på skalbarhets-och prestanda målen för en virtuell dators data diskar kan du fastställa det antal och den typ av disk som du behöver för att uppfylla dina prestanda-och kapacitets krav.

> [!IMPORTANT]
> För optimala prestanda begränsar du antalet mycket använda diskar som är anslutna till den virtuella datorn för att undvika möjlig begränsning. Om alla anslutna diskar inte används på samma gång kan den virtuella datorn stödja ett större antal diskar.

**För Azure Managed disks:**

I följande tabell visas standard-och Max gränserna för antalet resurser per region per prenumeration. Gränserna förblir desamma oavsett diskar som har krypterats med antingen plattforms hanterade nycklar eller Kundhanterade nycklar. Det finns ingen gräns för antalet Managed Disks, ögonblicks bilder och avbildningar per resurs grupp.  

> | Resurs | Gräns |
> | --- | --- |
> | Standard Managed disks | 50 000 |
> | Standard SSD Managed disks | 50 000 |
> | Premium Managed disks | 50 000 |
> | Standard_LRS ögonblicks bilder | 50 000 |
> | Standard_ZRS ögonblicks bilder | 50 000 |
> | Hanterad avbildning | 50 000 |

**För standard lagrings konton:** Ett standard lagrings konto har en högsta totala begär ande hastighet på 20 000 IOPS. Det totala antalet IOPS på alla virtuella dator diskar på ett standard lagrings konto bör inte överskrida den här gränsen.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**För Premium Storage-konton:** Ett Premium Storage-konto har en högsta totala data flödes hastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

