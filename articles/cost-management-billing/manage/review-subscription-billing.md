---
title: Granska faktureringsdata för Azure-prenumeration med REST API | Microsoft Docs
description: Lär dig hur du använder Azure REST API:er för att granska information om prenumerationsfakturering.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: cost-management-billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4dc7771862bc922702c5cc321fb3c566212d799a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "75990572"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Granska prenumerationsfakturering med hjälp av REST-API:er

Rapporterings-API:er i Azure hjälper dig att granska och hantera dina Azure-kostnader.

Filter hjälper dig att anpassa resultaten efter dina behov.

Här lär du dig att använda ett REST-API för att returnera information om prenumerationsfakturering för ett visst datumintervall.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Skapa begäran

Parametern `{subscriptionID}` är obligatorisk och identifierar målprenumerationen.

Parametern `{billingPeriod}` är obligatorisk och anger en aktuell [faktureringsperiod](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

Parametrarna `${startDate}` och`${endDate}` är obligatoriska för det här exemplet men valfria för slutpunkten. De anger datumintervallet som strängar i formatet ÅÅÅÅ-MM-DD (exempel: `'20180501'` och `'20180615'`).

Följande huvuden krävs:

|Begärandehuvud|Beskrivning|
|--------------------|-----------------|
|*Content-Type:*|Krävs. Ange till `application/json`.|
|*Auktorisering:*|Krävs. Ange till en giltig `Bearer`-[åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Svar

Statuskoden 200 (OK) returneras för ett lyckat svar, som innehåller en lista med detaljerade kostnader för ditt konto.

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

Varje objekt i **värde** representerar information om användningen av en tjänst:

|Svarsegenskap|Beskrivning|
|----------------|----------|
|**subscriptionGuid** | Globalt unikt ID för prenumerationen. |
|**startDate** | Det datum då användningen påbörjades. |
|**endDate** | Det datum då användningen avslutades. |
|**useageQuantity** | Den kvantitet som används. |
|**billableQuantity** | Den kvantitet som faktiskt faktureras. |
|**pretaxCost** | Fakturerade kostnader före tillämpliga skatter. |
|**meterDetails** | Detaljerad information om användningen. |
|**nextLink**| När denna egenskap anges specificerar den en URL för nästa ”sida” med information. Tom när sidan är den sista. |

Det här exemplet är förkortat; en fullständig beskrivning av varje svarsfält finns i [Visa användningsinformation](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy).

Andra statuskoder indikerar felvillkor. I dessa fall förklarar svarsobjektet varför begäran misslyckades.

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
- Granska [Översikt över företagsrapportering](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Undersöka [REST API för företagsfakturering](https://docs.microsoft.com/rest/api/billing/)
- [Komma igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)
