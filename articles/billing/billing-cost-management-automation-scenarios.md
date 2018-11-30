---
title: Azure-fakturering och kostnaden för automation hanteringsscenarier | Microsoft Docs
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
ms.openlocfilehash: 09ecd46837a12f5f1a8278b1644dc099701bcd00
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584731"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Fakturerings- och automation hanteringsscenarier

Vanliga scenarier för området för fakturerings- och identifieras nedan och mappas till olika API: er som kan användas i dessa scenarier. En sammanfattning av alla API: erna och de funktioner som de erbjuder finns under scenario för API-mappning. 

## <a name="common-scenarios"></a>Vanliga scenarier

Du kan använda fakturering och kostnadshantering API: er i en mängd olika scenarier för att besvara kostnader och användning av relaterade frågor.  En översikt över vanliga scenarier finns nedan.

- **Fakturera avstämningen** -gjorde Microsoft debiterar mig rätt belopp?  Vad är min faktura och kan jag beräkna det mig?

- **Mellan avgifter** – nu när jag vet hur mycket jag debiteras, vilka i organisationen behöver betala?

- **Kostnad optimering** – jag vet hur mycket har jag debiterats, men hur kan jag få ut mer av de pengar som jag tillbringar på Azure?

- **Kostnadsuppföljning** – jag vill se hur mycket jag är utgifter och använda Azure över tid. Vilka är trenderna? Hur kan jag göra bättre?

- **Azure-kostnader under månaden** – hur mycket är min aktuella månaden är spendera hittills? Behöver jag göra eventuella justeringar av i Mina utgifter och/eller användning av Azure? När under månaden ska jag använda Azure mest?

- **Ställa in aviseringar** – jag vill konfigurera resursbaserade förbrukning eller monetära-baserade aviseringar.

## <a name="scenario-to-api-mappings"></a>Scenario för att API-mappningarna

|         API-Scenario        | Avstämningen av faktura    | Mellan avgifter    | Kostnad optimering    | Kostnadsuppföljning    | Ägna åt att mitt i månaden    | Aviseringar    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgetar                     |                           |                  |           X          |                  |                    |     X     |
| Marknadsplatser                |             X             |         X        |           X          |         X        |          X         |     X     |
| Prisdokument                 |             X             |         X        |           X          |         X        |          X         |           |
| Rekommendationer för reservation |                           |                  |           X          |                  |                    |           |
| Information om reservation         |                           |                  |           X          |         X        |                    |           |
| Reservation sammanfattningar       |                           |                  |           X          |         X        |                    |           |
| Användningsinformation               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturering punkter             |             X             |         X        |           X          |         X        |                    |           |
| Fakturor                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Oklassificerade användning               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenariot för att API-mappningarna nedan inkluderar inte Enterprise Consumption-API: er. Om möjligt Använd allmän användning API: erna för att åtgärda net utvecklingsscenarier som framöver.

## <a name="api-summaries"></a>API-sammanfattningar

### <a name="consumption"></a>Förbrukning
(*Web Direct och Enterprise-kunder för alla API: er Förutom dessa kallas ut nedan*)

