---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335019"
---
| Resurs | Gräns |
| --- | --- |
| [Virtuella datorer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per moln tjänst<sup>1</sup> |50 |
| Slut punkter för ingångar per moln tjänst<sup>2</sup> |150 |

<sup>1</sup> Virtuella datorer som skapats med den klassiska distributions modellen i stället för Azure Resource Manager lagras automatiskt i en moln tjänst. Du kan lägga till fler virtuella datorer i moln tjänsten för belastnings utjämning och tillgänglighet. 

<sup>2</sup> Med ingångs slut punkter kan kommunikation till en virtuell dator utanför den virtuella datorns moln tjänst. Virtuella datorer i samma moln tjänst eller virtuella nätverk kan kommunicera automatiskt med varandra. Mer information finns i [så här konfigurerar du slut punkter till en virtuell dator](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
