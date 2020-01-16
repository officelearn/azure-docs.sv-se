---
title: Automationsscenarier för fakturering och kostnadshantering i Azure | Microsoft Docs
description: Lär dig hur vanliga scenarier för fakturering och kostnadshantering mappas till olika API:er.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c9de7d5f7661e4083d3a2f5b53368616d0e6655a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992821"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automationsscenarier för fakturering och kostnadshantering

Den här artikeln innehåller vanliga scenarier för fakturering och kostnadshantering i Azure. Den mappar dessa scenarier till API:er som du kan använda. I varje scenario-till-API-mappning finns en sammanfattning av API:erna och de funktioner som de innehåller.

## <a name="common-scenarios"></a>Vanliga scenarier

Du kan använda API:erna för fakturering och kostnadshantering i flera scenarier för att besvara frågor kring kostnader och användning. Här är en översikt över vanliga scenarier:

- **Faktura avstämning**: debiterade Microsoft mig rätt belopp?  Hur mycket är min faktura på och kan jag beräkna beloppet själv?

- **Mellan debiteringar**: nu vet jag hur mycket jag debiteras, som i min organisation behöver betala?

- **Kostnads optimering**: Jag vet hur mycket jag har debiteras. Hur kan jag få ut mer av de pengar som jag spenderar på Azure?

- **Kostnads uppföljning**: Jag vill se hur mycket jag kostar och använder Azure över tid. Hur ser trenderna ut? Hur kan jag göra detta på bättre sätt?

- **Azure-utgifter under månaden**: hur mycket är min nuvarande månads utgifter hittills? Behöver jag göra några ändringar i mina utgifter och/eller användning av Azure? När under månaden använder jag Azure mest?

- **Aviseringar**: Hur kan jag konfigurera resursbaserade konsumtion eller penningbaserade aviseringar?

## <a name="scenario-to-api-mapping"></a>Scenario-till-API-mappning

|         API        | Fakturaavstämning    | Tvärdebiteringar    | Kostnadsoptimering    | Kostnadsspårning    | Utgifter i mitten av månaden    | Aviseringar    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgetar                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace-debiteringar                |             X             |         X        |           X          |         X        |          X         |     X     |
| Prisdokument                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservationsrekommendationer |                           |                  |           X          |                  |                    |           |
| Information om reservation         |                           |                  |           X          |         X        |                    |           |
| Reservationssammanfattningar       |                           |                  |           X          |         X        |                    |           |
| Användningsinformation               |             X             |         X        |           X          |         X        |          X         |     X     |
| Faktureringsperioder             |             X             |         X        |           X          |         X        |                    |           |
| Fakturor                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Oklassificerad användning               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenario-till-API-mappningen omfattar inte Enterprise-förbruknings-API:er. Använd om möjligt de allmänna förbruknings-API:erna för nya utvecklingsscenarier.

## <a name="api-summaries"></a>API-sammanfattningar

### <a name="consumption"></a>Förbrukning
Web Direct- och Enterprise-kunder kan använda alla följande API:er, förutom där något annat anges:

