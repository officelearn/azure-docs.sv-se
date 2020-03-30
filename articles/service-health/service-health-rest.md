---
title: Hämta hälsohändelser för Azure-resurser med REST API | Microsoft-dokument
description: Använd Azure REST-API:erna för att hämta hälsohändelser för dina Azure-resurser.
ms.topic: conceptual
ms.date: 06/06/2017
ms.openlocfilehash: 6964a6c4e85c38d532b12e730a02c4df73be76e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654009"
---
# <a name="get-resource-health-using-the-rest-api"></a>Hämta resurshälsa med REST API 

Den här exempelartikeln visar hur du hämtar en lista över hälsohändelser för Azure-resurserna i din prenumeration med hjälp av [Azure REST API](/rest/api/azure/).

Fullständig referensdokumentation och ytterligare exempel för REST API finns i [Azure Monitor REST-referensen](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande `GET` HTTP-begäran för att lista hälsohändelser för `2018-05-16` din `2018-06-20`prenumeration under tidsintervallet mellan och .

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Begärandehuvuden

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange till en giltig -`Bearer` [åtkomsttoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parametrar

| Namn | Beskrivning |
| :--- | :---------- |
| subscriptionId | Prenumerations-ID:t som identifierar en Azure-prenumeration. Om du har flera prenumerationer läser [du Arbeta med flera prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| api-version | DEN API-version som ska användas för begäran.<br /><br /> Det här dokumentet `2015-04-01`täcker api-version , som ingår i ovanstående URL.  |
| $filter | Filtreringsalternativet för att minska uppsättningen returnerade resultat. De tillåtna mönstren för den här parametern är tillgängliga [i referensen för åtgärden Aktivitetsloggar](/rest/api/monitor/activitylogs/list#uri-parameters). Exemplet som visas fångar alla händelser i ett tidsintervall mellan 2018-05-16 och 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Det behövs ingen begärandeorgan för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Statuskod 200 returneras med en lista över hälsohändelsevärden som `nextlink` motsvarar filterparametern, tillsammans med en URI för att hämta nästa resultatsida.

## <a name="example-response"></a>Exempelsvar 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
