---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 2936fd318f08c74675f7e8b382c861f4a28319fc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261517"
---
Du kan koppla ett antal datadiskar till en Azure virtuell dator. Baserat på mål för skalbarhet och prestanda för en virtuell dators datadiskar kan bestämma du antalet och typen av disk som du behöver för att uppfylla dina kapacitetskrav på prestanda och.

> [!IMPORTANT]
> Begränsa antalet högutnyttjade diskar som är anslutna till den virtuella datorn för att undvika eventuell begränsning för optimala prestanda. Om alla anslutna diskar inte är används mycket på samma gång, har den virtuella datorn stöd för ett större antal diskar.

**Hanterade diskar för Azure:**

I följande tabell visas standardwebbplatsen och den gränsvärdena för antalet resurser per region per prenumeration

> | Resurs | Standardgräns  | Övre gräns |
> | --- | --- | --- |
> | Hanterade standarddiskar | 25,000 | 50,000 |
> | Hanterade Standard SSD-diskar | 25,000 | 50,000 |
> | Hanterade premiumdiskar | 25,000 | 50,000 |
> | Standard_LRS ögonblicksbilder | 25,000 | 50,000 |
> | Standard_ZRS ögonblicksbilder | 25,000 | 50,000 |
> | Hanterad avbildning | 25,000 | 50,000 |

* **För standardlagringskonton:** Ett standardlagringskonto har en högsta totala begärandefrekvens på 20 000 IOPS. Det totala antalet IOPS för alla virtuella diskar i ett standardlagringskonto bör inte överskrida den här gränsen.
  
    Du kan ungefär beräkna antalet högutnyttjade diskar som stöds av en enda standardlagringskonto baserat på gränsen för begärandehastigheten. För en grundläggande nivå VM är det maximala antalet högutnyttjade diskar som exempelvis cirka 66, vilket är 20 000/300 IOPS per disk. Det maximala antalet högutnyttjade diskar för en virtuell dator på standardnivå är cirka 40, vilket är 20 000/500 IOPS per disk. 

* **För Premium storage-konton:** Ett premiumlagringskonto har en högsta totala dataflödeshastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

