---
title: 'Migrera EA till Microsoft kund avtals-API: er – Azure'
description: Den här artikeln hjälper dig att förstå konsekvenserna av att migrera ett Microsoft Enterprise-avtal (EA) till ett kund avtal från Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: b0c30cd35efd598767d21dbdafc5100d8896380c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514722"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrera från Enterprise-avtal till Microsoft kund avtals-API: er

Den här artikeln hjälper dig att förstå data strukturen, API och andra system integrerings skillnader mellan Enterprise-avtal-(EA) och Microsoft Customer Agreement-konton (MCA). Azure Cost Management stöder API: er för båda konto typerna. Läs artikeln [om att installera fakturerings konto för](../manage/mca-setup-account.md) Microsoft-kund avtal innan du fortsätter.

Organisationer med ett befintligt EA-konto bör läsa den här artikeln tillsammans med att konfigurera ett MCA-konto. Tidigare krävde du att förnya ett EA-konto, vilket krävde ett minimalt arbete för att flytta från en gammal registrering till ett nytt. Att migrera till ett MCA-konto kräver dock ytterligare arbete. Ytterligare arbete beror på ändringar i det underliggande del systemet för fakturering, vilket påverkar alla kostnads relaterade API: er och tjänst erbjudanden.

## <a name="mca-apis-and-integration"></a>MCA API: er och integrering

Med MCA API: er och ny integrering kan du:

- Ha fullständig API-tillgänglighet via interna Azure-API: er.
- Konfigurera flera fakturor på ett enda fakturerings konto.
- Få åtkomst till ett kombinerat API med användning av Azure-tjänster, marknads plats på tredje part och marknads plats inköp.
- Visa kostnader i fakturerings profiler (samma som registreringar) med hjälp av Azure Cost Management.
- Få till gång till nya API: er för att Visa kostnader, få meddelanden när kostnader överstiger fördefinierade tröskelvärden och exportera rå data automatiskt.

## <a name="migration-checklist"></a>Check lista för migrering

Följande objekt hjälper dig att övergå till MCA API: er.