-   [Budget-API](https://docs.microsoft.com/rest/api/consumption/budgets) (*endast företags kunder*): skapa antingen kostnads-eller användnings budgetar för resurser, resurs grupper eller fakturerings mätare. När du har skapat budgetar kan du konfigurera aviseringar som meddelar dig när du har överskridit definierade budgettrösklar. Du kan även konfigurera åtgärder som ska ske när du har nått budgetbeloppen.

-   [API för Marketplace-debitering](https://docs.microsoft.com/rest/api/consumption/marketplaces): Hämta information om avgifter och användning på alla Azure Marketplace-resurser (Azure-partner erbjudanden). Du kan använda dessa data för att summera kostnaderna för alla Marketplace-resurser eller för att analysera kostnaderna/användningen för specifika resurser.

-   [Pris dokuments-API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*endast företags kunder*): hämta anpassad prissättning för alla mätare. Företag kan använda dessa data i kombination med användningsdata och information om Marketplace-användning för att beräkna kostnader med hjälp av användnings- och Marketplace-data.

-   [API för reservations rekommendationer](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): få rekommendationer för att köpa reserverade VM-instanser. Rekommendationer hjälper dig att analysera förväntade kostnadsbesparingar och inköpsbelopp. Mer information finns i [API:er för automatisk reservation i Azure](../reservations/reservation-apis.md).

-   [API för reservations information](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): se information om tidigare köpta VM-reservationer, till exempel hur mycket förbrukning som är reserverad jämfört med hur mycket som används. Du kan visa data på nivån för en enskild virtuell dator. Mer information finns i [API:er för automatisk reservation i Azure](../reservations/reservation-apis.md).

-   [API: er för reservations Sammanfattning](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): se samlad information om VM-reservationer som din organisation har köpt, till exempel hur mycket förbrukning som är reserverad jämfört med hur mycket som används i mängden. Mer information finns i [API:er för automatisk reservation i Azure](../reservations/reservation-apis.md).

-   [API för användnings information](https://docs.microsoft.com/rest/api/consumption/usagedetails): få information om avgifter och användning för alla Azure-resurser från Microsoft. Informationen visas i form av poster med användningsinformation och skickas för närvarande en gång per mätare och dag. Du kan använda informationen för att summera kostnaderna för alla resurser eller för att analysera kostnader/användning för specifika resurser.

-   [Ratecard-API](/previous-versions/azure/reference/mt219005(v=azure.100)): Hämta mätnings priser om du är en webb direkt kund. Du kan sedan använda den returnerade informationen med din information resursanvändning till att beräkna den förväntade fakturan manuellt.

-   [Oklassificerad användnings-API](/previous-versions/azure/reference/mt219003(v=azure.100)): Hämta information om RAW-användning innan Azure utför mätning/debitering.

### <a name="billing"></a>Fakturering
-   [API för fakturerings perioder](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): fastställer en fakturerings period att analysera, tillsammans med faktura-ID för den perioden. Du kan använda faktura-ID:n med API:et för fakturor.

-   [API för fakturor](/rest/api/billing/2019-10-01-preview/invoices): Hämta hämtnings-URL: en för en faktura för en fakturerings period i PDF-format.

### <a name="enterprise-consumption"></a>Enterprise-förbrukning
Följande API:er gäller endast för Enterprise:

-   [Sammanfattnings-API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): få en månatlig Sammanfattning av information om saldon, nya inköp, Azure Marketplace service-kostnader, justeringar och kostnader för överanvändning. Du kan hämta den här informationen för den aktuella faktureringsperioden eller för en tidigare period. Företag kan använda dessa data för att jämföra dem med manuellt beräknade sammanfattningskostnader. Det här API:et returnerar inte resursspecifik information eller en sammanställd vy över kostnaderna.

-   [API för användnings information](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Hämta information om Azure-användning (av Microsoft-erbjudanden) för den aktuella månaden, en viss fakturerings period eller en anpassad datum period. Företag kan använda dessa data för att manuellt beräkna fakturor baserat på pris och förbrukning. Företag kan även använda avdelnings-/organisationsinformation för att tillskriva olika organisationer kostnader. Dessa data ger en resursspecifik vy av användning/kostnad.

-   [API för Marketplace-lagrings avgift](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Hämta information om Azure-användning (av partner erbjudanden) för den aktuella månaden, en viss fakturerings period eller en anpassad datum period. Företag kan använda dessa data för att manuellt beräkna fakturor baserat på pris och förbrukning. Företag kan även använda avdelnings-/organisationsinformation för att tillskriva olika organisationer kostnader. Detta API ger en resursspecifik vy av användning/kostnad.

-   [Pris dokuments-API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Hämta tillämplig taxa för varje mätare för den aktuella registreringen och fakturerings perioden. Du kan använda den här prisinformationen i kombination med användningsinformation och information om Marketplace-användning för att beräkna den förväntade fakturan manuellt.

-   [API för fakturerings perioder](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Hämta en lista över fakturerings perioder. API: et ger dig också en egenskap som pekar på API-vägen för de fyra uppsättningarna med företags-API-data som hör till fakturerings perioden: BalanceSummary, UsageDetails, Marketplace-kostnader och pris dokument.

-   [Rekommendationer för reserverad instans i API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Titta efter sju dagar, 30 dagar eller 60 dagar av användning av virtuella datorer och få rekommendationer för en enda och delad inköps version. Du kan använda detta API för att analysera förväntade kostnadsbesparingar och rekommenderade inköpsbelopp. Mer information finns i [API:er för automatisk reservation i Azure](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Vad är skillnaden mellan API:erna för företagsrapportering och förbruknings-API:erna? När ska jag använda respektive API?
Dessa API:er har en liknande uppsättning funktioner och kan besvara samma breda uppsättning frågor vad gäller fakturering och kostnadshantering. Men de riktar sig till olika målgrupper:

- API:er för företagsrapportering är tillgängliga för kunder som har registrerat ett Enterprise-avtal med Microsoft som ger dem tillgång till förhandlade ekonomiska åtaganden och anpassad prissättning. API:erna kräver en nyckel som du kan hämta från [Enterprise-portalen](https://ea.azure.com). En beskrivning av dessa API:er finns i [översikten över rapporterings-API:er för Enterprise-kunder](enterprise-api.md).

- Förbruknings-API:er är tillgängliga för alla kunder, med några undantag. Mer information finns i [Översikt över API för Azure Consumption](consumption-api-overview.md) och [referensen för förbruknings-API i Azure](https://docs.microsoft.com/rest/api/consumption/). Vi rekommenderar de tillhandahållna API:erna som lösning för de senaste utvecklingsscenarierna.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Vad är skillnaden mellan API:et för användningsinformation och API:et för användning?
Dessa API:er erbjuder data som skiljer sig i grunden:

- [API:et för användningsinformation](https://docs.microsoft.com/rest/api/consumption/usagedetails) ger information om Azure-användning och kostnader per mätarinstans. Tillhandahållna data har redan passerat genom kostnadsmätningssystemet i Azure, och kostnader samt andra möjliga ändringar har tillämpats på dem:

   - Ändringar av kontot för användning av förbetalda ekonomiska åtaganden
   - Ändringar av kontot för användningsskillnader som identifieras av Azure

- [API:er för användning](/previous-versions/azure/reference/mt219003(v=azure.100)) ger rå Azure-användningsinformation innan den går genom systemet för kostnadsberäkning i Azure. Dessa data har kanske inte någon korrelation med den användning eller det avgiftsbelopp som visas efter Azure-systemet för avgiftsberäkning.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Vad är skillnaden mellan API:et för fakturering och API:et för användningsinformation?
Dessa API:er ger olika vyer av samma data:

- [Faktura-API:et](/rest/api/billing/2019-10-01-preview/invoices) är endast till för Web Direct-kunder. Det ger en månatlig sammanställning av din faktura baserat på de aggregerade avgifterna för varje mätartyp.

- [API:et för användningsinformation](https://docs.microsoft.com/rest/api/consumption/usagedetails) ger en detaljerad vy av användnings-/kostnadsposterna för varje dag. Det kan användas av både Enterprise-kunder och Web Direct-kunder.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Vad är skillnaden mellan API:et för prisdokument och API:et för RateCard?
Dessa API:er tillhandahåller liknande datauppsättningar men har olika målgrupper:

- [API:et för prisdokument](https://docs.microsoft.com/rest/api/consumption/pricesheet) ger den anpassade prissättning som förhandlades för en Enterprise-kund.

- [API:et för RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) ger den offentliga prissättning som gäller för Web Direct-kunder.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder Azure-API:er för att programmatiskt få inblick i din Azure-användning finns i [Översikt över förbruknings-API i Azure](consumption-api-overview.md) och [Översikt över fakturerings-API i Azure](usage-rate-card-overview.md).

- För att jämföra din faktura med filen med detaljerad daglig användning och kostnadshanteringsrapporterna i Azure-portalen kan du läsa [Förstå fakturan för Microsoft Azure](../understand/review-individual-bill.md).

- Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
