---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "67187717"
---
| Resource | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Web-eller Worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md) <sup>1</sup> |25 |25 |
| [Slut punkter för instans ingång](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per distribution |25 |25 |
| [Slut punkter för ingångar](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per distribution |25 |25 |
| [Interna slut punkter](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per distribution |25 |25 |
| [Värdbaserade tjänst certifikat](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per distribution |199 |199 |

<sup>1</sup> Varje Azure-Molntjänster med Web-eller Worker-roller kan ha två distributioner, en för produktion och en för mellanlagring. Den här gränsen avser antalet distinkta roller, det vill säga konfigurationen. Den här gränsen refererar inte till antalet instanser per roll, det vill säga skalning.

