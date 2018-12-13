---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886332"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Web/worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Instans-slutpunkter för indata](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) per distribution |25 |25 |
| [Ange slutpunkter](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) per distribution |25 |25 |
| [Interna slutpunkter](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) per distribution |25 |25 |
| [Värdbaserade tjänstcertifikat](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribution |199 |199 |

<sup>1</sup>varje molntjänst med Web/Worker-roller kan ha två distributioner, en för produktion och en för mellanlagring. Tänk också på att den här gränsen avser antalet distinkta roller (konfiguration) och inte antalet instanser per roll (skala).

