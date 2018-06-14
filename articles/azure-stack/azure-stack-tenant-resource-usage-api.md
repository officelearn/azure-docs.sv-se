---
title: Resursanvändning API-klient | Microsoft Docs
description: Referens för Resursanvändning API, som att hämta information om Azure-stacken användning.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: bc0b9993119342f07c28ed0384c11ae0f15bc439
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
ms.locfileid: "29873495"
---
# <a name="tenant-resource-usage-api"></a>Resursanvändning API-klient

En klient kan använda klient-API för att visa resursanvändningsdata för klientens egna. Detta API är konsekventa med Azure Usage API (för tillfället i privat förhandsvisning).

Du kan använda Windows PowerShell-cmdleten **Get-UsageAggregates** att hämta användningsdata som i Azure.

## <a name="api-call"></a>API-anrop
### <a name="request"></a>Förfrågan
Begäran hämtar information om förbrukningen för de begärda prenumerationerna och för den begärda tidsperioden. Det finns ingen brödtext i begäran.

| **Metoden** | **URI-begäran** |
| --- | --- |
| HÄMTA |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argument
| **Argument** | **Beskrivning** |
| --- | --- |
| *armendpoint* |Azure Resource Manager-slutpunkten för din Azure Stack-miljö. Azure-stacken konventionen är att namnet på Azure Resource Manager-slutpunkten är i formatet `https://management.{domain-name}`. Till exempel för i development kit domännamnet är local.azurestack.external och Resource Manager-slutpunkten är `https://management.local.azurestack.external`. |
| *subId* |Prenumerations-ID för den användare som har att göra anropet. Du kan använda detta API endast till frågan för användning i en enda prenumeration. Leverantörer kan använda providern resurs användning API till fråga nätverksanvändning för alla klienter. |
| *reportedStartTime* |Starttid för frågan. Värdet för *DateTime* ska vara i UTC och i början av timme, till exempel 13:00. Ange värdet till midnatt UTC-tid för daglig sammanställning. Formatet är *undantagstecken* ISO 8601, till exempel 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, där kolon hoppas till % 3a och plus hoppas till % 2b så att den är eget URI. |
| *reportedEndTime* |Sluttid för frågan. Begränsningar som gäller för *reportedStartTime* gäller även för det här argumentet. Värdet för *reportedEndTime* får inte vara i framtiden. |
| *aggregationGranularity* |Valfri parameter som har två separata möjliga värden: varje dag och varje timme. Eftersom värdena föreslår en returnerar data i timme och det andra är en upplösning på varje timme. Dagliga alternativet är standardinställningen. |
| *api-version* |Version av det protokoll som används för att göra denna begäran. Du måste använda 2015-06-01-preview. |
| *continuationToken* |Token hämtas från det senaste anropet till användning av API-providern. Denna token krävs när ett svar är större än 1 000 rader och det fungerar som ett bokmärke för pågår. Om den inte finns data hämtas från början på dagen eller timme, baserat på Granulariteten skickades. |

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
| *id* |Unikt ID för mängdfunktionen användning |
| *Namn* |Namnet på mängdfunktionen användning |
| *Typ* |Resursdefinitionen |
| *subscriptionId* |Prenumerations-ID för Azure användaren |
| *usageStartTime* |UTC starttid för en användning bucket som tillhör den här samlingen för användning |
| *usageEndTime* |UTC-sluttid för usage-bucket som tillhör den här samlingen för användning |
| *instanceData* |Nyckel-värdepar för detaljerad information om instansen (i ett nytt format):<br>  *resourceUri*: fullständigt resurs-ID, inklusive resursgrupper och instansnamn <br>  *plats*: Region där den här tjänsten körs <br>  *taggar*: resurstaggar som användaren anger <br>  *additionalInfo*: Mer information om den resurs som förbrukades, till exempel OS-version eller image-typ |
| *Antal* |Mängden resursförbrukning som uppstått i den här tidsintervall |
| *meterId* |Unikt ID för den resurs som förbrukades (kallas även *ResourceID*) |


## <a name="next-steps"></a>Nästa steg
[API för providerresursanvändning](azure-stack-provider-resource-api.md)

[Användning-relaterade vanliga frågor och svar](azure-stack-usage-related-faq.md)

