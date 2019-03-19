---
title: Automatiseringsscenarier för Azure-fakturering och kostnadshantering | Microsoft Docs
description: 'Lär dig hur vanliga fakturering och kostnadshantering scenarier är mappade till olika API: er.'
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/13/2018
ms.author: erikre
ms.openlocfilehash: 8b42361e7b94bf017dde4eaec882f9fed4d42ed6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57862003"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automatiseringsscenarier för hantering av fakturerings- och kostnad

Den här artikeln innehåller vanliga scenarier för Azure-fakturering och kostnadshantering. Det motsvarar API: er som du kan använda dessa scenarier. Under varje scenario för API-mappning hittar du en sammanfattning av de API: er och funktioner som de erbjuder.

## <a name="common-scenarios"></a>Vanliga scenarier

Du kan använda fakturering och kostnadshantering API: er i flera scenarier för att besvara frågor som rör relaterade kostnader och användningsrelaterade. Här är en översikt över vanliga scenarier:

- **Fakturera avstämningen**: Microsoft debiterar mig rätt belopp?  Vad är min faktura och kan jag beräkna det mig?

- **Cross-avgifter**: Nu när jag vet hur mycket jag debiteras, vilka i organisationen behöver betala?

- **Kostnad optimering**: Jag vet hur mycket jag har debiterats. Hur kan jag få ut mer av de pengar som jag tillbringar på Azure?

- **Kostnadsuppföljning**: Jag vill se hur mycket jag är utgifter och använda Azure över tid. Vilka är trenderna? Hur kan jag göra bättre?

- **Azure-utgifter under månaden**: Hur mycket ödslar min nuvarande månad till dags datum? Behöver jag göra några ändringar i Mina utgifter och/eller användning av Azure? När under månaden ska jag använda Azure mest?

- **Aviseringar**: Hur ställer jag in Resursbaserad förbrukning eller monetära-baserade aviseringar?

## <a name="scenario-to-api-mapping"></a>Scenario-API-mappning

|         API        | Avstämningen av faktura    | Cross-avgifter    | Kostnad optimering    | Kostnadsuppföljning    | Midmonth utgifter    | Aviseringar    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgetar                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace-debiteringar                |             X             |         X        |           X          |         X        |          X         |     X     |
| Prisdokument                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservationsrekommendationer |                           |                  |           X          |                  |                    |           |
| Information om reservation         |                           |                  |           X          |         X        |                    |           |
| Reservationssammanfattningar       |                           |                  |           X          |         X        |                    |           |
| Användningsinformation               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturering punkter             |             X             |         X        |           X          |         X        |                    |           |
| Fakturor                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Oklassificerade användning               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenario för API-mappning innehåller inte Enterprise Consumption-API: er. Om möjligt, kan du använda API: er för allmän användning för nya utvecklingsscenarier.

## <a name="api-summaries"></a>API-sammanfattningar

### <a name="consumption"></a>Förbrukning
Web Direct och Enterprise-kunder kan använda alla de följande API: er, utom där annat anges:

