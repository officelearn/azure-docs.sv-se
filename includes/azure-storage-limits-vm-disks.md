---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 09b4f94db3464943a8367bfb3ca89f9a88446193
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554181"
---
Du kan koppla ett antal datadiskar till en Azure virtuell dator. Baserat på mål för skalbarhet och prestanda för en virtuell dators datadiskar kan bestämma du antalet och typen av disk som du behöver för att uppfylla dina kapacitetskrav på prestanda och.

> [!IMPORTANT]
> Begränsa antalet högutnyttjade diskar som är anslutna till den virtuella datorn för att undvika eventuell begränsning för optimala prestanda. Om alla anslutna diskar inte är används mycket på samma gång, har den virtuella datorn stöd för ett större antal diskar.

* **Hanterade diskar för Azure:** 

> | Resurs | Standardgräns | Övre gräns |
> | --- | --- | --- |
> | Hanterade standarddiskar | 10 000 | 50,000 |
> | Hanterade Standard SSD-diskar | 10 000 | 50,000 |
> | Hanterade premiumdiskar | 10 000 | 50,000 |
> | Standard_LRS ögonblicksbilder | 10 000 | 50,000 |
> | Standard_ZRS ögonblicksbilder | 10 000 | 50,000 |
> | Hanterad avbildning | 10 000 | 50,000 |

* **För standardlagringskonton:** Ett standardlagringskonto har en högsta totala begärandefrekvens på 20 000 IOPS. Det totala antalet IOPS för alla virtuella diskar i ett standardlagringskonto bör inte överskrida den här gränsen.
  
    Du kan ungefär beräkna antalet högutnyttjade diskar som stöds av en enda standardlagringskonto baserat på gränsen för begärandehastigheten. För en grundläggande nivå VM är det maximala antalet högutnyttjade diskar som exempelvis cirka 66, vilket är 20 000/300 IOPS per disk. Det maximala antalet högutnyttjade diskar för en virtuell dator på standardnivå är cirka 40, vilket är 20 000/500 IOPS per disk. 

* **För Premium storage-konton:** Ett premiumlagringskonto har en högsta totala dataflödeshastighet på 50 Gbit/s. Det totala dataflödet på alla virtuella datordiskar bör inte överskrida den här gränsen.

