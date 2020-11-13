---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 2d66e7f497f85141de172c59b67676e1bb93955e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578837"
---
[Azure privat slutpunkt](../articles/private-link/private-endpoint-overview.md) är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link.  Privat slutpunkt använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt tar in tjänsten i ditt virtuella nätverk.

Du kan använda privat slut punkt för dina funktioner som finns i [Premium](../articles/azure-functions/functions-premium-plan.md) -och [App Service](../articles/azure-functions/functions-scale.md#app-service-plan) -planerna.

När du skapar en inkommande privat slut punkts anslutning för functions måste du också ha en DNS-post för att matcha den privata adressen.  Som standard skapas en privat DNS-post åt dig när du skapar en privat slut punkt med hjälp av Azure Portal.

Mer information finns i [använda privata slut punkter för Web Apps](../articles/app-service/networking/private-endpoint.md).