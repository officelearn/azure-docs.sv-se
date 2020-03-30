---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335019"
---
| Resurs | Gräns |
| --- | --- |
| [Virtuella datorer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per molntjänst<sup>1</sup> |50 |
| Ingångsslutpunkter per molntjänst<sup>2</sup> |150 |

<sup>1.</sup> Virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen i stället för Azure Resource Manager lagras automatiskt i en molntjänst. Du kan lägga till fler virtuella datorer till molntjänsten för belastningsutjämning och tillgänglighet. 

<sup>2.</sup> Ingångsslutpunkter tillåter kommunikation till en virtuell dator utanför den virtuella datorns molntjänst. Virtuella datorer i samma molntjänst eller virtuella nätverk kan automatiskt kommunicera med varandra. Mer information finns i [Så här konfigurerar du slutpunkter på en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
