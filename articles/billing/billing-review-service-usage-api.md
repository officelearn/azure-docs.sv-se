---
title: Granska Azure-tjänsten Resursanvändning med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska Resursanvändning i Azure-tjänsten.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alleonar
ms.openlocfilehash: 1b7b1455413fb4886b317d468e6d278111c094b1
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40226024"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Granska Azure-Resursanvändning med hjälp av REST-API


Azure [Consumption-API: er](https://docs.microsoft.com/rest/api/consumption/) hjälp med att du granskar data kostnader och användning för dina Azure-resurser.

I den här artikeln du lär dig hur du hämtar och aggregera resource användningsinformation för resurser i en Azure-resursgrupp, samt hur att filtrera resultaten baserat på [Azure resource manager-taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

## <a name="get-usage-for-a-resource-group"></a>Hämta användning för en resursgrupp

Hämta resursanvändningen för beräkning, databas och andra resurser i en resursgrupp med det `usageDetails` REST-åtgärden och filtrera resultatet efter resursgrupp.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30&filter=properties/resourceGroup eq '{resource-group}]
Content-Type: application/json   
Authorization: Bearer
```

Den `{subscription-id}` parametern krävs och bör innehålla ett prenumerations-ID som har åtkomst till resursgruppen {resursgruppen} med läsarrollen. 

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*| Krävs. Ange `application/json`. |  
|*Auktorisering:*| Krävs. Ange att ett giltigt `Bearer` token. |

### <a name="response"></a>Svar  

Statuskod 200 (OK) returneras för ett lyckat svar som innehåller en lista med användningsstatistik för varje Azure-resurser i resursgruppen med subscriptipon ID `00000000-0000-0000-0000-000000000000`.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    } ] }
```

## <a name="get-usage-for-tagged-resources"></a>Hämta användning för taggade resurser

Hämta resursanvändningen för resurser i ordnade efter taggar, använder den `usageDetails` REST-åtgärden och filtrera resultatet efter tagg namn med den `$filter` frågeparameter.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Consumption/usageDetails?$filter=tags eq 'tag1'&api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Den `{subscription-id}` parametern är obligatoriskt och bör innehålla ett prenumerations-ID som har åtkomst till taggade resurser.


### <a name="response"></a>Svar  

Statuskod 200 (OK) returneras för ett lyckat svar som innehåller en lista med användningsstatistik för varje Azure-resurser i resursgruppen med subscriptipon ID `00000000-0000-0000-0000-000000000000` och taggen namn nyckelvalv par är `dev` och `tools`. 

Exempelsvaret:

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "tags": {
        "dev": "tools"
      },
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
