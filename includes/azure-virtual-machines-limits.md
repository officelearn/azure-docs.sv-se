---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554056"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Virtuella datorer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per molntjänst<sup>1</sup> |50 |50 |
| Ange slutpunkter per molntjänst<sup>2</sup> |150 |150 |

<sup>1</sup>virtuella datorer som skapats med hjälp av den klassiska distributionsmodellen i stället för Azure Resource Manager lagras automatiskt i en molntjänst. Du kan lägga till fler virtuella datorer till Molntjänsten för Utjämning av nätverksbelastning och tillgänglighet. 

<sup>2</sup>indata slutpunkter gör det möjligt för kommunikation till en virtuell dator från utanför den virtuella datorns tjänst i molnet. Virtuella datorer i samma molntjänst eller virtuella nätverk automatiskt kan kommunicera med varandra. Mer information finns i [så här ställer du in slutpunkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
