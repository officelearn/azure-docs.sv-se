---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334755"
---
| Resurs | Gräns |
| --- | --- |
| [Webb- eller arbetsroller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Instansinmatningsslutpunkter](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribution |25 |
| [Indataslutpunkter](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribution |25 |
| [Interna slutpunkter](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribution |25 |
| [Värdbaserade tjänstcertifikat](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribution |199 |

<sup>1.</sup> Varje Azure Cloud Service med webb- eller arbetsroller kan ha två distributioner, en för produktion och en för mellanlagring. Den här gränsen avser antalet olika roller, det vill än konfiguration. Den här gränsen refererar inte till antalet instanser per roll, det vill än skalning.

