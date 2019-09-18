---
title: REST-API:er för Azure Enterprise
description: I den här artikeln beskrivs de REST-API:er du använder med din Azure Enterprise-registrering.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 4b56ef09525473920c5f5925485aae9c793ecc04
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900870"
---
# <a name="azure-enterprise-rest-apis"></a>REST-API:er för Azure Enterprise

I den här artikeln beskrivs de REST-API:er du använder med din Azure Enterprise-registrering. Dessutom går vi igenom hur du löser vanliga problem med REST-API:erna.

## <a name="consumption-and-usage-apis"></a>API:er för förbrukning och användning

Microsoft Enterprise Azure-kunder kan få information om användning och fakturering via REST-API:er. Rollägaren (företagsadministratören, avdelningsadministratören, kontoinnehavaren) måste ge åtkomst till API:et genom att generera en nyckel från Azure EA-portalen. Sedan kan alla som har registreringsnumret och nyckeln komma åt data via API:et.

### <a name="available-apis"></a>Tillgängliga API:er

**Saldon och sammanfattningar** – [API:et för saldon och sammanfattningar](billing-enterprise-api-balance-summary.md) returnerar en månatlig sammanfattning av information om saldon, nya inköp, kostnader för Azure Marketplace-tjänster, justeringar och kostnader för överförbrukning. Mer information finns i [Rapport-API:er för Enterprise-kunder – saldon och sammanfattningar](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary).

