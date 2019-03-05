---
title: 'Migrera från Enterprise-avtal till Microsoft Customer avtal API: er – Azure | Microsoft Docs'
description: 'Den här artikeln hjälper dig att förstå effekten till API: er när du migrerar från en Microsoft Enterprise Agreement (EA) till en Microsoft-kundavtal.'
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/02/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: a12360e60026fd4251cc232caccd1bc52a34d2ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344969"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrera från Enterprise-avtal till Microsoft Customer avtal API: er

Den här artikeln hjälper dig att förstå effekten till API: er när du migrerar från en Microsoft Enterprise Agreement (EA) till en Microsoft-kundavtal. Följande nya API: er används för att visa kostnaderna och priser:

- Prisdokument-API för Microsoft-kundernas avtal
  - Prisdokument med faktura
  - Prisdokument av profil för fakturering
  - Prisdokument för den öppna faktureringsperioden
- Användningsinformation API för Microsoft-kundernas avtal
  - Information om användning av fakturan för fakturering profil (filter faktura avsnittet)
    - Information om användning av datum för användning
    - Användningsinformation för faktureringskonto
    - Användningsinformation för fakturering profil
    - Användningsinformation för faktura-avsnitt
    - Användningsinformation för prenumerationen
  - Användningsinformation för månad till dags datum
    - Användningsinformation för faktureringskonto
    - Användningsinformation för fakturering profil
    - Användningsinformation för faktura-avsnitt
    - Användningsinformation för prenumerationen
  - Information om användning av faktureringsperiod
- Krediter API för Microsoft kundavtal
  - Aktuellt saldo sammanfattning API av profil för fakturering
  - Kredit händelser API av profil för fakturering
  - Kredit många API av profil för fakturering
- Fråge-API
- Avgifter API
  - Avgifter API efter faktureringskonto
  - Avgifter API av fakturering profil
  - Avgifter API faktura avsnittet
- Invoice List API
  - Av profil för fakturering
  - Genom att faktureringskonto
- API:er för reservation
- Power BI-integrering API
- Export-API
  - genom att faktureringskonto
  - av profil för fakturering
  - Faktura avsnittet
- Transaktionen API
- Budgetar API
- Sammanställda kostnaden API

## <a name="effect-on-automation"></a>Effekt på automation

API: er som används med ett Microsoft-kundavtal skiljer sig från de som används med ett Enterprise-avtal. Du kanske har lagt upp API automation tidigare med enterprise nyckelbaserad API: er eller Azure Resource Manager-baserade API: er för enterprise-registreringar. I så fall kan du uppdatera din automation-konfiguration för nyare API: er som används av Microsoft kundens avtal.

