---
title: Azure Security Center efterlevnad övervakning med hjälp av REST API | Microsoft Docs
description: Granskningsresultaten av automatisk kompatibilitetsavsökningar med hjälp av REST-API i Azure Security Center.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 3f07928897df01f5d654fa6a6bffce14290b24e4
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44295318"
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