-   [Budgetar API](https://docs.microsoft.com/rest/api/consumption/budgets) (*Enterprise-kunder*): Skapa budgetar för kostnader och användning för resurser, resursgrupper eller faktureringsmätare. När du har skapat budgetar, konfigurerar du varningar som meddelar dig när du har överskridit definierade budgetgränser. Du kan också konfigurera åtgärder som ska utföras när du har nått budgetbelopp.

-   [Marketplace-avgifter API](https://docs.microsoft.com/rest/api/consumption/marketplaces): Hämta kostnad-och användningsdata på alla Azure Marketplace-resurser (Azure-partnererbjudanden). Du kan använda dessa data för att lägga till upp kostnader över alla resurser i Marketplace eller undersöka kostnader/användning på specifika resurser.

-   [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Enterprise-kunder*): Få anpassade priser för alla mätare. Företag kan använda dessa data i kombination med användningsinformation och information om marketplace användning för att beräkna kostnader med hjälp av användnings-och marketplace. 

-   [API för rekommendationer reservation](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Få rekommendationer för att köpa reserverade VM-instanser. Rekommendationer för att analysera förväntade kostnadsbesparingar och inköp. Mer information finns i [API: er för Azure reservation automation](billing-reservation-apis.md).

-   [Reservation beskriver API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Se information om tidigare köpt VM-reservationer, till exempel hur mycket förbrukning är reserverat jämfört med hur mycket används. Du kan se data i detalj på per-VM-nivå. Mer information finns i [API: er för Azure reservation automation](billing-reservation-apis.md).

-   [Reservation sammanfattningar API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Se samlad information på VM-reservationer som din organisation har köpt, t.ex. hur mycket förbrukning är reserverat jämfört med hur mycket används i samlingen. Mer information finns i [API: er för Azure reservation automation](billing-reservation-apis.md).

-   [Användning beskriver API](https://docs.microsoft.com/rest/api/consumption/usagedetails): Få information om kostnad och användning på alla Azure-resurser från Microsoft. Informationen är i form av den användningsposter för information, som genereras för närvarande en gång per mätnings per dag. Du kan använda informationen för att lägga till upp kostnader över alla resurser eller undersöka kostnader/användning på specifika resurser.

-   [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx): Få mätaren betalning om du är direktkund Web. Du kan sedan använda den returnerade informationen med din resurs användningsinformation för att beräkna den förväntade fakturan manuellt. 

-   [Oklassificerat användnings-API](https://msdn.microsoft.com/library/azure/mt219003.aspx): Få information om rådata användningen innan Azure gör eventuella mätning/debitera.

### <a name="billing"></a>Fakturering
-   [Fakturering perioder API](https://docs.microsoft.com/rest/api/billing/billingperiods): Fastställa en faktureringsperiod att analysera, tillsammans med fakturan ID: N för den perioden. Du kan använda faktura ID: N med API för fakturor.

-   [API-fakturor](https://docs.microsoft.com/rest/api/billing/invoices): Hämta nedladdnings-URL för en faktura för en faktureringsperiod i PDF-format.

### <a name="enterprise-consumption"></a>Enterprise-förbrukning
Följande API: er är endast för företag:

-   [Balansera sammanfattning API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Få en månatlig sammanfattning av information om saldo, nya inköp, Azure Marketplace-tjänstavgifter, justeringar och överförbrukningskostnader. Du kan hämta den här informationen för den aktuella faktureringsperioden eller valfri punkt i förflutna. Företag kan använda dessa data ska jämföras med manuellt beräknade sammanfattning avgifter. Den här API: et tillhandahåller inte resursspecifik information eller en aggregerad vy över kostnaderna.

-   [Användning beskriver API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Få information om Azure-användning (av Microsoft-erbjudanden) för den aktuella månaden, en viss faktureringsperiod eller en anpassad period. Företag kan använda dessa data för att beräkna manuellt fakturor baserat på priset och förbrukning. Företag kan också använda avdelning/organisationsinformationen attributet kostnader mellan olika organisationer. Data innehåller en resurs-specifika vy över förbrukningskostnad /.

-   [Marketplace-Store kostnad API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Få information om Azure-användning (partnererbjudanden) för den aktuella månaden, en viss faktureringsperiod eller en anpassad period. Företag kan använda dessa data för att beräkna manuellt fakturor baserat på priset och förbrukning. Företag kan också använda avdelning/organisationsinformationen attributet kostnader mellan olika organisationer. Den här API: et tillhandahåller en resursspecifika vy över förbrukningskostnad /.

-   [Pris blad API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Hämta det gälla priset för varje mätare för viss registreringspost och faktureringsperioden. Du kan använda det här priset information i kombination med användningsinformation och information om marketplace användning för att beräkna manuellt förväntade fakturan.

-   [Fakturering perioder API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Hämta en lista över faktureringsperioder. API: et ger dig också en egenskap som pekar på API-väg för fyra uppsättningar med Enterprise-API-data som hör till faktureringsperioden: BalanceSummary, UsageDetails, Marketplace-avgifter och Prisdokument.

-   [Reserverad instans API för rekommendationer](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Titta på 7 dagar, 30 dagar eller 60 dagars användning av virtuella datorer och få rekommendationer för enstaka eller delade köpet. Du kan använda detta API för att analysera förväntade kostnadsbesparingar och rekommenderade inköp. Mer information finns i [API: er för Azure reservation automation](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Vad är skillnaden mellan Enterprise Reporting API: er och API: er för förbrukning? När ska jag använda varje?
Dessa API: er har en liknande uppsättning funktioner och kan svara på samma rad olika frågor i området för fakturerings- och kostnad. Men de är riktade mot olika målgrupper: 

- Enterprise Reporting API: er är tillgängliga för kunder som har registrerat ett Enterprise-avtal med Microsoft som ger åtkomst till den förhandlade monetära åtaganden och anpassad prissättning. API: erna kräver en nyckel som du kan hämta från den [Enterprise Portal](https://ea.azure.com). En beskrivning av dessa API: er finns i [översikt med Reporting API: er för företagskunder](billing-enterprise-api.md).

- Förbrukning API: er är tillgängliga för alla kunder, med några undantag. Mer information finns i [översikt över Azure-förbrukning API](billing-consumption-api-overview.md) och [Azure Consumption-API-referens](https://docs.microsoft.com/rest/api/consumption/). Vi rekommenderar den tillhandahållna API: er som lösning för de senaste utvecklingsscenarier. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Vad är skillnaden mellan API: et för användning information och användning API?
Dessa API: er ger helt olika data:

- Den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails) ger Azure information om användning och kostnader per mätnings-instans. Dessa data har redan passerat kostnaden för Avläsning av system i Azure och hade kostnad som används för den, tillsammans med andra möjliga ändringar:

   - Ändringar för användning av förbetalda monetära åtaganden
   - Ändringar för användning avvikelser som identifierats av Azure

- Den [användning API](https://msdn.microsoft.com/library/Mt219003.aspx) innehåller raw användningen av Azure information innan den går igenom kostnaden för Avläsning av system i Azure. Dessa data kanske inte har alla samband med användning eller laddning belopp som visas när Azure debiterar Avläsning av systemet.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Vad är skillnaden mellan API: et faktura och användning information om API: et?
Dessa API: er ger en annan vy av samma data:

- Den [faktura API](https://docs.microsoft.com/rest/api/billing/invoices) för Web Direct kunder. Den innehåller månatliga fakturan utifrån sammanställd avgifterna för varje Mätartyp av. 

- Den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails) ger en detaljerad vy av förbrukningskostnad /-poster för varje dag. Både företagscertifikatutfärdare och Web Direct-kunder kan använda den.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Vad är skillnaden mellan Price Sheet API och RateCard-API: et?
Dessa API: er tillhandahåller en liknande uppsättning data men har olika målgrupper:

- Den [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) innehåller den anpassade priser som var förhandlas för en företagskund.

- Den [: T för RateCard](https://msdn.microsoft.com/library/mt219005.aspx) innehåller den offentliga pris som gäller för Web Direct-kunder.

## <a name="next-steps"></a>Nästa steg

- Information om hur du programmässigt få insikt i din Azure-användning med Azure API: er finns i [översikt över API för Azure-förbrukning](billing-consumption-api-overview.md) och [översikt över Azure Billing API](billing-usage-rate-card-overview.md).

- Jämförelse mellan din faktura med detaljerade dagliga användningsfil och kostnadshanteringsrapporter i Azure portal finns [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md).

- Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