En lista över äldre stöds inte API: er av Microsoft Customer avtal finns i [stöds inte API: er](#unsupported-apis).

API: er för Microsoft Customer avtal stöder endast Azure Resource Manager-autentisering – de inte stöder API-nyckel-baserad autentisering.

## <a name="pricesheet-apis-show-prices-for-azure-services"></a>Pricesheet APIs visa priser för Azure-tjänster

The Pricesheet APIs utifrån för Microsoft kundavtal är bland annat information om priser för alla tjänster som är klassificerade för fakturering, antalet för användning. API: et innehåller inte rätt inköp. Marketplace-köp för berättigande, priserna för reserverade instanser och supporterbjudanden. API: et tillhandahåller unika priset för varje tjänst för en specifik mätare och produktnamn ordning.

### <a name="view-the-pricesheet-for-azure-consumption-services-by-billing-profile"></a>Visa Pricesheet för Azure-tjänst genom att fakturering profil

Du kan visa priser för alla första och tredje parts tjänster som Azure-förbrukning i Pricesheet för en profil för fakturering. Använd API för att visa priser för Azure-tjänst. Priserna som visas för fakturering profilen gäller för alla prenumerationer som hör till fakturering profilen.

- CSV-format
  - POST -`https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31& format=csv`
- JSON-format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json`

### <a name="view-current-open-billing-or-service-period-estimated-prices"></a>Visa aktuella öppna fakturerings- eller tjänsten uppskattade tidsperiod priser

Om du vill visa de uppskattade priserna för alla tjänster i den aktuella open faktureringsperiod eller serviceperioden kan du visa Prisdokument i CSV eller JSON-format.

- CSV-format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`
- JSON-format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

Priserna som visas i Prisdokument för alltid aktuella serviceperioden är senaste priserna för mätaren. Priserna uppdateras varje dag, med hjälp av det senaste priset för mätare och produkt.

När du granskar kostnaderna baserat på priset i den öppna perioden är uppskattade kostnader. Senaste låst priset i slutet av öppna serviceperioden gäller för användning i den öppna perioden.

Den öppna perioden är aktuella serviceperioden där användningen ännu inte har klassificerats för kostnadshantering och fakturering.

### <a name="view-invoice-by-billing-profile-prices"></a>Visa faktura efter fakturering profil priser

Du kan visa priser för Azure-tjänst som faktureras i en viss faktura för en profil för fakturering. Använd API för att visa priser endast för de tjänster som visas på fakturan. Fakturan identifieras med fakturanummer.

- CSV-format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv`

- JSON-format
  - POST
    - `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json`

Den `{invoiceId}` visas tidigare är samma som den **fakturanummer** visas på fakturan sammanfattning PDF-fil. Här är ett exempel.

![Exempelbild som visar en faktura: talet som motsvarar till InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

## <a name="pricesheet-api-asynchronous-response"></a>Asynkront svar för Prisdokument-API

Stöd för API: erna i [Azure REST asynkrona](../azure-resource-manager/resource-manager-async-operations.md) format. När du anropar API: er med hjälp av GET, visas följande svar:

```
            No Response Body
            HTTP Status 202 Accepted
```

 Följande huvuden skickas med platsen för utdata.

```
Location: https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
Azure-AsyncOperation: https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==
   Retry-After: 10
 OData-EntityId: {operationId}
```

Klienten kan göra ett GET-anrop till platsen. Svaret på GET-anrop är samma som exempel informationen som visas tidigare, tills åtgärden tillståndet klart eller har misslyckats. När du är klar, returnerar svaret till anropet för att komma på plats samma svar som om åtgärden har utförts på samma gång. Här är ett exempel.

```
HTTP Status 200
i.e.
            {
    “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
    “name”: {operationId},
    “type”: “Microsoft.Consumption/operationResults”,
    “properties” : {
          “downloadUrl”: {urltoblob},
          “vaildTill”: “Date”
}
}
```

Dessutom kan klienten kan också göra en GET-anrop den `Azure-AsyncOperation`. Slutpunkten returnerar status för åtgärden. Här är ett exempel:

```
HTTP Status 200
               {
"id": “providers/Microsoft.Consumption/operationStatus/{operationId}",
"name": "operationId",
"status" : "Succeeded | Failed | Canceled",
"startTime": "<DateLiteral per ISO8601>",
"endTime": "<DateLiteral per ISO8601>",
"percentComplete": <double between 0 and 100>
"properties": {
    /\* The resource provider can choose the values here, but it should only be
       returned on a successful operation (status being "Succeeded"). \*/
},
"error" : {
    /\* This is the OData v4 format, used by the RPC and will go into the
     v2.2 Azure REST API guidelines \*/
    "code": "BadArgument",
    "message": "The provided database &#39;foo&#39; has an invalid username."
}
}
```

### <a name="pricesheet-fields"></a>Fält för Prisdokument

Granska informationen på [Förstå villkoren i ditt prisdokument för ett Microsoft-kundavtal](../billing/billing-mca-understand-pricesheet.md) att visa alla Prisdokument fält och deras beskrivningar.

Priser som baseras på användning, är Förbrukningspris låst för serviceperioden sedan i början av perioden. Priset för tjänsten appliceras och låst på inköpsdatumet. Serviceperioden är vanligtvis en kalendermånad för Azure-tjänster.

Priset för en Azure-förbrukning tjänst i ett Microsoft-kundavtal är unikt för en produkt ordning och mätaren.

Fälten som du kan använda för att unikt identifiera priset för Azure-tjänst är:

- productOrderName
- meterId (även meterName)

Priset för Microsoft Customer avtal definieras annorlunda än Enterprise-avtal där priset för tjänster i registreringen är unik för:

- Produkt
- PartNumber
- Mätare
- erbjudande

### <a name="pricesheet-support"></a>Stöd för Prisdokument

Prisdokument-API: et stöder för närvarande bara Microsoft kundavtal priser för Azure från första part och tjänster från tredje part. Det stöder inte priserna för Marketplace-tjänster, reserverade instanser och Azure Hybrid-förmåner (AHUB).

API: et tillhandahåller endast det pris som kommer att debiteras mot tjänsten. Det här priset är lika med eller mindre än den förhandlade priset.

## <a name="query-for-usage-and-costs"></a>Fråga om användning och kostnader

API: et för användning information ger åtkomst till Azure och tredje parts Marketplace användning och kostnader. Priserna baseras på den förhandlade priserna för ditt avtal. EA-kunder som flyttar till en kundavtal bör använda användningsinformation för faktureringskontot eller fakturering omfång för profilen. Du kan vara frågan till någon omfattning, med följande undantag:

|   | Kundavtal | Enterprise Agreement | Enskilda avtal (PAYG) |
| --- | --- | --- | --- |
| Faktureringskonto | Ja | Ja | Gäller inte |
| Fakturering profiler (faktura) | Ja | Ej tillämpligt. Se faktureringskonto. | Ej tillämpligt. Se prenumerationer. |
| Fakturaavsnitt | Nej. Använd fakturering profiler med ett filter. | Gäller inte | Gäller inte |
| EA-avdelningar | Gäller inte | Ja | Gäller inte |
| EA-registreringskonton | Gäller inte | Ja | Ej tillämpligt. Se prenumerationer. |
| Hanteringsgrupper | Nej. Använd faktureringskonto. | Ja | Ja |
| Prenumerationer | Ja | Ja | Ja |
| Resursgrupper | Nej. Använda prenumerationer med ett filter. | Nej. Använda prenumerationer med ett filter. | Nej. Använda prenumerationer med ett filter. |

Egenskaperna som returneras av UsageDetails per avtalstyp är olika. Se [UsageDetails API-dokumentation](/rest/api/consumption/usagedetails/) en fullständig lista.

### <a name="power-bi-integration"></a>Power BI-integrering

EA-kunder som flyttar till en Microsoft-kundavtal ska sluta använda någon befintlig integrering med Power BI till Microsoft Azure Consumption Insights med hjälp av information om deras EA. Du kan i stället använda:

- Power BI Desktop – skapa nya Power BI-rapporter för Microsoft Customer avtal med det [Azure Cost Management-anslutningstjänsten](https://docs.microsoft.com/power-bi/desktop-connect-azure-consumption-insights).
- Power BI-tjänsten – flytta från den [Microsoft Azure Consumption Insights](https://docs.microsoft.com/power-bi/service-connect-to-azure-consumption-insights) Innehållspaketet till appen Azure Cost Management (kundavtal) på [AppSource](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview).

## <a name="unsupported-apis"></a>Stöds inte API: er

I följande Enterprise-avtal API: er stöds inte av Microsoft kundens avtal. Alternativ till stöds inte API: er beskrivs.

[Hämta Pricesheet av prenumeration](/rest/api/consumption/pricesheet/get/) stöds inte. Den visar priserna för omfånget för en prenumerations-ID. Använd i stället **Prisdokument av fakturering profilen**. Priset för en tjänst i omfånget för en prenumeration är samma pris som omfattas av en profil för fakturering. Användaren som anropar API: et måste ha nödvändig behörighet.

**Pris blad - hämta av faktureringsperiod** stöds inte. Den hämtar prisdokumentet för ett omfång med subscriptionId och faktureringsperioden. Använd i stället **Prisdokument av fakturering profilen**. Priset för tjänsten i en prenumeration omfattning är lika med priset för tjänsten i ett fakturering omfång för profilen. Användaren som anropar API: et måste ha nödvändig behörighet.

[Prisdokument för företagskunder genom registrering](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) stöds inte. Och, [Pricesheet för företagskunder av registrering för en faktureringsperiod](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet/) stöds inte. Både ange gäller priset för varje mätare för den angivna registrering och faktureringsperiod. Använd i stället **Prisdokument av fakturering profilen**. Prisdokument definitionsområdet registrering är prisdokument som är tillgängliga för fakturerings-profilen. Användaren som anropar API: et måste ha nödvändig behörighet.

## <a name="see-also"></a>Se också
- Läs mer om [Azure Consumption REST API: er](/rest/api/consumption/).
