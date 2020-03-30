---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334642"
---
Du kan koppla ett antal datadiskar till en virtuell Azure-dator. Baserat på skalbarhets- och prestandamålen för en virtuell dators datadiskar kan du bestämma antalet och typen av disk som du behöver för att uppfylla dina prestanda- och kapacitetskrav.

> [!IMPORTANT]
> För optimal prestanda, begränsa antalet högt utnyttjade diskar som är anslutna till den virtuella datorn för att undvika eventuell begränsning. Om alla anslutna diskar inte är mycket utnyttjade samtidigt kan den virtuella datorn stödja ett större antal diskar.

**För Azure-hanterade diskar:**

I följande tabell visas standard- och maximigränserna för antalet resurser per region per prenumeration. Det finns ingen gräns för antalet hanterade diskar, ögonblicksbilder och avbildningar per resursgrupp.  

> | Resurs | Gräns |
> | --- | --- |
> | Standardhanterade diskar | 50 000 |
> | Standardiserade SSD-hanterade diskar | 50 000 |
> | Premium-hanterade diskar | 50 000 |
> | Standard_LRS ögonblicksbilder | 50 000 |
> | Standard_ZRS ögonblicksbilder | 50 000 |
> | Hanterad bild | 50 000 |

* **För standardlagringskonton:** Ett standardlagringskonto har en maximal total begärandehastighet på 20 000 IOPS. Den totala IOPS-enheten för alla virtuella datordiskar i ett standardlagringskonto bör inte överskrida den här gränsen.
  
    Du kan grovt beräkna antalet diskar med högt använda data som stöds av ett enda standardlagringskonto baserat på gränsen för begärandehastighet. För en virtuell virtuell virtuell resurs på basic-nivå är till exempel det maximala antalet diskar med högt utnyttjade diskar cirka 66, vilket är 20 000/300 IOPS per disk. Det maximala antalet högt utnyttjade diskar för en virtuell standardnivå är cirka 40, vilket är 20 000/500 IOPS per disk. 

* **För Premium-lagringskonton:** Ett Premium-lagringskonto har en maximal total dataflödeshastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

