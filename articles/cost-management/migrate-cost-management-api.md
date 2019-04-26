---
title: 'Migrera från Enterprise-avtal till Microsoft Customer avtal API: er – Azure | Microsoft Docs'
description: Den här artikeln hjälper dig att förstå konsekvenserna av migreringen Microsoft Enterprise Agreement (EA) till en Microsoft-kundavtal.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: e4c5607089efb247620766fb311b97cae3772770
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311951"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migrera från Enterprise-avtal till Microsoft Customer avtal API: er

Den här artikeln hjälper dig att förstå datastrukturen, API och andra system integration skillnader mellan Enterprise Agreement (EA) och Microsoft Customer avtal MCA ()-konton. Azure Cost Management stödjer API: er för båda typerna. Granska den [konfigurera faktureringskonto för](../billing/billing-mca-setup-account.md) Microsoft kundavtal artikeln innan du fortsätter.

Organisationer med ett befintligt EA-konto bör granska den här artikeln i samband med att skapa en MCA-konto. Tidigare krävs förnyar ett EA-konto vissa minimalt arbete att flytta från en gammal registrering till en ny. Migrera till ett konto för MCA kräver dock extra arbete. Extra arbete är på grund av ändringar i det underliggande fakturering undersystemet som påverkar alla cost-relaterade API: er och Tjänsterbjudanden.

## <a name="mca-apis-and-integration"></a>MCA API: er och integration

MCA API: er och nya integreringen kan du:

- Ha fullständig API-tillgänglighet via API: er med inbyggda Azure.
- Konfigurera flera fakturor i ett enda faktureringskonto.
- Få åtkomst till en kombinerad API med Azure-tjänst-användning, från tredje part Marketplace användning och Marketplace-köp.
- Visa kostnader över fakturering profiler (samma som registreringar) med hjälp av Azure Cost Management.
- Åtkomst till nya API: er för att visa kostnader, bli meddelad när kostnader överskrider fördefinierade trösklar och rådata automatiskt.

## <a name="migration-checklist"></a>Checklista för migrering

Följande objekt hjälp du övergår till MCA API: er.

