---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b6ba1dcddd435c42ad864b8e87175d0e98c9b3a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279974"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| [Web/worker-roller per distribution](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Instans-slutpunkter för indata](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) per distribution |25 |25 |
| [Ange slutpunkter](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) per distribution |25 |25 |
| [Interna slutpunkter](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) per distribution |25 |25 |

<sup>1</sup>varje molntjänst med Web/Worker-roller kan ha två distributioner, en för produktion och en för mellanlagring. Tänk också på att den här gränsen avser antalet distinkta roller (konfiguration) och inte antalet instanser per roll (skala).

