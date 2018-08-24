---
title: Azure Security Center efterlevnad övervakning med hjälp av REST API | Microsoft Docs
description: Granskningsresultaten av automatisk kompatibilitetsavsökningar med hjälp av REST-API i Azure Security Center.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819932"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Granska Security Center kompatibilitetsresultat med hjälp av REST-API: er för Azure

I den här artikeln får du lära dig att hämta security kompatibilitetsinformation för en lista över prenumerationer med hjälp av REST-API: er för Azure.

## <a name="review-compliance-for-each-subscription"></a>Kontrollera kompatibiliteten för varje prenumeration

I exemplet nedan hämtar utvärderingsverktyg säkerhetsinformation för en viss efterlevnad och prenumeration med hjälp av den [hämta Efterlevnadskraven](/rest/api/securitycenter/compliances/get) igen.

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

Den `{complianceName}` parametern krävs och måste innehålla namnet på den utvärderade kompatibiliteten för `{subscription-id}`. Utdata innehåller resultaten, till exempel:

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Granska efterlevnad för flera prenumerationer

Om du vill hämta data för flera prenumerationer, att anropa nedan först hämta en lista över prenumerationer med hjälp av den [lista prenumerationer](/rest/api/resources/subscriptions/list) igen. Invoke ovanstående [hämta Efterlevnadskraven](/rest/api/securitycenter/compliances/get) för var och en av de returnerade prenumerationen-ID.

API-anrop är:

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

Som returnerar en matris med med värden, till exempel följande. Använda prenumerations-ID-värden i ovanstående anropet för att visa kompatibilitetsinformation för alla prenumerationer.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