-   **Budgetar** (*Enterprise-kunder*): Använd den [budgetar API](https://docs.microsoft.com/rest/api/consumption/budgets) Skapa budgetar för kostnader och användning för resurser, resursgrupper eller faktureringsmätare.  När du har skapat budgetar kan aviseringar konfigureras för att meddela när användardefinierade budget tröskelvärden överskrids. Åtgärder kan också konfigureras för att inträffa när budgetbelopp uppnås.
-   **Marknadsplatser**: Använd den [Marketplace-avgifter API](https://docs.microsoft.com/rest/api/consumption/marketplaces) att hämta kostnad-och användningsdata på alla resurser i Marketplace (Azure erbjudanden med 3 part). Dessa data kan användas för att lägga till upp kostnader över alla resurser i Marketplace eller undersöka kostnader/användning på specifika resurser.
-   **Prisdokument** (*Enterprise-kunder*): Enterprise-kunder kan använda den [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) att hämta sina anpassad prissättning för alla mätare. Företag kan använda dessa data i kombination med information och marknadsplatser användning användningsinformation för kostnadsberäkningar med användnings-och marketplace. 
-   **Rekommendationer för reservation**: Använd den [Reservation Rekommendations-API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) att hämta rekommendationer för att köpa reserverade VM-instanser. Rekommendationer är avsedda att kunden kan du analysera förväntade kostnadsbesparingar och köpa mängder som anges.
-   **Information om reservation**: Använd den [Reservation information API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) att se information om tidigare köpta VM-reservationer, till exempel hur mycket förbrukningen har reserverats jämfört med hur mycket faktiskt används. Du kan se data i detalj på per-VM-nivå.
-   **Reservation sammanfattningar**: Använd den [Reservation sammanfattningar API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) att se sammanställd information om tidigare köpta VM-reservationer, till exempel hur mycket förbrukningen har reserverats jämfört med hur mycket faktiskt används i samlingen. 
-   **Användningsinformation**: Använd den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails) att hämta kostnad och användning på alla Azure 1 part resurser. Informationen är i form av den användningsposter för information, som genereras för närvarande en gång per mätnings per dag. Informationen kan användas för att lägga till upp kostnader över alla resurser eller undersöka kostnader/användning på specifika resurser.
-   **RateCard**: Web Direct-kunder kan använda den [: T för RateCard](https://msdn.microsoft.com/library/azure/mt219005.aspx) till sina kostnader för mätaren. De kan sedan använda den returnerade informationen med deras användning Resursinformation vill beräkna manuellt förväntade faktura. 
-   **Oklassificerat användning**: du kan använda den [oklassificerade användning API](https://msdn.microsoft.com/library/azure/mt219003.aspx) att hämta rådata användningsinformation före eventuella mätning/debitera från Azure.

### <a name="billing"></a>Fakturering
-   **Fakturering perioder**: Använd den [Billing perioder API](https://docs.microsoft.com/rest/api/billing/billingperiods) att fastställa en faktureringsperiod att analysera, tillsammans med fakturan ID-numret för den perioden. Faktura-ID: n kan användas med faktura API: et nedan. 
-   **Fakturor**: Använd den [fakturor API](https://docs.microsoft.com/rest/api/billing/invoices) att hämta nedladdnings-URL för en faktura för en viss faktureringsperiod i PDF-format.

### <a name="enterprise-consumption"></a>Enterprise-förbrukning
*(Alla API: er Enterprise endast)*

-   **Saldosammanfattning**: Använd den [saldo sammanfattning API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) att få en månatlig sammanfattning av information om saldo, nya inköp, Azure Marketplace-tjänstavgifter, justeringar och överförbrukningskostnader. Du kan hämta den här informationen för den aktuella faktureringsperioden eller valfri punkt i förflutna. Företag kan använda dessa data för att utföra en jämförelse med manuellt beräknade sammanfattning avgifter. Den här API: et tillhandahåller inte resursspecifik information eller en aggregerad vy över kostnaderna.
-   **Användningsinformation**: Använd den [användning information API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) att hämta 1 part Azure-användning detaljerad information om den aktuella månaden, en viss faktureringsperiod eller en anpassad period. Företag kan använda dessa data för att beräkna manuellt faktura baserat på priset och förbrukning och kan också använda avdelning/organisationsinformationen finns attributet kostnader mellan olika organisationer. Data innehåller en resurs-specifika vy över förbrukningskostnad /.
-   **Marketplace store kostnad**: Använd den [Marketplace Store kostnad API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) att hämta 3 part Azure-användning detaljerad information om den aktuella månaden, en viss faktureringsperiod eller en anpassad period. Företag kan använda dessa data för att beräkna manuellt faktura baserat på priset och förbrukning och kan också använda avdelning/organisationsinformationen finns attributet kostnader mellan olika organisationer. Marketplace store avgiften API ger en resursspecifika överblick över förbrukningskostnad /.
-   **Prisdokument**: den [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) ger det gälla priset för varje mätare för den angivna registrering och faktureringsperiod. Information om det här kan användas tillsammans med information och marknadsplatser användning användningsinformation för att beräkna manuellt förväntade faktura.
-   **Fakturering perioder**: Använd den [Billing perioder API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) att hämta en lista över faktureringsperioder tillsammans med en egenskap som pekar på API-väg för fyra uppsättningar med Enterprise-API-data som hör till den aktuella faktureringsperioden - BalanceSummary, UsageDetails Marketplace-avgifter och Prisdokument.
-   **Azure reservation rekommendationer**: den [API för rekommendationer för reserverad instans](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) tittar på kundens 7 dagar, 30 dagar eller 60 dagars användning av virtuella datorer och rekommendationer för enstaka eller delade köpet. Den reserverade instansen för API: et kan kunder att analysera förväntat kostnadsbesparingar och rekommenderade inköp.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-is-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Vad är skillnaden mellan Enterprise Reporting API: er och API: er för förbrukning? När ska jag använda varje?
Dessa API: er har en liknande uppsättning funktioner och kan svara på samma rad olika frågor i området för fakturerings- och kostnad. Men varje API riktar sig till olika målgrupper: 

- **Enterprise Reporting API: er**: dessa API: er är tillgängliga för kunder som har registrerat ett Enterprise-avtal med Microsoft som ger åtkomst till den förhandlade monetära åtaganden och anpassad prissättning. API: erna kräver en nyckel som ska användas som kan köpas genom den [Enterprise Portal](https://ea.azure.com). En beskrivning av dessa API: er finns i [översikt med Reporting API: er för företagskunder](billing-enterprise-api.md).

- **Förbrukning API: er**: dessa API: er är tillgängliga för alla kunder, med några undantag. Mer information finns i [översikt över Azure-förbrukning API](billing-consumption-api-overview.md) och [Azure Consumption-API-referens](https://docs.microsoft.com/rest/api/consumption/). De tillhandahållna API: er är den rekommenderade lösningen för senaste utvecklingsscenarier. 

### <a name="what-is-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Vad är skillnaden mellan API: et för användning information och användning API?
Dessa API: er ger helt olika data:

- **Användningsinformation**: den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails) ger Azure information om användning och kostnader per mätnings-instans. Data som redan passerat Azures kostnaden för Avläsning av system och hade kostnad som används för den tillsammans med andra möjliga ändringar:

    - Ändringar för användning av förbetalda monetära åtaganden
    - Ändringar för användning avvikelser som identifierats av Azure

- **Användning**: den [användning API](https://msdn.microsoft.com/library/Mt219003.aspx) ger raw Azure användningsinformation innan den går igenom Azure kostnader Avläsning av systemet. Dessa data kanske inte har alla samband med användning och/eller avgift belopp som visas när Azure debiterar Avläsning av systemet.

### <a name="what-is-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Vad är skillnaden mellan API: et faktura och användning information om API: et?
Dessa API: er ger en annan vy av samma data. Den [faktura API](https://docs.microsoft.com/rest/api/billing/invoices) för Web Direct kunder och ger en månatlig sammanställd upp på fakturan utifrån sammanställd avgifterna för varje Mätartyp av. Den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails) ger en detaljerad vy av förbrukningskostnad /-poster för varje dag och kan användas av både företagscertifikatutfärdare och Web Direct-kunder.

### <a name="what-is-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Vad är skillnaden mellan Price Sheet API och RateCard-API: et?
Dessa API: er tillhandahåller en liknande uppsättning data men har olika målgrupper. Informationen nedan.

- Price Sheet API: Den [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) innehåller den anpassade priser som har varit förhandling för en företagskund.
- RateCard API: Den [: T för RateCard](https://msdn.microsoft.com/library/mt219005.aspx) ger offentligt vända pris som gäller för Web Direct-kunder.

## <a name="next-steps"></a>Nästa steg

- Information om hur du programmässigt få insikt i din Azure-användning med Azure API: er finns i [översikt över Azure Consumption-API: et](billing-consumption-api-overview.md) och [Azure Billing API-översikt](billing-usage-rate-card-overview.md).
- Jämförelse mellan din faktura med detaljerade dagliga användningsfil och kostnadshanteringsrapporter i Azure portal finns [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
