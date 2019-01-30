---
title: Klientresursanvändning | Microsoft Docs
description: Referens för Resursanvändning API, som hämtar information för användningen av Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: db0cdfce5903b8866985b0545494351e2796d8e2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244631"
---
# <a name="tenant-resource-usage-api"></a>Klientresursanvändning

En klient kan använda klient-API för att visa användningsdata för klientens egen resurs. Detta API är konsekvent med API för Azure-användning (för tillfället i privat förhandsversion).

Du kan använda Windows PowerShell-cmdleten **Get-UsageAggregates** att hämta användningsdata som i Azure.

## <a name="api-call"></a>API-anrop
### <a name="request"></a>Förfrågan
Begäran hämtar information om förbrukning för de begärda prenumerationerna och för den begärda tidsramen. Det finns inga begärandetexten.

| **Metod** | **Begärande-URI** |
| --- | --- |
| HÄMTA |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argument
| **Argument** | **Beskrivning** |
| --- | --- |
| *armendpoint* |Azure Resource Manager-slutpunkten för Azure Stack-miljön. Azure Stack-konventionen är att namnet på Azure Resource Manager-slutpunkten är i formatet `https://management.{domain-name}`. Till exempel för development kit domännamnet är local.azurestack.external och Resource Manager-slutpunkten är `https://management.local.azurestack.external`. |
| *subId* |Prenumerations-ID för den användare som ansvarar för att göra anropet. Du kan använda detta API endast till frågan för användning av en enda prenumeration. Leverantörer kan använda API: T för Provider Resource användning till fråga användning för alla klienter. |
| *reportedStartTime* |Starttid för frågan. Värdet för *DateTime* ska vara i UTC och i början av timme, till exempel 13:00. För dagliga aggregering, ange ett värde till midnatt i UTC. Formatet är *undantagna* ISO 8601, till exempel 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, där kolon hoppas att % 3a och plus hoppas till % 2b så att den är URI: N eget. |
| *reportedEndTime* |Sluttid för frågan. De begränsningar som gäller för *reportedStartTime* gäller även för det här argumentet. Värdet för *reportedEndTime* får inte vara i framtiden. |
| *aggregationGranularity* |Valfri parameter som har två diskreta möjliga värden: varje dag och per timme. Eftersom värdena föreslå en returnerar data i daglig kornighet och den andra är upplösningen per timme. Dagliga alternativet är standardinställningen. |
| *api-version* |Version av det protokoll som används för att göra denna begäran. Du måste använda 2015-06-01-preview. |
| *continuationToken* |Token hämtas från det senaste anropet till användning API-providern. Den här token krävs när ett svar är större än 1 000 rader och den fungerar som ett bokmärke för pågår. Om den inte finns data hämtas från början på dagen eller timme, baserat på precisionen skickas in. |

### <a name="response"></a>Svar
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Svarsinformation
| **Argument** | **Beskrivning** |
| --- | --- |
| *ID* |Unikt ID för aggregering för användning |
| *Namn* |Namnet på samlingen användning |
| *typ* |Resursdefinitionen |
| *Prenumerations-ID* |Prenumerations-ID för Azure-användare |
| *usageStartTime* |UTC starttid för bucketen användning som tillhör den här aggregeringen användning |
| *usageEndTime* |Sluttid i UTC för bucketen användning som tillhör den här aggregeringen användning |
| *instanceData* |Nyckel / värde-par med instansinformation (i ett nytt format):<br>  *resourceUri*: Fullständigt kvalificerade resurs-ID, inklusive resursgrupper och instansnamn <br>  *Plats*: Region där den här tjänsten kördes <br>  *Taggar*: Resurstaggar som användaren anger <br>  *additionalInfo*: Mer information om den resurs som förbrukades, till exempel version av Operativsystemet eller avbildning skriver |
| *Kvantitet* |Mängden resursförbrukning som inträffat under det här tidsintervallet |
| *meterId* |Unikt ID för den resurs som förbrukades (kallas även *ResourceID*) |


## <a name="next-steps"></a>Nästa steg
[API för providerresursanvändning](azure-stack-provider-resource-api.md)

[Användningsrelaterade vanliga frågor och svar](azure-stack-usage-related-faq.md)

