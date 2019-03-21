---
title: Granska Azure-prenumeration faktureringsdata med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska fakturainformationen för prenumerationen.'
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
ms.author: erikre
ms.openlocfilehash: 15725989ef786f94421eddf647f101e3e73633fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890771"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Granska prenumerationsärenden med hjälp av REST API: er

Azure Reporting API: er hjälper dig att granska och hantera dina Azure-kostnader.

Filter att anpassa resultatet för att uppfylla dina behov.

Här kan du lära dig att använda ett REST-API för att returnera fakturainformationen för prenumerationen för ett visst datumintervall.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Skapa begäran

Den `{subscriptionID}` parametern krävs och identifierar målprenumerationen.

Den `{billingPeriod}` parametern krävs och anger en aktuell [faktureringsperioden](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

Den `${startDate}` och `${endDate}` parametrar är obligatoriskt i det här exemplet, men valfria för slutpunkten. De ange datumintervallet som strängar i formatet ÅÅÅÅ-MM-DD (exempel: `'20180501'` och `'20180615'`).

Följande huvuden krävs:

|Begärandehuvud|Beskrivning|
|--------------------|-----------------|
|*Content-Type:*|Krävs. Ange `application/json`.|
|*Auktorisering:*|Krävs. Ange att ett giltigt `Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Svar

Statuskod 200 returneras (OK) för ett lyckat svar som innehåller en lista med detaljerade kostnader för ditt konto.

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

Varje objekt i **värdet** representerar ett information om användningen av en tjänst:

|Egenskapen svar|Beskrivning|
|----------------|----------|
|**subscriptionGuid** | Globalt unikt ID för prenumerationen. |
|**startDate** | Datum användning igång. |
|**endDate** | Datum användning avslutades. |
|**useageQuantity** | Använd kvantitet. |
|**billableQuantity** | Kvantitet som faktiskt faktureras. |
|**pretaxCost** | Kostnad fakturerade innan tillämpliga skatter. |
|**meterDetails** | Detaljerad information om hur du använder. |
|**nextLink**| När värdet anger en URL för nästa ”page” av information. Tom när sidan är den sista. |

Det här exemplet är förkortas; Se [lista användningsinformation](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) för en fullständig beskrivning av varje svarsfält.

Andra statuskoder indikerar fel. I dessa fall kan förklarar objektet response varför begäran misslyckades.

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
- Granska [Enterprise rapporteringsöversikt](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Undersöka [Enterprise fakturering REST-API](https://docs.microsoft.com/rest/api/billing/)
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)
