---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198351"
---
| Resurs | [Konsumtionsplan](../articles/azure-functions/functions-scale.md#consumption-plan) | [Premiumplan](../articles/azure-functions/functions-scale.md#premium-plan) | [App Service plan](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Skala ut | Händelsebaserad | Händelsebaserad | [Manuell/automatisk skalning](../articles/app-service/manage-scale-up.md) | 
| Max instanser | 200 | 100 | 10-20 |
|[Standardtidsgräns (min)](../articles/azure-functions/functions-scale.md#timeout) |5 | 30 |30<sup>2</sup> |
|Max [tidsgräns](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | obegränsad<sup>8</sup> | obegränsad<sup>3</sup> |
| Max utgående anslutningar (per instans) | 600 aktiva (1200 totalt) | Obegränsad | Obegränsad |
| Max begäran storlek (MB)<sup>4</sup> | 100 | 100 | 100 |
| Max frågestränglängd<sup>4</sup> | 4096 | 4096 | 4096 |
| Url-längd för max begäran<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per instans | 100 | 210-840 | 100-840 |
| Max minne (GB per instans) | 1.5 | 3.5-14 | 1.75-14 |
| Funktionsappar per abonnemang |100 |100 |obegränsad<sup>5</sup> |
| [App Service-planer](../articles/app-service/overview-hosting-plans.md) | 100 per [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resursgrupp |100 per resursgrupp |
| Lagring<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Anpassade domäner per app</a> |500<sup>7</sup> |500 |500 |
| Anpassat [SSL-stöd](../articles/app-service/configure-ssl-bindings.md) för domän |obunden SNI SSL-anslutning inkluderad | obundna SNI SSL- och 1 IP SSL-anslutningar ingår |obundna SNI SSL- och 1 IP SSL-anslutningar ingår | 

<sup>1</sup> För specifika begränsningar för de olika appserviceplansalternativen finns i begränsningarna för [App Service-planen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> Som standard är tidsgränsen för funktion 1.x-körningen i en apptjänstplan obegränsad.  
<sup>3</sup> Kräver att apptjänstplanen är inställd [på Alltid på](../articles/azure-functions/functions-scale.md#always-on). Betala till [standardpriser](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Dessa gränser [anges i värden](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> Det faktiska antalet funktionsappar som du kan vara värd för beror på aktiviteternas aktivitet, storleken på maskininstanserna och motsvarande resursutnyttjande.  
<sup>6</sup> Lagringsgränsen är den totala innehållsstorleken i tillfällig lagring i alla appar i samma App Service-plan. Förbrukningsplan använder Azure-filer för tillfällig lagring.  
<sup>7</sup> När din funktionsapp finns i en [förbrukningsplan](../articles/azure-functions/functions-scale.md#consumption-plan)stöds endast alternativet CNAME. För funktionsappar i en [Premium-abonnemang](../articles/azure-functions/functions-scale.md#premium-plan) eller en [App Service-plan](../articles/azure-functions/functions-scale.md#app-service-plan)kan du mappa en anpassad domän med antingen ett CNAME eller en A-post.  
<sup>8</sup> Garanterat i upp till 60 minuter.