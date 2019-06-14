---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 9b98e1a1f5243584d0ca4b1490e25302ec26b465
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050565"
---
| Resource | [Förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premium-abonnemang](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service-plan](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Skala ut | Händelsedriven | Händelsedriven | [Manuell/automatisk skalning](../articles/app-service/web-sites-scale.md) | 
|Standard [tidsgräns](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Max [tidsgräns](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | obegränsade | obegränsade<sup>3</sup> |
| Maxvärde utgående anslutningar (per instans) | 600 active (1200 totalt) | obegränsade | obegränsade |
| Maxstorlek för begäran (MB)<sup>4</sup> | 100 | 100 | 100 |
| Max stränglängd för frågan<sup>4</sup> | 4096 | 4096 | 4096 |
| URL: en begäran om maxlängd<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per instans | 100 | 210-840 | 100-840 |
| Maximalt minne (GB per instans) | 1,5 | 3.5-14 | 1.75-14 |
| Funktionsappar per plan |100 |100 |obegränsade<sup>5</sup> |
| [App Service-planer](../articles/app-service/overview-hosting-plans.md) | 100 per [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resursgrupp |100 per resursgrupp |
| Storage<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Anpassade domäner per app</a> |500<sup>7</sup> |500 |500 |
| Anpassad domän [SSL-stöd](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Stöds, inte jokerteckencertifikat för *. azurewebsites.net som är tillgängliga som standard| obegränsade SNI SSL och 1 IP SSL-anslutningar ingår |obegränsade SNI SSL och 1 IP SSL-anslutningar ingår | 

<sup>1</sup> gränser för de olika alternativen för App Service-plan, finns det [begränsningar för App Service-plan](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> tidsgränsen för Functions 1.x-körning i en App Service-plan är obegränsade som standard.  
<sup>3</sup> kräver App Service-planen [alltid på](../articles/azure-functions/functions-scale.md#always-on). Övergå till standard [priserna](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> dessa gränser är [i värden](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> det faktiska antalet funktionsappar som du kan ha beror på aktiviteten för appar, storleken på instanserna av datorer och motsvarande resursutnyttjandet.
<sup>6</sup> lagringsgränsen är den totala innehållsstorleken i tillfällig lagring över alla appar i samma App Service-planen. Förbrukningsplan använder Azure Files för tillfällig lagring.  
<sup>7</sup> när funktionsappen är värd för en [förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan), endast alternativet CNAME stöds. För funktionsappar i en [premiumprenumerationen](../articles/azure-functions/functions-scale.md#premium-plan) eller en [App Service-plan](../articles/azure-functions/functions-scale.md#app-service-plan), kan du mappa en anpassad domän med en CNAME-post eller en A-post.