**Användningsinformation** – [API:et för användningsinformation](billing-enterprise-api-usage-detail.md) returnerar en daglig uppdelning av förbrukade kvantiteter och beräknade avgifter per registrering. Resultatet innehåller också information om instanser, mätare och avdelningar. Du kan köra frågor mot API:et per faktureringsperiod eller per start- och slutdatum. Du kan läsa mer i [Rapport-API:er för Enterprise-kunder – användningsinformation](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

**Kostnader för Marketplace Store** – [API:et för kostnader för Marketplace Store](billing-enterprise-api-marketplace-storecharge.md) returnerar en uppdelning av de användningsbaserade kostnaderna för Marketplace per dag under den angivna faktureringsperioden eller per start- och slutdatum. Du kan läsa mer i [Rapport-API:er för Enterprise-kunder – kostnader för Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge).

**Prisdokument** – [API:et för prisdokument](/billing-enterprise-api-pricesheet.md) returnerar relevant pris för respektive mätare för en registrering och faktureringsperiod. Du kan läsa mer i [Rapport-API:er för Enterprise-kunder – prisdokument](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet).

**Faktureringsperioder** – [API:et för faktureringsperioder](billing-enterprise-api-billing-periods.md) returnerar en lista med faktureringsperioder som har förbrukningsdata för en registrering i omvänd kronologisk ordning. Varje period innehåller en egenskap som pekar på API-vägen för de fyra datauppsättningarna BalanceSummary, UsageDetails, MarketplaceCharges och PriceSheet. Du kan läsa mer i [Rapport-API:er för Enterprise-kunder – faktureringsperioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

### <a name="enable-api-data-access"></a>Aktivera dataåtkomst via API:er

Rollägare kan göra så här i Azure EA-portalen: Navigera till **Rapporter** > **Ladda ned användning** > **API-åtkomstnyckel**. Sedan kan de:

- Generera primära och sekundära åtkomstnycklar.
- Inaktivera åtkomstnycklar.
- Visa start- och slutdatum för åtkomstnycklar.

### <a name="generate-or-retrieve-the-api-key"></a>Generera eller hämta API-nyckeln

1. Logga in som företagsadministratör.
2. Klicka på **Rapporter** i det vänstra navigeringsfönstret och klicka på fliken **Ladda ned användning**.
3. Klicka på **API-åtkomstnyckel**.
4. Under **Åtkomstnycklar för registrering** väljer du symbolen för att generera antingen en primär eller en sekundär nyckel.
5. Välj **Visa nyckel** för att visa hela den genererade API-åtkomstnyckeln.
6. Välj **Kopiera** för att hämta API-åtkomstnyckeln så att du kan använda den direkt.

![Exempel på sidan API-åtkomstnyckel](./media/billing-ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Gör så här om du vill ge API-åtkomstnycklar till personer i registreringen som inte är företagsadministratörer:

1. Klicka på **Hantera** i det vänstra navigeringsfönstret.
2. Klicka på pennsymbolen bredvid **Visa avgifter för DA** (avdelningsadministratör).
3. Välj **Aktivera** och klicka sedan på **Spara**.
4. Klicka på pennsymbolen bredvid **Visa avgifter för AO** (kontoägare).
5. Välj **Aktivera** och klicka sedan på **Spara**.

![Exempel som visar att avdelningsadministratörer och kontoägare kan visa avgifter](./media/billing-ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) De här stegen ger åtkomst till API-nyckeln till personer som har åtkomst till information om kostnader och priser i användningsrapporter.

### <a name="pass-keys-in-the-api"></a>Skicka nycklar i API:et

Skicka API-nyckeln i varje anrop för autentisering och auktorisering. Skicka följande egenskap till HTTP-huvuden:

| Nyckel i huvud | Värde |
| --- | --- |
| Auktorisering | Ange värdet på det här formatet: **bearer {API\_KEY}**
Exempel: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Det finns en Swagger-slutpunkt vid [Enterprise Reporting v3-API:erna](https://consumption.azure.com/swagger/ui/index)för följande API:er. Swagger hjälper dig att inspektera API:et. Använd Swagger till att generera klient-SDK:er med [AutoRest](https://github.com/Azure/AutoRest) eller [Swagger CodeGen](http://swagger.io/swagger-codegen/). Du kommer åt data som är tillgängliga efter den 1 maj 2014 via API:et.

### <a name="api-response-codes"></a>API-svarskoder

När du använder ett API returneras statuskoder. De beskrivs i följande tabell.

| Returnerad statuskod | Meddelande | Beskrivning |
| --- | --- | --- |
| 200 | OK | Inget fel |
| 401 | Behörighet saknas | Det gick inte att hitta API-nyckeln – ogiltig, har gått ut eller liknande |
| 404 | Inte tillgänglig | Det gick inte att hitta rapportslutpunkten |
| 400 | Felaktig förfrågan | Ogiltiga parametrar – datumintervall, EA-nummer eller liknande |
| 500 | Serverfel | Ett oväntat fel inträffade när förfrågan bearbetades |

### <a name="usage-and-billing-data-update-frequency"></a>Uppdateringsfrekvens för användnings- och faktureringsdata

Filerna med användnings- och faktureringsdata uppdateras var 24:e timme under den aktuella faktureringsmånaden. Det kan dock uppstå fördröjningar på upp till tre dagar. Om användningen till exempel sker på en måndag kanske inte data visas i filen förrän på torsdag.

### <a name="test-enrollment-for-development"></a>Testregistrering för utveckling

Om du är en partner eller utvecklare utan någon Azure Enterprise-registrering och du vill ha åtkomst till API:et kan du använda testregistreringen. Registreringsnamnet är _EnrollmentNumber 100_. Sedan kan du [använda registreringens åtkomstnyckel](https://automaticbillingspec.blob.core.windows.net/spec/TestEnrollmentUsageApiKey.txt) till att anropa API:et och se exempeldata.

### <a name="azure-service-catalog"></a>Katalogen med Azure-tjänster

Alla Azure-tjänster publiceras i en katalog i CSV-format på en Azure Storage-blogg. Den är användbar om du behöver skapa en katalog med utvalda Azure-tjänster för ditt system. Den aktuella katalogen finns på [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv).

### <a name="csv-data-file-details"></a>Informationen i CSV-datafilen

Följande information beskriver egenskaperna för API-rapporter.

#### <a name="usage-summary"></a>Användningssammanfattning

JSON-formatet genereras från CSV-rapporten. Formatet är därför detsamma som formatet i CSV-sammanfattningen. Kolumnnamnet är sammansatt, så du bör deserialisera till en datatabell när du använder JSON-sammanfattningsdata.

| CSV-kolumnnamn | Json-kolumnnamn | Ny Json-kolumn | Kommentar |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Kontonamn | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Prenumerationens namn | SubscriptionName | SubscriptionName |   |
| Date | Date | Date | Visar datumet när tjänstkatalogsrapporten kördes. Formatet är en datumsträng utan tidstämpel. |
| Month | Month | Month |   |
| Day | Day | Day |   |
| Year | Year | Year |   |
| Produkt | BillableItemName | Produkt |   |
| Mätar-ID | ResourceGUID | MeterId |   |
| Mätarkategori | Tjänst | MeterCategory | Användbar när du ska hitta tjänster. Relevant för tjänster som har flera tjänsttyper. Ett exempel är virtuella datorer. |
| Mätarens underkategori | ServiceType | MeterSubCategory | Ger en andra detaljnivå för en tjänst. Till exempel A1 VM (inte Windows).  |
| Mätarregion | ServiceRegion | MeterRegion | Den tredje detaljnivån som krävs för en tjänst. Användbart när du ska hitta regionkontexten för ett ResourceGUID. |
| Mätarnamn | ServiceResource | MeterName | Namnet på tjänsten. |
| Förbrukat antal | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Resursplats | ServiceSubRegion | ResourceLocation |   |
| Förbrukad tjänst | ServiceInfo | ConsumedService |   |
| Instans-ID | Komponent | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Taggar | Taggar | Taggar |   |
| Id för Store-tjänst   | OrderNumber | StoreServiceIdentifier   |   |
| Avdelningsnamn | DepartmentName | DepartmentName |   |
| Kostnadsställe | CostCenter | CostCenter |   |
| Måttenhet | UnitOfMeasure | UnitOfMeasure | Exempelvärden: Timmar, GB, händelser, push-meddelanden, enhet, enhetstimmar, MB, dagliga enheter |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Azure Marketplace-rapport

| CSV-kolumnnamn | Json-kolumnnamn | Ny Json-kolumn |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Kontonamn | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Prenumerationens namn | SubscriptionName |  SubscriptionName |
| Date | BillingCycle |  Datum (endast datum sträng, ingen tidsstämpel)
| Month | Month |  Month |
| Day | Day |  Day |
| Year | Year |  Year |
| Mätar-ID | MeterResourceId |  MeterId |
| Utgivarens namn | PublisherFriendlyName |  PublisherName |
| Erbjudandets namn | OfferFriendlyName |  OfferName |
| Plannamn | PlanFriendlyName |  PlanName |
| Förbrukat antal | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Måttenhet | UnitOfMeasure | UnitOfMeasure |
| Instans-ID | InstanceId | InstanceId |
| Ytterligare info | AdditionalInfo | AdditionalInfo |
| Taggar | Taggar | Taggar |
| Ordernummer | OrderNumber | OrderNumber |
| Avdelningsnamn | DepartmentNames | DepartmentName |
| Kostnadsställe | CostCenters |  CostCenter |
| Resursgrupp | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Prisdokument

| CSV-kolumnnamn | Json-kolumnnamn | Kommentar |
| --- | --- | --- |
| Tjänst | Tjänst |  Ingen ändring av priset |
| Måttenhet | UnitOfMeasure |   |
| Överförbrukningens delnummer | ConsumptionPartNumber |   |
| Överförbrukningens enhetspris | ConsumptionPrice |   |
| Valutakod | CurrencyCode |     |

### <a name="common-api-issues"></a>Vanliga API-problem

När du använder REST-API:er för Azure Enterprise kan du stöta på något av följande vanliga problem.

Du kanske försöker använda en API-nyckel som inte har rätt typ av behörighet. API-nycklar genereras av:

- Företagsadministratörer
- Avdelningsadministratörer (DA)
- Kontoägare (AO)

En nyckel som genereras av EA-administratören ger åtkomst till all information om registreringen. En EA-administratör med skrivskyddad åtkomst kan inte skapa API-nycklar.

En nyckel som genereras av en DA eller AO ger inte åtkomst till saldon, avgifter eller prisdokument.

En API-nyckel upphör att gälla efter sex månader. Om den upphör att gälla måste du generera en ny.

Om du får ett tidsgränsfel kan du lösa det genom att öka tröskelvärdet för tidsgränsen.

Du kan få ett 401-fel (behörighet saknas). Det här felet orsakas normalt av att nyckeln har upphört att gälla. Om nyckeln har upphört att gälla kan du generera en ny.

Du kan få 400- och 404-fel (inte tillgänglig) från ett API-anrop om inga data är tillgängliga för det valda datumintervallet. Det här felet kan till exempel inträffa på grund av att en registreringsöverföring har initierats nyligen. Data från ett visst datum och framåt ligger nu i en ny registrering. I annat fall kan felet inträffa om du använder ett nytt registreringsnummer till att hämta information som ligger i en gammal registrering.

## <a name="next-steps"></a>Nästa steg

- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](billing-ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](billing-ea-portal-troubleshoot.md).
