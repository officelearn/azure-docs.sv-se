---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59805215"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Webb- eller worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Instans inkommande slutpunkter](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribution |25 |25 |
| [Ange slutpunkter](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribution |25 |25 |
| [Interna slutpunkter](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribution |25 |25 |
| [Värdbaserade tjänstcertifikat](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribution |199 |199 |

<sup>1</sup>varje Azure-molntjänst med web- eller worker-roller kan ha två distributioner, en för produktion och en för mellanlagring. Den här gränsen syftar på hur många av olika roller, det vill säga konfiguration. Den här gränsen avser inte antalet instanser per roll, det vill säga skalning.