- Bekanta dig med det nya [fakturerings kontot för Microsofts kund avtal](../understand/mca-overview.md).
- Bestäm vilka API: er som du använder och se vilka som ersätts i följande avsnitt.
- Bekanta dig med [Azure Resource Manager REST-API: er](/rest/api/azure).
- Om du inte redan använder Azure Resource Manager-API: er [registrerar du din klient app med Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Uppdatera eventuell programmerings kod för att [använda Azure AD-autentisering](/rest/api/azure/#create-the-request).
- Uppdatera eventuell programmerings kod för att ersätta EA API-anrop med MCA API-anrop.
- Uppdatera fel hanteringen för att använda nya felkoder.
- Granska ytterligare integrerings erbjudanden, till exempel Cloudyn och Power BI, för övrig nödvändig åtgärd.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA-API: er ersatta med MCA API: er

EA-API: er använder en API-nyckel för autentisering och auktorisering. MCA-API: er använder Azure AD-autentisering.

| Syfte | EA-API | MCA-API |
| --- | --- | --- |
| Saldo och krediter | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Användning (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Användning (CSV) | [/UsageDetails/Download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/UsageDetails/Submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft. förbrukning/usageDetails/hämtning](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-användning (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft. förbrukning/usageDetails/hämtning](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Fakturerings perioder | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft. fakturering/billingAccounts/billingProfiles/fakturor |
| Prisdokument | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. fakturering/billingAccounts/billingProfiles/pris dokument/standard/Download format = JSON|CSV Microsoft. fakturering/billingAccounts/.../billingProfiles/.../fakturor/... /pricesheet/default/Download-format = JSON|CSV Microsoft. fakturering/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Reservationsköp | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Reservations rekommendationer | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Reservations användning | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft. förbrukning/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft. förbrukning/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure-tjänst och Marketplace-användning från tredje part finns i [API för användnings information](/rest/api/consumption/usagedetails).

Följande API: er är tillgängliga för MCA-fakturerings konton:

| Syfte | API för Microsoft Customer Agreement (MCA) |
| --- | --- |
| Fakturerings konton<sup>2</sup> | Microsoft. fakturering/billingAccounts |
| Fakturerings profiler<sup>2</sup> | Microsoft. Billing/billingAccounts/billingProfiles |
| Faktura avsnitt<sup>2</sup> | Microsoft. Billing/billingAccounts/invoiceSections |
| Fakturor | Microsoft. fakturering/billingAccounts/billingProfiles/fakturor |
| Fakturerings prenumerationer | {scope}/billingSubscriptions |

<sup>2</sup> API: er returnerar listor med objekt, som är omfång, där Cost Management upplevelser i Azure Portal och API: er körs. Mer information om Cost Management omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

Om du använder några befintliga EA-API: er måste du uppdatera dem så att de stöder MCA-fakturerings konton. I följande tabell visas andra integrerings ändringar:

| Syfte | Gammalt erbjudande | Nytt erbjudande |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Innehålls paket och anslutnings program för [Microsoft konsumtions Insights](/power-bi/desktop-connect-azure-consumption-insights) |  [Azure Consumption Insights koppling](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API: er för att få saldo och krediter

Med [sammanfattnings](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) -API: et för sammanfattning får du en månatlig Sammanfattning av:

- Saldon
- Nya inköp
- Avgifter för Azure Marketplace-tjänster
- Justeringar
- Kostnader för ersättning av tjänster

Alla förbruknings-API: er ersätts av interna Azure-API: er som använder Azure AD för autentisering och auktorisering. Mer information om hur du anropar Azure REST API: er finns i [komma igång med rest](/rest/api/azure/#create-the-request).

API: t för att hämta balans sammanfattningen ersätts av API: t Microsoft. Billing/billingAccounts/billingProfiles/availableBalance.

Så här hämtar du tillgängliga saldon med det tillgängliga balanserings-API: et:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API: er för att få kostnad och användning

Få en daglig uppdelning av kostnader från Azure Service-användning, användning av marknads platser från tredje part och andra marknads plats köp med följande API: er. Följande separata API: er slogs samman för Azure-tjänster och Marketplace-användning från tredje part. De gamla API: erna ersätts av [Microsoft. förbrukning/usageDetails-](/rest/api/consumption/usagedetails) API: et. Det lägger till Marketplace-inköp, som tidigare endast visades i saldot för saldot hittills.

- [Hämta användnings information/Ladda ned](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Hämta användnings information/skicka](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Hämta användnings information/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Hämta användnings information/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Hämta marketplacecharges för Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Hämta marketplacechargesbycustomdate för Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Alla förbruknings-API: er ersätts av interna Azure-API: er som använder Azure AD för autentisering och auktorisering. Mer information om hur du anropar Azure REST API: er finns i [komma igång med rest](/rest/api/azure/#create-the-request).

Alla föregående API: er ersätts av förbruknings-/användnings informationens API.

För att få användnings information med API: t för användnings information:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

API för användnings information, precis som med alla Cost Management API: er, finns i flera omfång. För fakturerade kostnader, som du traditionellt skulle ta emot på registrerings nivå, använder du fakturerings profil omfånget.  Mer information om Cost Management omfattningar finns i [förstå och arbeta med omfattningar](understand-work-scopes.md).

| Typ | ID-format |
| --- | --- |
| Faktureringskonto | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Faktureringsprofil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Prenumeration | `/subscriptions/{subscriptionId}` |
| Resursgrupp | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Använd följande QueryString-parametrar för att uppdatera eventuell programmerings kod.

| Gamla parametrar | Nya parametrar |
| --- | --- |
| `billingPeriod={billingPeriod}` | Stöds inte |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Bröd texten i svaret har också ändrats.

Gammal svars text:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nytt svars text:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Egenskaps namnet som innehåller matrisen med användnings poster som har ändrats från data till _värden_. Varje post som används för att ha en plan lista med detaljerade egenskaper. Men varje post finns nu i en kapslad egenskap med namnet _Properties_, förutom taggar. Den nya strukturen stämmer överens med andra Azure API: er. Vissa egenskaps namn har ändrats. I följande tabell visas motsvarande egenskaper.

| Tidigare egenskap | Ny egenskap | Anteckningar |
| --- | --- | --- |
| AccountId | Gäller inte | Prenumerationens skapare spåras inte. Använd invoiceSectionId (samma som departmentId). |
| AccountNameAccountOwnerId och AccountOwnerEmail | Gäller inte | Prenumerationens skapare spåras inte. Använd invoiceSectionName (samma som departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Observera att dessa egenskaper är motstående. Om isAzureCreditEnabled är sant skulle ChargesBilledSeparately vara falskt. |
| ConsumedQuantity | kvantitet | &nbsp; |
| ConsumedService | consumedService | De exakta sträng värdena kan skilja sig. |
| ConsumedServiceId | Inget | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Datum-och usageStartDate | date | &nbsp;  |
| Day | Inget | Tolkar dag från datum. |
| DepartmentId | invoiceSectionId | Exakta värden skiljer sig åt. |
| DepartmentName | invoiceSectionName | De exakta sträng värdena kan skilja sig. Konfigurera faktura avsnitt så att de matchar avdelningar, om det behövs. |
| ExtendedCost och kostnad | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Är en återkommande avgift | Inget | &nbsp;  |
| Location | location | &nbsp;  |
| MeterCategory | meterCategory | De exakta sträng värdena kan skilja sig. |
| MeterId | meterId | Exakta sträng värden skiljer sig åt. |
| MeterName | meterName | De exakta sträng värdena kan skilja sig. |
| MeterRegion | meterRegion | De exakta sträng värdena kan skilja sig. |
| MeterSubCategory | meterSubCategory | De exakta sträng värdena kan skilja sig. |
| Månad | Inget | Tolkar månad från datum. |
| Erbjudandets namn | Inget | Använd publisherName och productOrderName. |
| offerID | Inget | &nbsp;  |
| Ordernummer | Inget | &nbsp;  |
| ArtNr | Inget | Använd meterId och productOrderName för att identifiera priserna unikt. |
| Plannamn | productOrderName | &nbsp;  |
| Produkt | Produkt |   |
| ProductId | productId | Exakta sträng värden skiljer sig åt. |
| Utgivarens namn | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Exakta sträng värden skiljer sig åt. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Inget | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | Gäller inte | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| Tjänstnamn | meterCategory | De exakta sträng värdena kan skilja sig. |
| ServiceTier | meterSubCategory | De exakta sträng värdena kan skilja sig. |
| StoreServiceIdentifier | Gäller inte | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Taggar | tags | Egenskapen Taggar gäller för rotobjektet, inte egenskapen kapslade egenskaper. |
| UnitOfMeasure | unitOfMeasure | Exakta sträng värden skiljer sig åt. |
| usageEndDate | date | &nbsp;  |
| Year | Inget | Tolkar året från datum. |
| (ny) | billingCurrency | Valuta som används för avgiften. |
| (ny) | billingProfileId | Unikt ID för fakturerings profilen (samma som registreringen). |
| (ny) | billingProfileName | Namnet på fakturerings profilen (samma som registreringen). |
| (ny) | chargeType | Använd för att differentiera användningen av Azure-tjänster, Marketplace-användning och köp. |
| (ny) | invoiceId | Unikt ID för fakturan. Tomt för den aktuella, öppna månaden. |
| (ny) | publisherType | Typ av utgivare för inköp. Tom för användning. |
| (ny) | serviceFamily | Typ av köp. Tom för användning. |
| (ny) | servicePeriodEndDate | Slutdatum för den köpta tjänsten. |
| (ny) | servicePeriodStartDate | Start datum för den köpta tjänsten. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API: er för fakturerings perioder ersatt av fakturor-API

MCA-fakturerings konton använder inte fakturerings perioder. I stället använder de fakturor för omfångs kostnader för vissa fakturerings perioder. [API: et för fakturerings perioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) ersätts av fakturornas API. Alla förbruknings-API: er ersätts av interna Azure-API: er som använder Azure AD för autentisering och auktorisering. Mer information om hur du anropar Azure REST API: er finns i [komma igång med rest](/rest/api/azure/#create-the-request).

Så här hämtar du fakturor med API: er för fakturor:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Pris dokument-API: er

Det här avsnittet beskriver befintliga pris dokument-API: er och ger rekommendationer för att gå över till pris dokumentets API för Microsofts kund avtal. Den diskuterar även pris dokumentets API för Microsofts kund avtal och förklarar fält i pris dokumenten. De här API: erna för att [få pris dokument för företag](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) och [Enterprise Erhåll](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) ersätts av pris dokumentets API för Microsofts kund avtal (Microsoft. Billing/billingAccounts/billingProfiles/pris dokument). Det nya API: et stöder både JSON-och CSV-format, i asynkrona REST-format. Alla förbruknings-API: er ersätts av interna Azure-API: er som använder Azure AD för autentisering och auktorisering. Mer information om hur du anropar Azure REST API: er finns i [komma igång med rest](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Fakturerings företags-API: er

Du använde fakturerings företags-API: er med företags registreringar för att få information om pris-och fakturerings perioder. Autentisering och auktorisering som används Azure Active Directory-webbtoken.

Så här hämtar du priser för den angivna företags registreringen med API: er för pris dokument och fakturerings perioder:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| HÄMTA | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Pris dokument-API för Microsofts kund avtal

Använd pris dokumentets API för Microsofts kund avtal för att se priser för alla förbruknings tjänster för Azure-förbrukning och Marketplace. Priserna som visas för fakturerings profilen gäller för alla prenumerationer som tillhör fakturerings profilen.

Använd pris dokumentets API för att visa alla pris dokument data i Azure förbruknings tjänster i CSV-format:

| Metod | Begärd URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Använd pris dokumentets API för att visa alla pris dokument data i Azure förbruknings tjänster i JSON-format:

| Metod | Begärd URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Med API: et returneras pris dokumentet för hela kontot. Men du kan också få en komprimerad version av pris dokumentet i PDF-format. Sammanfattningen innehåller Azure-förbrukning och Marketplace-konsumtions tjänster som faktureras för en speciell faktura. Fakturan identifieras av {invoiceId}, som är samma som **faktura numret** som visas i PDF-filerna för faktura sammanfattning. Här är ett exempel.

![Exempel bild som visar faktura numret som motsvarar InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Så här visar du faktura information med pris dokumentets API i CSV-format:

| Metod | Begärd URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Så här visar du faktura information med pris dokumentets API i JSON-format:

| Metod | Begärd URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Du kan också se uppskattade priser för alla Azure-konsumtioner eller Marketplace-förbruknings tjänster i den aktuella öppna fakturerings perioden eller service perioden.

Visa uppskattade priser för förbruknings tjänster med pris dokumentets API i CSV-format:

| Metod | Begärd URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Visa uppskattade priser för förbruknings tjänster med pris dokumentets API i JSON-format:

| Metod | Begärd URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Pris dokuments-API: erna för Microsoft kund avtal är *asynkrona REST API: er*. Svaren för API: erna har ändrats från äldre synkrona API: er. Bröd texten i API-svaret har också ändrats.

#### <a name="old-response-body"></a>Gammal svars text

Här är ett exempel på synkrona REST API-svar:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Ny svars text

API: erna stöder [Azure rest-asynkront](../../azure-resource-manager/management/async-operations.md) format. Anropa API: et med GET och du får följande svar:

```
No Response Body

HTTP Status 202 Accepted
```

Följande rubriker skickas med platsen för utdata:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Gör ett annat anrop till platsen. Svaret på GET-anropet är detsamma tills åtgärden når ett slut för ande eller haveri läge. När det är slutfört, returnerar svaret på hämtnings platsen hämtnings-URL: en. Precis som om åtgärden utfördes samtidigt. Här är ett exempel:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Klienten kan också göra ett GET-anrop för `Azure-AsyncOperation`. Slut punkten returnerar status för åtgärden.

I följande tabell visas fält i det äldre API: t för att få pris dokument i Enterprise. Den innehåller motsvarande fält i det nya pris dokumentet för Microsofts kund avtal:

| Tidigare egenskap | Ny egenskap | Anteckningar |
| --- | --- | --- |
| billingPeriodId  | _Ej tillämpligt_ | Inte tillämpligt. För Microsofts kund avtal ersätter fakturan och det tillhör ande pris dokumentet begreppet billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | De exakta sträng värdena kan skilja sig. |
| includedQuantity  | includedQuantity | Ej tillämpligt för tjänster i Microsofts kund avtal. |
| partNumber  | _Ej tillämpligt_ | Använd i stället en kombination av productOrderName (samma som offerID) och meterID. |
| unitPrice  | unitPrice | Enhets priset gäller för tjänster som förbrukas i Microsofts kund avtal. |
| currencyCode  | pricingCurrency | Microsofts kund avtal har pris representationer i pris-och fakturerings valuta. CurrencyCode motsvarar pricingCurrency i Microsofts kund avtal. |
| offerID | productOrderName | I stället för OfferID kan du använda productOrderName, men är inte samma som OfferID. ProductOrderName och mätaren fastställer dock priser i Microsofts kund avtal som rör meterId och OfferID i äldre registreringar. |

## <a name="consumption-price-sheet-api-operations"></a>API-åtgärder för förbruknings pris dokument

För Enterprise-avtal använde du förbruknings pris bladet API [Get](/rest/api/consumption/pricesheet/get) och [Get av fakturerings periods](/rest/api/consumption/pricesheet/getbybillingperiod) åtgärder för ett omfång av subscriptionId eller en fakturerings period. API: et använder Azure Resource Management-autentisering.

Så här hämtar du pris dokument information för ett omfång med pris dokumentets API:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Så här hämtar du pris dokument information per fakturerings period med pris dokumentets API:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

I stället för API-slutpunkterna ovan använder du följande för Microsofts kund avtal:

**Pris dokument-API för Microsofts kund avtal (asynkron REST API)**

Detta API är för Microsofts kund avtal och tillhandahåller ytterligare attribut.

**Pris dokument för en fakturerings profil omfattning i ett fakturerings konto**

Detta API är det befintliga API: et. Den har uppdaterats för att tillhandahålla pris dokumentet för en fakturerings profil i ett fakturerings konto.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Pris dokument för ett omfång efter fakturerings konto

Azure Resource Manager autentisering används när du hämtar pris dokumentet vid registrerings omfånget i ett fakturerings konto.

Så här hämtar du pris dokumentet på registrerings kontot på ett fakturerings konto:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Använd informationen i följande avsnitt för ett Microsofts kund avtal. Det innehåller fält egenskaper som används för Microsofts kund avtal.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Pris dokument för en fakturerings profil omfattning i ett fakturerings konto

Det uppdaterade pris dokumentet per fakturerings konto API hämtar pris dokumentet i CSV-format. Så här hämtar du pris dokumentet i fakturerings profil omfånget för en MCA:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

API-svar och egenskaper är identiska i EA-registrerings omfånget. Egenskaperna motsvarar samma MCA-egenskaper.

De äldre egenskaperna för [Azure Resource Manager pris dokuments-API: er](/rest/api/consumption/pricesheet) och samma nya egenskaper finns i följande tabell.

| Gammal Azure Resource Manager API-egenskap för pris dokument  | Ny API-egenskap för pris dokument i Microsoft kund avtal   | Beskrivning |
| --- | --- | --- |
| Mätar-ID | _meterId_ | Unik identifierare för mätaren. Samma som meterID. |
| Mätar namn | meterName | Namnet på mätaren. Mätaren visar den Azure-tjänst som är distributions bara resurs. |
| Mätar kategori  | tjänst | Namnet på mätarens klassificeringskategori. Samma som tjänsten i pris dokumentet för Microsofts kund avtal. Exakta sträng värden skiljer sig åt. |
| Mätar under kategori | meterSubCategory | Namn på kategorin för mätarens underklassificering. Baserat på klassificeringen av funktions uppsättnings differentiering på hög nivå i tjänsten. Till exempel Basic SQL DB vs standard SQL DB. |
| Mätar region | meterRegion | &nbsp;  |
| Enhet | _Ej tillämpligt_ | Kan parsas från unitOfMeasure. |
| Måttenhet | unitOfMeasure | &nbsp;  |
| Del nummer | _Ej tillämpligt_ | Använd productOrderName och MeterID för att unikt identifiera priset för en fakturerings profil i stället för del nummer. Fälten visas på MCA-fakturan i stället för del numret i MCA-fakturor. |
| Enhetspris | unitPrice | Microsofts kund avtal enhets pris. |
| Valuta kod | pricingCurrency | Microsofts kund avtal representerar priser i pris valutan och fakturerings valutan. Valuta koden är samma som pricingCurrency i Microsofts kund avtal. |
| Kvantitet som ingår | includedQuantity | Ej tillämpligt för tjänster i Microsofts kund avtal. Visa med värden noll. |
|  Erbjudande-ID  | productOrderName | Använd productOrderName i stället för OfferID. Inte detsamma som OfferID, men productOrderName och mätaren fastställer priser i Microsofts kund avtal. Relaterat till meterId och OfferID i äldre registreringar. |

Priset för Microsofts kund avtal definieras annorlunda än för företags avtal. Priset för tjänster i företags registreringen är unikt för produkt, del nummer, mätare och erbjudande. Del numret används inte i Microsofts kund avtal.

Priset för Azure-konsumtions tjänsten som är en del av ett Microsofts kund avtal är unikt för productOrderName och meterID. De representerar tjänst mätaren och produkt planen.

Om du vill stämma av mellan pris dokumentet och användningen i API för användnings information kan du använda productOrderName och meterID.

Användare som har behörighet för fakturerings profil ägare, deltagare, läsare och faktura hanterare kan hämta pris dokumentet.

Pris dokumentet innehåller priser för tjänster vars pris baseras på användning. Tjänsterna omfattar användning av Azure-förbrukning och Marketplace. Det senaste priset i slutet av varje service period är låst och tillämpas på användning i en enda service period. För Azures förbruknings tjänster är service perioden vanligt vis en kalender månad.

### <a name="retired-price-sheet-api-fields"></a>Återkallade pris dokument API-fält

Följande fält är antingen inte tillgängliga i pris dokuments API: er för Microsoft kund avtal eller har samma fält.

|Tillbakadraget fält| Beskrivning|
|---|---|
| billingPeriodId | Ingen tillämplig. Motsvarar InvoiceId för MCA. |
| offerID | Inte tillämpligt. Motsvarar productOrderName i MCA. |
| meterCategory  | Inte tillämpligt. Motsvarar tjänsten i MCA. |
| enhet | Inte tillämpligt. Kan parsas från unitOfMeasure. |
| currencyCode | Samma som pricingCurrency i MCA. |
| meterLocation | Samma som meterRegion i MCA. |
| partNumber partnumber | Inte tillämpligt eftersom del numret inte visas i MCA-fakturor. I stället för del nummer använder du kombinationen meterId och productOrderName för att identifiera priserna unikt. |
| totalIncludedQuantity | Inte tillämpligt. |
| pretaxStandardRate  | Inte tillämpligt. |

## <a name="reservation-instance-charge-api-replaced"></a>Avgifts-API för reservations instans ersatt

Du kan få fakturerings transaktioner för reservations köp med den [reserverade instansen avgifts-API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Det nya API: t innehåller alla inköp, inklusive erbjudanden från tredje parts Marketplace. Alla förbruknings-API: er ersätts av interna Azure-API: er som använder Azure AD för autentisering och auktorisering. Mer information om hur du anropar Azure REST API: er finns i [komma igång med rest](/rest/api/azure/#create-the-request). Avgifts-API: t för reserverad instans ersätts av API: et för transaktioner

Så här hämtar du reservations inköps transaktioner med transaktions-API:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Rekommendationer för API: er ersatta

Reserverade instanser köp rekommendationer för användning av virtuella datorer under de senaste 7, 30 eller 60 dagarna. API: er tillhandahåller även rekommendationer för reservations köp. De omfattar:

- [Rekommendations-API för delad reserverad instans](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Rekommendations-API för enkel reserverad instans](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Alla förbruknings-API: er ersätts av interna Azure-API: er som använder Azure AD för autentisering och auktorisering. Mer information om hur du anropar Azure REST API: er finns i [komma igång med rest](/rest/api/azure/#create-the-request). API: erna för reservations rekommendationerna ovan har ersatts av [Microsoft. förbrukning/reservationRecommendations-](/rest/api/consumption/reservationrecommendations/list) API: et.

För att få reservations rekommendationer med reservations rekommendationernas API:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>API: er för reservations användning byts ut

Du kan få reservations användning i en registrering med Reserverade instanser – användning-API: et. Om det finns fler än en reserverad instans i en registrering kan du också få all användning av alla reserverade instans inköp med detta API.

De omfattar:

- [Reserverade instanser – användning information](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Reserverade instanser – användning Sammanfattning](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Alla förbruknings-API: er ersätts av interna Azure-API: er som använder Azure AD för autentisering och auktorisering. Mer information om hur du anropar Azure REST API: er finns i [komma igång med rest](/rest/api/azure/#create-the-request). API: erna för reservations rekommendationerna ovan har ersatts av API: erna [Microsoft. förbrukning/reservationDetails](/rest/api/consumption/reservationsdetails) och [Microsoft. förbrukning/reservationSummaries](/rest/api/consumption/reservationssummaries) .

Så här hämtar du reservations information med reservations informationens API:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Så här hämtar du reservations sammanfattningar med reservations sammanfattningar API:

| Metod | Begärd URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Flytta från Cloudyn till Cost Management

Organisationer som använder [Cloudyn](https://cloudyn.com) bör börja använda [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) för eventuella kostnads hanterings behov. Cost Management är tillgängligt i Azure Portal utan onboarding och en svars tid på åtta timmar. Mer information finns i Cost Management- [dokumentationen](../index.yml).

Med Azure Cost Management kan du:

- Visa kostnader över tid mot en fördefinierad budget. Analysera mönster i dagliga kostnader för att identifiera och stoppa avvikelser i utgifterna. Dela upp kostnader efter taggar, resurs grupp, tjänst och plats.
- Skapa budgetar för att ange gränser för användning och kostnader och få meddelanden när viktiga tröskelvärden närmar sig. Konfigurera automatisering med åtgärdsgrupper för att utlösa anpassade händelser och genomtvinga hårda gränser på dina villkor.
- Optimera kostnader och användning med rekommendationer från Azure Advisor. Identifiera inköps optimeringar med reservationer, downsize-underutnyttjade virtuella datorer och ta bort oanvända resurser för att hålla dig inom budgetarna.
- Schemalägg en export av kostnader och användnings data för att publicera en CSV-fil till ditt lagrings konto dagligen. Automatisera integreringen med externa system för att hålla fakturerings data synkroniserade och uppdaterade.

## <a name="power-bi-integration"></a>Power BI-integrering

Du kan också använda Power BI för kostnads rapportering. [Azure Cost Management anslutningen](/power-bi/desktop-connect-azure-cost-management) för Power BI Desktop kan användas för att skapa kraftfulla, anpassade rapporter som hjälper dig att förstå dina Azure-utgifter bättre. Azure Cost Management Connector stöder för närvarande kunder med antingen ett Microsoft-kundavtal eller ett Enterprise-avtal (EA).

## <a name="next-steps"></a>Nästa steg

- Läs [Cost Management-dokumentationen](../index.yml) om du vill lära dig att övervaka och kontrol lera Azure-utgifter. Eller, om du vill optimera resurs användningen med Cost Management.
