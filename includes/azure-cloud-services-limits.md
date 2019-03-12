---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553989"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Webb- eller worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Instans inkommande slutpunkter](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) per distribution |25 |25 |
| [Ange slutpunkter](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) per distribution |25 |25 |
| [Interna slutpunkter](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) per distribution |25 |25 |
| [Värdbaserade tjänstcertifikat](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribution |199 |199 |

<sup>1</sup>varje Azure-molntjänst med web- eller worker-roller kan ha två distributioner, en för produktion och en för mellanlagring. Den här gränsen syftar på hur många av olika roller, det vill säga konfiguration. Den här gränsen avser inte antalet instanser per roll, det vill säga skalning.

