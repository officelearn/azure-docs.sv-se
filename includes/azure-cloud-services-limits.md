---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 3295c7e677e4c9bf7bff27614fe5fa4fa3200570
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334755"
---
| Resurs | Gräns |
| --- | --- |
| [Web-eller Worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Slut punkter för instans ingång](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribution |25 |
| [Slut punkter för ingångar](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribution |25 |
| [Interna slut punkter](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribution |25 |
| [Värdbaserade tjänst certifikat](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribution |199 |

<sup>1</sup> Varje Azure-Molntjänster med Web-eller Worker-roller kan ha två distributioner, en för produktion och en för mellanlagring. Den här gränsen avser antalet distinkta roller, det vill säga konfigurationen. Den här gränsen refererar inte till antalet instanser per roll, det vill säga skalning.

