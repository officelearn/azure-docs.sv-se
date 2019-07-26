---
title: Granska fakturerings data för Azure-prenumerationen med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska fakturerings information för prenumerationen.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: banders
ms.openlocfilehash: 8cfa429b18fb282f5c1f85d2fd1637704653b855
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443039"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Granska prenumerations fakturering med hjälp av REST API: er

Azure repor ting-API: er hjälper dig att granska och hantera dina Azure-kostnader.

Filter hjälper dig att anpassa resultaten efter dina behov.

Här lär du dig att använda en REST API för att returnera fakturerings information om prenumerationen för ett visst datum intervall.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Skapa begäran

`{subscriptionID}` Parametern är obligatorisk och identifierar mål prenumerationen.

Parametern är obligatorisk och anger en aktuell [fakturerings period.](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) `{billingPeriod}`

Parametrarna `${startDate}` och`${endDate}` måste anges för det här exemplet, men det är valfritt för slut punkten. De anger datum intervallet som strängar i formatet åååå-mm-dd (exempel: `'20180501'` och `'20180615'`).

Följande rubriker krävs:

|Begär ande huvud|Beskrivning|
|--------------------|-----------------|
|*Innehålls typ:*|Obligatoriskt. Ange till `application/json`.|
|*Authorization:*|Obligatoriskt. Ange en giltig `Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Svar

Status koden 200 (OK) returneras för ett lyckat svar som innehåller en lista med detaljerade kostnader för ditt konto.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Varje objekt i **värde** representerar en information om användningen av en tjänst:

|Svars egenskap|Beskrivning|
|----------------|----------|
|**subscriptionGuid** | Globalt unikt ID för prenumerationen. |
|**/SD** | Datum då användningen startades. |
|**endDate** | Datum då användningen avslutades. |
|**useageQuantity** | Använd kvantitet. |
|**billableQuantity** | Kvantitet som faktiskt faktureras. |
|**pretaxCost** | Fakturerade kostnader före tillämpliga skatter. |
|**meterDetails** | Detaljerad information om användningen. |
|**nextLink**| Anger en URL för nästa sida av information när den anges. Tomt när sidan är den sista. |

Det här exemplet är förkortat; Se [lista användnings information](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) för en fullständig beskrivning av varje svars fält.

Andra status koder indikerar fel villkor. I dessa fall förklarar Response-objektet varför begäran misslyckades.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
- Granska [Översikt över företags rapportering](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Undersök [företagets fakturerings REST API](https://docs.microsoft.com/rest/api/billing/)
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)
