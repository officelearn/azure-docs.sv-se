---
title: Hämta Azure resource health-händelser med hjälp av REST-API | Microsoft Docs
description: 'Använd Azure REST API: er för att hämta health-händelser för dina Azure-resurser.'
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790920"
---
# <a name="get-resource-health-using-the-rest-api"></a>Hämta Resource Health med hjälp av REST-API 

Det här exemplet artikeln visar hur du hämtar en lista över health-händelser för Azure-resurser i din prenumeration med hjälp av den [Azure REST API](/rest/api/azure/).

Fullständig referensdokumentation och ytterligare exempel för REST API finns i den [Azure Monitor REST-referens för](/rest/api/monitor). 

## <a name="build-the-request"></a>Skapa begäran

Använd följande `GET` HTTP-begäran att lista health-händelser för din prenumeration för intervallet mellan `2018-05-16` och `2018-06-20`.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>Begärandehuvud

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange `application/json`.|  
|*Auktorisering:*|Krävs. Ange att ett giltigt `Bearer` [åtkomsttoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |  

### <a name="uri-parameters"></a>URI-parametrar

| Namn | Beskrivning |
| :--- | :---------- |
| subscriptionId | Prenumerations-ID som identifierar en Azure-prenumeration. Om du har flera prenumerationer, se [arbeta med flera prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). |
| API-versionen | API-version för begäran.<br /><br /> Det här dokumentet beskriver api-versionen `2015-04-01`och ingår i URL: en ovan.  |
| $filter | Filtrera alternativet för att minska antal returnerade resultat. De tillåtna mönster för den här parametern är tillgängliga [i referensen för aktivitetsloggar åtgärden](/rest/api/monitor/activitylogs/list#uri-parameters). I exemplet samlar in alla händelser i ett tidsintervall mellan 2018-05-16 och 2018-06-20 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>Begärandetext

Inga begärandetexten krävs för den här åtgärden.

## <a name="handle-the-response"></a>Hantera svaret

Statuskod 200 returneras en lista över hälsa händelsevärden som motsvarar filter-parametern tillsammans med en `nextlink` URI för att hämta nästa sida i resultatet.

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
