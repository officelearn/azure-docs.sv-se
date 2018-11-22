---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 7e390e2134df02b0ca9c0d1752c3207aff7b9314
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279964"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Virtuella datorer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per molntjänst<sup>1</sup> |50 |50 |
| Ange slutpunkter per molntjänst<sup>2</sup> |150 |150 |

<sup>1</sup>virtuella datorer som skapats i Service Management (i stället för Resource Manager) lagras automatiskt i en molntjänst. Du kan lägga till fler virtuella datorer till Molntjänsten för Utjämning av nätverksbelastning och tillgänglighet. 

<sup>2</sup>indata slutpunkter gör det möjligt för kommunikation till en virtuell dator från utanför den virtuella datorns tjänst i molnet. Virtuella datorer i samma molntjänst eller virtuella nätverk automatiskt kan kommunicera med varandra. Se [så här konfigurerar du slutpunkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
