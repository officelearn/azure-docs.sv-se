---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 24e327a0b669df247d44e4bf8f05b693abb49990
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224348"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Web-eller Worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Slut punkter för instans ingång](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribution |25 |25 |
| [Slut punkter för ingångar](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribution |25 |25 |
| [Interna slut punkter](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribution |25 |25 |
| [Värdbaserade tjänst certifikat](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribution |199 |199 |

<sup>1</sup> Varje Azure-Molntjänster med Web-eller Worker-roller kan ha två distributioner, en för produktion och en för mellanlagring. Den här gränsen avser antalet distinkta roller, det vill säga konfigurationen. Den här gränsen refererar inte till antalet instanser per roll, det vill säga skalning.