- Bekanta dig med den nya [Microsoft kundavtal fakturering konto](../billing/billing-mca-overview.md).
- Avgör vilka API: er som du använder och se vilka som har ersatts i följande avsnitt.
- Bekanta dig med [Azure Resource Manager REST API: er](/rest/api/azure).
- Om du inte redan använder Azure Resource Manager API: er, [registrera din klientapp med Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Uppdatera någon programmering kod [Använd Azure AD-autentisering](/rest/api/azure/#create-the-request).
- Uppdatera programming kod för att ersätta EA API-anrop med MCA API-anrop.
- Uppdatera felhantering för att använda nya felkoder.
- Granska ytterligare integrering erbjudanden som Cloudyn och Power BI för andra som krävs för åtgärden.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA API: er som ersätts med MCA API: er

EA API: erna använder API-nyckel för autentisering och auktorisering. MCA API: erna använder Azure AD-autentisering.

| Syfte | EA API | MCA API |
| --- | --- | --- |
| Saldo och krediter | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Usage (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Användning (CSV) | [ladda ned/usagedetails/](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) [ /usagedetails/skicka](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-användning (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Fakturering punkter | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Prisdokument | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format = json|CSV Microsoft.Billing/billingAccounts/.../billingProfiles/.../invoices/... /pricesheet/default/Download format = json|CSV Microsoft.Billing/billingAccounts/... / billingProfiles /... /providers/Microsoft.consumption/pricesheets/Download  |
| Köp av reservation | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Rekommendationer för reservation | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Reservation-användning | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> azure-tjänsten och tredje parts Marketplace användning är tillgängliga med den [användning information API](/rest/api/consumption/usagedetails).

Följande API: er är tillgängliga för MCA fakturering konton:

| Syfte | Microsoft-kundernas avtal (MCA) API |
| --- | --- |
| Fakturering konton<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Fakturering profiler<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Fakturera avsnitt<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Fakturor | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Fakturering prenumerationer | {scope}/billingSubscriptions |

<sup>2</sup> API: erna returnerar en lista över objekt som är scope, där kostnadshantering inträffar i Azure portal och API: er fungerar. Läs mer om Cost Management scope [förstå och arbeta med omfattningar](understand-work-scopes.md).

Om du använder alla befintliga EA-API: er, måste du uppdatera dem för att stödja MCA fakturering konton. I följande tabell visar andra integration ändringar:

| Syfte | Gamla erbjudande | Nya erbjudandet |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) innehållspaket och anslutning | [Microsoft Azure Consumption Insights Power BI-appen](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) och [Azure Consumption Insights-anslutningsapp](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API: er för att få balans och krediter

Den [få balans sammanfattning](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API ger dig månatligen:

- Saldon
- Nya inköp
- Tjänstavgifter för Azure Marketplace
- Justeringar
- Tjänsteöverförbrukningsavgifter

Alla förbrukning API: er har ersatts av interna Azure API: er som använder Azure AD för autentisering och auktorisering. Mer information om anropa Azure REST API: er finns i [komma igång med REST](/rest/api/azure/#create-the-request).

Hämta saldo sammanfattning API ersätts av Microsoft.Billing/billingAccounts/billingProfiles/availableBalance-API: et.

Hämta tillgängliga saldo med tillgängliga saldo API:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API: er för att hämta kostnader och användning

Få en daglig sammanställning av kostnaderna från Azure-tjänst-användning, från tredje part Marketplace användning och andra Marketplace-köp med följande API: er. Följande separat API: er har sammanfogats för Azure-tjänster och tredje parts Marketplace användning. De gamla API: er har ersatts av den [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API. Det lägger till Marketplace-köp som har tidigare endast visas i saldot sammanfattningen hittills.

- [Hämta användning detalj/nedladdning](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Hämta användning detalj/skicka](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Hämta information om användning/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Hämta information om användning/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Hämta marketplace store kostnad/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Hämta marketplace store kostnad/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Alla förbrukning API: er har ersatts av interna Azure API: er som använder Azure AD för autentisering och auktorisering. Mer information om anropa Azure REST API: er finns i [komma igång med REST](/rest/api/azure/#create-the-request).

Alla föregående API: er har ersatts av förbrukningen/användningen information om API: et.

Hämta information om användning med API för användning-information:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

API: et för användning information är precis som med alla Cost Management API: er, tillgänglig på flera scope. Fakturerade kostnader som du normalt sett skulle få på registreringsnivå kan använda i området fakturering profil.  Läs mer om Cost Management scope [förstå och arbeta med omfattningar](understand-work-scopes.md).

| Typ | ID-format |
| --- | --- |
| Faktureringskonto | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Faktureringsprofil | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Prenumeration | `/subscriptions/{subscriptionId}` |
| Resursgrupp | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Använd följande parametrar i frågesträngen för att uppdatera programming kod.

| Gamla parametrar | Nya parametrar |
| --- | --- |
| `billingPeriod={billingPeriod}` | Stöds inte |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Brödtexten i svaret ändras också.

Gamla svarstext:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Ny svarstext:

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

Egenskapsnamnet som innehåller den användningsposter-matrisen som har ändrats från data till _värden_. Varje post som används för att ha en plan lista med detaljerade egenskaper. Men varje post nu alla detaljer är nu i en kapslad egenskap med namnet _egenskaper_, utom etiketter. Den nya strukturen är konsekvent med andra Azure-API: er. Vissa egenskapsnamn har ändrats. I följande tabell visas motsvarande egenskaper.

| Gamla egenskapen | Ny egenskap | Anteckningar |
| --- | --- | --- |
| Konto-ID | Gäller inte | Den prenumeration som har skaparen är inte spåras. Använd invoiceSectionId (samma som departmentId). |
| AccountNameAccountOwnerId och AccountOwnerEmail | Gäller inte | Den prenumeration som har skaparen är inte spåras. Använd invoiceSectionName (samma som departmentName). |
| Ytterligare information | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Observera att de här egenskaperna är opposites. Om isAzureCreditEnabled är true, är ChargesBilledSeparately FALSKT. |
| Använd kvantitet | kvantitet | &nbsp; |
| Använd tjänst | consumedService | Exakta strängvärden kan variera. |
| ID för använd tjänst | Ingen | &nbsp; |
| Kostnadsställe | costCenter | &nbsp; |
| Datum och usageStartDate | date | &nbsp;  |
| Dag | Ingen | Parsar dagen från datum. |
| Avdelnings-ID | InvoiceSectionId | Exakta värden skiljer sig åt. |
| Avdelningsnamn | InvoiceSectionName | Exakta strängvärden kan variera. Konfigurera faktura avsnitt om du vill matcha avdelningar, om det behövs. |
| ExtendedCost och kostnader | costInBillingCurrency | &nbsp;  |
| Instans-ID | resourceId | &nbsp;  |
| Är en återkommande debitering | Ingen | &nbsp;  |
| Location | location | &nbsp;  |
| Mätarkategori | meterCategory | Exakta strängvärden kan variera. |
| Mätar-ID | meterId | Exakta strängvärden skiljer sig åt. |
| Mätarnamn | meterName | Exakta strängvärden kan variera. |
| Mätarregion | meterRegion | Exakta strängvärden kan variera. |
| Underkategori för mätare | meterSubCategory | Exakta strängvärden kan variera. |
| Månad | Ingen | Parsar månad från datum. |
| Erbjudandets namn | Ingen | Använd publisherName och productOrderName. |
| OfferId | Ingen | &nbsp;  |
| Ordernummer | Ingen | &nbsp;  |
| PartNumber | Ingen | Använd meterId och productOrderName för att unikt identifiera priser. |
| Plannamn | productOrderName | &nbsp;  |
| Product | Product |   |
| Produkt-ID | productId | Exakta strängvärden skiljer sig åt. |
| Utgivarens namn | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Exakta strängvärden skiljer sig åt. |
| Resursplats | resourceLocation | &nbsp;  |
| ID för resursplats | Ingen | &nbsp;  |
| Resurskostnad | effectivePrice | &nbsp;  |
| ID för tjänstadministratör | Gäller inte | &nbsp;  |
| Tjänstinformation 1 | serviceInfo1 | &nbsp;  |
| Tjänstinformation 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Exakta strängvärden kan variera. |
| ServiceTier | meterSubCategory | Exakta strängvärden kan variera. |
| ID för lagringstjänst | Gäller inte | &nbsp;  |
| Prenumerations-GUID | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Prenumerationsnamn | subscriptionName | &nbsp;  |
| Taggar | tags | Egenskapen taggar gäller för rotobjektet inte till egenskapen kapslade egenskaper. |
| Måttenhet | unitOfMeasure | Exakta strängvärden skiljer sig åt. |
| usageEndDate | date | &nbsp;  |
| År | Ingen | Parsar år från skapandedatum. |
| (ny) | billingCurrency | Valutan som används för tillägget. |
| (ny) | billingProfileId | Unikt ID för fakturering profilen (samma som registrering). |
| (ny) | billingProfileName | Namnet på den fakturering profilen (samma som registrering). |
| (ny) | chargeType | Använd för att skilja mellan Azure-tjänst-användning, Marketplace användning och inköp. |
| (ny) | invoiceId | Unikt ID för fakturan. Tom för den aktuella, öppna månaden. |
| (ny) | publisherType | Typ av utgivare för köp. Tom för användning. |
| (ny) | serviceFamily | Typ av köp. Tom för användning. |
| (ny) | servicePeriodEndDate | Slutdatum för köpt tjänsten. |
| (ny) | servicePeriodStartDate | Startdatum för köpta-tjänsten. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Fakturerings-API för perioder ersatts av fakturor API

MCA fakturering konton använda inte faktureringsperioder. I stället använder de fakturor omfång kostnader till specifika faktureringsperioder. Den [Billing perioder API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) ersätts av fakturor API: et. Alla förbrukning API: er har ersatts av interna Azure API: er som använder Azure AD för autentisering och auktorisering. Mer information om anropa Azure REST API: er finns i [komma igång med REST](/rest/api/azure/#create-the-request).

Hämta fakturor med fakturor-API:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Price Sheet API: er

Det här avsnittet beskriver befintliga Price Sheet API: er och ger rekommendationer för att flytta till Price Sheet API för Microsoft kundens avtal. Dessutom beskrivs Price Sheet API för Microsoft Customer-avtal och förklarar fält i prisdokument. Den [Enterprise hämta prisdokument](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) och [Enterprise hämta faktureringsperioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API: er har ersatts av Price Sheet API för Microsoft Customer-avtal (Microsoft.Billing/billingAccounts/billingProfiles / prisdokument). De nya API: et stöder både JSON och CSV-format i asynkrona REST-format. Alla förbrukning API: er har ersatts av interna Azure API: er som använder Azure AD för autentisering och auktorisering. Mer information om anropa Azure REST API: er finns i [komma igång med REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>Fakturering Enterprise API: er

Du använde fakturering Enterprise API: er med Enterprise-registreringar för att hämta pris- och period faktureringsinformation. Autentisering och auktorisering används för Azure Active Directory-webb-token.

Hämta priser för den angivna Företagsregistrering med Prisdokument och Billing Period API: er:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| HÄMTA | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Pris-blad API för Microsoft-kundernas avtal

Använd pris blad API för Microsoft Customer avtal för att visa priser för alla Azure-förbrukning och Marketplace-tjänst. Priserna som visas för fakturering profilen gäller för alla prenumerationer som hör till fakturering profilen.

Använd Price Sheet API: et för att visa alla Azure-förbrukning services Prisdokument-data i CSV-format:

| Metod | Förfrågans URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Använd Price Sheet API: et för att visa alla Azure-förbrukning tjänster Prisdokument data i JSON-format:

| Metod | Förfrågans URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Med hjälp av API: et returnerar prisdokument för hela kontot. Du kan också få en komprimerad version av prisdokumentet i PDF-format. Sammanfattningen innehåller Azure-förbrukning och Marketplace-tjänst som faktureras för en viss faktura. Fakturan har identifierats av {invoiceId}, vilket är samma som den **fakturanummer** visas på fakturan sammanfattning PDF-filer. Här är ett exempel.

![Exempelbild som visar en faktura: talet som motsvarar till InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Visa fakturainformation med Price Sheet API i CSV-format:

| Metod | Förfrågans URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Visa fakturainformation med Price Sheet API i JSON-Format:

| Metod | Förfrågans URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Du kan också se uppskattade priser för alla Azure-förbrukning eller Marketplace förbrukning tjänster i den aktuella öppna faktureringsperiod eller serviceperioden.

Visa beräknad priserna för tjänster med Price Sheet API i CSV-format:

| Metod | Förfrågans URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Visa beräknad priserna för tjänster med Price Sheet API i JSON-format:

| Metod | Förfrågans URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft Customer avtal Price Sheet API: er *asynkrona REST API: er*. Svar för API: erna ändras från den äldre synkron API: er. Brödtexten i API-svaret ändras också.

#### <a name="old-response-body"></a>Gamla svarstexten

Här är ett exempel på synkron REST API-svaret:

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

#### <a name="new-response-body"></a>Ny svarstexten

Stöd för API: erna i [Azure REST asynkrona](../azure-resource-manager/resource-manager-async-operations.md) format. Anropa API med hjälp av GET och du får följande svar:

```
No Response Body

HTTP Status 202 Accepted
```

Följande huvuden skickas med platsen för utdata:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Kontrollera en annan GET anrop till platsen. Svaret på GET-anrop är samma tills åtgärden tillståndet klart eller har misslyckats. När du är klar, returnerar svaret på GET-anropsplats nedladdnings-URL. Precis som om åtgärden utfördes på samma gång. Här är ett exempel:

```
HTTP Status 200

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

Klienten kan också göra en GET-anrop den `Azure-AsyncOperation`. Slutpunkten returnerar status för åtgärden.

I följande tabell visar fälten i äldre Enterprise hämta price sheet API: et. Det innehåller motsvarande fält i det nya prisdokumentet för Microsoft Customer avtal:

| Gamla egenskapen | Ny egenskap | Anteckningar |
| --- | --- | --- |
| billingPeriodId  | _Inte tillämpligt_ | Inte tillämpligt. Avtal för Microsoft-kund ersättas faktura och associerade prisdokument i begreppet billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Exakta strängvärden kan variera. |
| includedQuantity  | includedQuantity | Ej tillämpligt för tjänster i Microsoft kundens avtal. |
| partNumber  | _Inte tillämpligt_ | Använd istället en kombination av productOrderName (samma som offerId) och meterid. |
| unitPrice  | unitPrice | Enhetspriset kan användas för tjänster du förbrukar i Microsoft kundens avtal. |
| currencyCode  | pricingCurrency | Microsoft Customer avtal har pris garantier i pris valuta och valuta. CurrencyCode motsvarar pricingCurrency i Microsoft kundens avtal. |
| offerId | productOrderName | I stället för OfferId, du kan använda productOrderName men är inte densamma som OfferId. ProductOrderName och mätaren fastställa priser i Microsoft Customer avtal rör meterId och Offerid i äldre registreringar. |

## <a name="consumption-price-sheet-api-operations"></a>Förbrukning Price Sheet API-åtgärder

För Enterprise-avtal som du använde förbrukning Price Sheet API [hämta](/rest/api/consumption/pricesheet/get) och [hämta av faktureringsperiod](/rest/api/consumption/pricesheet/getbybillingperiod) åtgärder för ett omfång av prenumerations-ID eller en faktureringsperiod. API: et använder Azure Resource Manager-autentisering.

Att hämta information om Prisdokument för ett omfång med Price Sheet API:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Hämta information för Prisdokument genom faktureringsperiod med Price Sheet API:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

I stället för ovanstående API-slutpunkter, använda följande för Microsoft Customer avtal:

**Price Sheet API för Microsoft Customer-avtal (asynkron REST API)**

Detta API är för Microsoft Customer-avtal och ger ytterligare attribut.

**Prisdokument för ett fakturering profil omfång i ett faktureringskonto**

Detta API är befintliga API: et. Det har uppdaterats för att tillhandahålla prisdokumentet för en profil för fakturering i ett faktureringskonto.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Prisdokument för ett omfång med fakturering konto

Azure Resource Manager-autentisering används när du får Prisdokumentet definitionsområdet registrering i något faktureringskonto.

Hämta Prisdokumentet på konto för enhetsregistreringshanterare i något faktureringskonto:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Använd informationen i följande avsnitt för en Microsoft-kundavtal. Den innehåller fältegenskaper som används för Microsoft Customer avtal.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Prisdokument för ett fakturering profil omfång i ett faktureringskonto

Uppdaterade Prisdokumentet av faktureringskonto API hämtar Prisdokumentet i CSV-format. Hämta Prisdokumentet definitionsområdet fakturering profil för en MCA:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

I omfånget för den EA-registrering är API-svar och egenskaper identiska. Egenskaperna motsvarar samma MCA egenskaper.

Äldre egenskaperna för [Azure Resource Manager pris blad API: er för](/rest/api/consumption/pricesheet) och samma nya egenskaper finns i följande tabell.

| Gamla Azure Resource Manager-priset blad API-egenskap  | Nya Microsoft kunden avtal Price Sheet API-egenskapen   | Beskrivning |
| --- | --- | --- |
| Mätar-ID | _meterId_ | Unik identifierare för mätaren. Samma som meterId. |
| Mätarnamn | meterName | Namnet på mätaren. Mätaren representerar Azure-tjänsten distribuerbar resurs. |
| Mätarkategori  | tjänst | Namnet på klassificeringskategori för mätaren. Samma som tjänsten i Prisdokumentet Microsoft kundens avtal. Exakta strängvärden skiljer sig åt. |
| Mätarunderkategori | meterSubCategory | Namnet på underkategori för etablering. Baserat på klassificeringen av övergripande funktionen set differentiering i tjänsten. Till exempel grundläggande SQL DB jämfört med Standard SQL DB |
| Mätarregion | meterRegion | &nbsp;  |
| Enhet | _Inte tillämpligt_ | Kan parsas från unitOfMeasure. |
| Måttenhet | unitOfMeasure | &nbsp;  |
| Artikelnummer | _Inte tillämpligt_ | I stället för partNumber, använder du productOrderName och MeterId att unikt identifiera priset för en profil för fakturering. Fält visas på fakturan MCA i stället för partNumber i MCA fakturor. |
| A-pris | unitPrice | Microsoft kundavtal enhetspriset. |
| Valutakod | pricingCurrency | Microsoft Customer avtal representerar priserna i priser valuta och fakturering valuta. Valutakod är samma som pricingCurrency i Microsoft kundens avtal. |
| Kvantitet som ingår | includedQuantity | Ej tillämpligt för tjänster i Microsoft kundens avtal. Visa med värdet noll. |
|  Erbjudandets ID  | productOrderName | I stället för OfferId, Använd productOrderName. Inte är densamma som OfferId, men productOrderName och mätaren fastställa priser i Microsoft kundens avtal. Rör meterId och Offerid i äldre registreringar. |

Priset för Microsoft Customer avtal definieras annorlunda än Enterprise-avtal. Priset för tjänster i Enterprise-registrering är unikt för produkten, PartNumber, mätare och erbjudandet. PartNumber används inte i Microsoft kundens avtal.

Priset för Azure Consumption-tjänsten som ingår i en Microsoft-kundavtal är unikt för productOrderName och meterId. De representerar service-mätare och produkt-planen.

För att stämma av mellan prisdokumentet och användning i API: et för användning information, kan du använda productOrderName och meterId.

Användare som har fakturering profilera ägare, deltagare, läsare och fakturera manager rättigheter kan hämta prisdokumentet.

Prisdokumentet innehåller priserna för tjänster vars pris baseras på användning. Tjänsterna omfattar Azure-förbrukning och Marketplace-konsumtion. Det senaste priset i slutet av varje serviceperioden är låst och tillämpas på användning inom en enskild tjänst. För tjänster som Azure-förbrukning är serviceperioden vanligtvis en kalendermånad.

### <a name="retired-price-sheet-api-fields"></a>Indragna Price Sheet API-fält

Följande fält är antingen inte tillgänglig i kund-priset på Microsoft-avtalet blad API: er eller ha samma fält.

|Indragna fält| Beskrivning|
|---|---|
| billingPeriodId | Inte tillämpligt. Motsvarar InvoiceId för MCA. |
| offerId | Inte tillämpligt. Motsvarar productOrderName i MCA. |
| meterCategory  | Inte tillämpligt. Motsvarar tjänsten i MCA. |
| enhet | Inte tillämpligt. Kan parsas från unitOfMeasure. |
| currencyCode | Samma som pricingCurrency i MCA. |
| meterLocation | Samma som meterRegion i MCA. |
| partNumber partnumber | Ej tillämpligt eftersom artikelnumret inte visas i MCA fakturor. I stället för partnumber, att använda meterId och productOrderName tillsammans att unikt identifiera priser. |
| totalIncludedQuantity | Inte tillämpligt. |
| pretaxStandardRate  | Inte tillämpligt. |

## <a name="reservation-instance-charge-api-replaced"></a>Reserverade instans kostnad API ersättas

Du kan hämta fakturering transaktioner för reservation inköp hos den [reserverad instans kostnad API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Den nya API: et innehåller alla köp, inklusive tredje parts Marketplace-erbjudanden. Alla förbrukning API: er har ersatts av interna Azure API: er som använder Azure AD för autentisering och auktorisering. Mer information om anropa Azure REST API: er finns i [komma igång med REST](/rest/api/azure/#create-the-request). Den reserverade instansen kostnad API ersätts av API-transaktioner.

Hämta reserverade köp transaktioner med API-transaktioner:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Rekommendationer API: er som ersätts

Reserverad instans köp rekommendationer API: er ger användning av virtuella datorer under de senaste 7, 30 och 60 dagarna. API: er ger även rekommendationer för köp av reservation. Dessa är:

- [Delade reserverade instanser rekommendation API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [Reserverad instans API för rekommendationer](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Alla förbrukning API: er har ersatts av interna Azure API: er som använder Azure AD för autentisering och auktorisering. Mer information om anropa Azure REST API: er finns i [komma igång med REST](/rest/api/azure/#create-the-request). Reservation-rekommendationer API: er som angavs tidigare har ersatts av den [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API.

Hämta reserverade rekommendationer med Reservation Rekommendations-API:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Reservation användning API: er ersättas

Du kan hämta reserverade användning i en registrering med API för användning av reserverad instans. Om det finns fler än en reserverad instans i en registreringsgrupp, får du också användningen av alla den reserverade instansen för köp med den här API: et.

Dessa är:

- [Information om användning av reserverad instans](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Användningsöversikt för reserverad instans](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Alla förbrukning API: er har ersatts av interna Azure API: er som använder Azure AD för autentisering och auktorisering. Mer information om anropa Azure REST API: er finns i [komma igång med REST](/rest/api/azure/#create-the-request). Reservation-rekommendationer API: er som angavs tidigare har ersatts av den [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) och [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API: er.

Hämta information om reservation med API: et för Reservation information:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Hämta reserverade sammanfattningar med API: et för Reservation sammanfattningar:

| Metod | Förfrågans URI |
| --- | --- |
| HÄMTA | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Flytta från Cloudyn till Cost Management

Organisationer som använder [Cloudyn](https://cloudyn.com) ska börja använda [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) för cost management behov. Kostnadshantering är tillgängligt i Azure-portalen med inga onboarding och en svarstid på åtta timmar. Mer information finns i den [dokumentation om Cost Management](index.yml).

Med Azure Cost Management kan du:

- Visa kostnader över tid mot en fördefinierad budget. Analysera dagligen kostnaden mönster för att identifiera och stoppa utgifter avvikelser. Dela upp kostnader efter taggar, resursgrupp, tjänst och plats.
- Skapa budgetar för att ange begränsningar för användning och kostnader och få ett meddelande när viktiga tröskelvärdena uppnås. Ställ in automatisering med åtgärdsgrupper för att utlösa anpassade händelser och tillämpa gränser på dina villkor.
- Optimera kostnader och användning med rekommendationer från Azure Advisor. Identifiera köp optimeringar med reservationer, lågsäsong när virtuella datorer och ta bort oanvända resurser för att hålla sig inom budget.
- Schemalägga en export av kostnader och användning data att publicera en CSV-fil till ditt storage-konto varje dag. Automatisera integrering med externa system för att hålla faktureringsdata synkroniserade och uppdaterade.

## <a name="power-bi-integration"></a>Power BI-integrering

Om du använder Power BI för rapportering av kostnaden, måste du övergången till följande:

- Microsoft Azure Consumption Insights Power BI-app
- Azure Consumption Insights skrivbord anslutningsapp


Anslutningen rekommenderas för organisationer som mest flexibilitet. Power BI-appen är också tillgängligt för snabba installation.

- Installera den [Microsoft Azure Consumption Insights Power BI-appen](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Anslut till Azure Consumption Insights-anslutningsapp](/power-bi/desktop-connect-azure-consumption-insights)

Äldre Consumption Insights-Innehållspaketet och anslutningen du arbetat på registreringsnivå. Det krävs minst läsbehörighet. Nya förbrukning Insights Power BI-appen och den nya Azure Consumption Insights-anslutningsappen är tillgängliga för fakturering profilanvändare. Team som behöver ytterligare alternativ för att granska kostnaderna eller för att visa kostnader över fakturering profiler ska använda i [analys av kostnader](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) Azure-portalen.

## <a name="next-steps"></a>Nästa steg

- Läs den [kostnadshantering dokumentation](index.yml) och lär dig att övervaka och kontrollera Azure utgifter. Eller, om du vill optimera Resursanvändning med Cost Management.
