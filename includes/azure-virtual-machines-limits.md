---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2019
ms.locfileid: "67187701"
---
| Resource | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Virtuella datorer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per moln tjänst<sup>1</sup> |50 |50 |
| Slut punkter för ingångar per moln tjänst<sup>2</sup> |150 |150 |

<sup>1</sup> Virtuella datorer som skapats med den klassiska distributions modellen i stället för Azure Resource Manager lagras automatiskt i en moln tjänst. Du kan lägga till fler virtuella datorer i moln tjänsten för belastnings utjämning och tillgänglighet. 

<sup>2</sup> Med ingångs slut punkter kan kommunikation till en virtuell dator utanför den virtuella datorns moln tjänst. Virtuella datorer i samma moln tjänst eller virtuella nätverk kan kommunicera automatiskt med varandra. Mer information finns i [så här konfigurerar du slut punkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
