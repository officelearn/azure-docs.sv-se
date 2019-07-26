---
title: Automatiserings scenarier för fakturering och kostnads hantering i Azure | Microsoft Docs
description: 'Lär dig hur vanliga scenarier för fakturering och kostnads hantering mappas till olika API: er.'
services: billing
documentationcenter: ''
author: bandersmsft
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
ms.author: banders
ms.openlocfilehash: 3d06df9b9a90f26b39afc17b8fcd02c85da567a1
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443256"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automatiserings scenarier för fakturering och kostnads hantering

Den här artikeln innehåller vanliga scenarier för fakturering och kostnads hantering i Azure. Den mappar dessa scenarier till API: er som du kan använda. Under varje scenario-till-API-mappning kan du hitta en sammanfattning av API: erna och de funktioner som de erbjuder.

## <a name="common-scenarios"></a>Vanliga scenarier

Du kan använda API: er för fakturering och kostnads hantering i flera scenarier för att besvara kostnads relaterade och användnings relaterade frågor. Här är en översikt över vanliga scenarier:

- **Faktura avstämning**: Debiterade Microsoft mig rätt belopp?  Vad är faktura och kan jag beräkna det själv?

- **Tvär debitering**: Nu när jag vet hur mycket jag debiteras, vem i organisationen behöver betala?

- **Kostnads optimering**: Jag vet hur mycket jag har debiteras. Hur kan jag få ut mer av pengar som jag kostar med Azure?

- **Kostnads uppföljning**: Jag vill se hur mycket jag kostar och använder Azure över tid. Vilka är trenderna? Hur kan jag göra bättre?

- **Azure-utgifter under månaden**: Hur mycket är min nuvarande månads utgifter hittills? Behöver jag göra några ändringar i mina utgifter och/eller användningen av Azure? När jag under månaden använder jag Azure?

- **Aviseringar**: Hur konfigurerar jag resursbaserade konsumtion eller penningbaserade aviseringar?

## <a name="scenario-to-api-mapping"></a>Scenario-till-API-mappning

|         API        | Faktura avstämning    | Tvär kostnader    | Kostnadsoptimering    | Kostnads uppföljning    | MIDMONTH-utgifter    | Aviseringar    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgetar                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace-debiteringar                |             X             |         X        |           X          |         X        |          X         |     X     |
| Prisdokument                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservationsrekommendationer |                           |                  |           X          |                  |                    |           |
| Information om reservation         |                           |                  |           X          |         X        |                    |           |
| Reservationssammanfattningar       |                           |                  |           X          |         X        |                    |           |
| Användningsinformation               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturerings perioder             |             X             |         X        |           X          |         X        |                    |           |
| Fakturor                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Oklassificerad användning               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenario-till-API-mappningen omfattar inte Enterprise förbruknings-API: er. Använd om möjligt de allmänna förbruknings-API: erna för nya utvecklings scenarier.

## <a name="api-summaries"></a>API-sammanfattningar

### <a name="consumption"></a>Förbrukning
Web Direct-och Enterprise-kunder kan använda alla följande API: er, förutom där det anges:

-   [Budget-API](https://docs.microsoft.com/rest/api/consumption/budgets) (*Endast företags kunder*): Skapa antingen kostnads-eller användnings budgetar för resurser, resurs grupper eller fakturerings mätare. När du har skapat budgetar kan du konfigurera aviseringar som meddelar dig när du har överskridit definierade budget trösklar. Du kan också konfigurera åtgärder som ska utföras när du har nått budget beloppen.

-   [API för Marketplace-debitering](https://docs.microsoft.com/rest/api/consumption/marketplaces): Hämta information om kostnader och användning på alla Azure Marketplace-resurser (Azure-partner erbjudanden). Du kan använda dessa data för att lägga till kostnader i alla Marketplace-resurser eller för att undersöka kostnader/användning för vissa resurser.

-   [Pris dokuments-API](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Endast företags kunder*): Hämta anpassad prissättning för alla mätare. Företag kan använda dessa data i kombination med användnings information och information om Marketplace-användning för att beräkna kostnader med hjälp av användnings-och Marketplace-data. 

-   [API för reservations rekommendationer](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Få rekommendationer för att köpa reserverade VM-instanser. Rekommendationer hjälper dig att analysera förväntade kostnads besparingar och inköps belopp. Mer information finns i [API: er för Azure reservation Automation](billing-reservation-apis.md).

-   [API för reservations information](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Se information om tidigare köpta VM-reservationer, till exempel hur mycket förbrukning som är reserverad jämfört med hur mycket som används. Du kan se information om data per VM-nivå. Mer information finns i [API: er för Azure reservation Automation](billing-reservation-apis.md).

-   [API för reservations Sammanfattning](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Se samlad information om VM-reservationer som din organisation har köpt, till exempel hur mycket förbrukning som är reserverad jämfört med hur mycket som används i mängden. Mer information finns i [API: er för Azure reservation Automation](billing-reservation-apis.md).

-   [API för användnings information](https://docs.microsoft.com/rest/api/consumption/usagedetails): Få information om kostnad och användning för alla Azure-resurser från Microsoft. Information anges i form av användnings informations poster, som för närvarande genereras en gång per mätning per dag. Du kan använda informationen för att summera kostnaderna för alla resurser eller undersöka kostnader/användning för vissa resurser.

-   [Ratecard-API](/previous-versions/azure/reference/mt219005(v=azure.100)): Hämta mätar priser om du är en webb direkt kund. Du kan sedan använda den returnerade informationen med information om resursanvändningen för att beräkna den förväntade fakturan manuellt. 

-   [Oklassificerad användnings-API](/previous-versions/azure/reference/mt219003(v=azure.100)): Hämta information om rå data innan Azure utför all mätning/debitering.

### <a name="billing"></a>Fakturering
-   [API för fakturerings perioder](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Fastställ en fakturerings period som ska analyseras, tillsammans med faktura-ID för den perioden. Du kan använda faktura-ID: n med fakturorna för fakturor.

-   [API för fakturor](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): Hämta hämtnings-URL: en för en faktura för en fakturerings period i PDF-format.

### <a name="enterprise-consumption"></a>Företags förbrukning
Följande API: er är endast för företag:

-   [Sammanfattnings-API för saldo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Få en månatlig Sammanfattning av information om saldon, nya inköp, Azure Marketplace service-kostnader, justeringar och kostnader för överanvändning. Du kan hämta den här informationen för den aktuella fakturerings perioden eller en period tidigare. Företag kan använda dessa data för att jämföra med manuellt beräknade sammanfattnings kostnader. Detta API tillhandahåller inte resursinformation eller en sammanställd vy över kostnader.

-   [API för användnings information](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Få information om Azure-användning (av Microsoft-erbjudanden) för den aktuella månaden, en viss fakturerings period eller en anpassad datum period. Företag kan använda dessa data för att manuellt beräkna fakturor utifrån pris och förbrukning. Företag kan också använda avdelnings-/organisations information för att utnyttja kostnader mellan organisationer. Datan ger en resurs bestämd vy av användning/kostnad.

-   [API för Marketplace Store-avgift](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Få information om Azure-användning (av partner erbjudanden) för den aktuella månaden, en viss fakturerings period eller en anpassad datum period. Företag kan använda dessa data för att manuellt beräkna fakturor utifrån pris och förbrukning. Företag kan också använda avdelnings-/organisations information för att utnyttja kostnader mellan organisationer. Detta API ger en resurs bestämd vy av användning/kostnad.

-   [API för pris dokument](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Hämta tillämplig taxa för varje mätare för den aktuella registreringen och fakturerings perioden. Du kan använda den här pris informationen i kombination med användnings information och information om användning av Marketplace för att beräkna den förväntade fakturan manuellt.

-   [API för fakturerings perioder](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Hämta en lista över fakturerings perioder. API: et ger dig också en egenskap som pekar på API-vägen för de fyra uppsättningarna med företags-API-data som hör till fakturerings perioden: BalanceSummary, UsageDetails, Marketplace-avgifter och pris dokument.

-   [API för reserverad instans rekommendationer](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Titta efter sju dagar, 30 dagar eller 60 dagar av virtuell dator användning och få rekommendationer för både och delade inköp. Du kan använda detta API för att analysera förväntade kostnads besparingar och rekommenderade inköps belopp. Mer information finns i [API: er för Azure reservation Automation](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Vad är skillnaden mellan API: er för företags rapportering och förbruknings-API: er? När ska jag använda dem?
Dessa API: er har en liknande uppsättning funktioner och kan besvara samma breda uppsättning frågor i fakturerings-och kostnads hanterings utrymmet. Men de riktar sig till olika mål grupper: 

- API: er för företags rapportering är tillgängliga för kunder som har registrerat en Enterprise-avtal med Microsoft och som ger dem till gång till förhandlade betalnings åtaganden och anpassad prissättning. API: erna kräver en nyckel som du kan hämta från [Enterprise Portal](https://ea.azure.com). En beskrivning av dessa API: er finns i [Översikt över rapporterings-API: er för företags kunder](billing-enterprise-api.md).

- Förbruknings-API: er är tillgängliga för alla kunder, med några få undantag. Mer information finns i [Översikt över Azure förbruknings-API](billing-consumption-api-overview.md) och [Azure FÖRBRUKNINGs-API](https://docs.microsoft.com/rest/api/consumption/)-referensen. Vi rekommenderar de tillhandahållna API: erna som lösning för de senaste utvecklings scenarierna. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Vad är skillnaden mellan API för användnings information och användnings-API: et?
Dessa API: er ger grundläggande olika data:

- [API för användnings information](https://docs.microsoft.com/rest/api/consumption/usagedetails) tillhandahåller användnings-och kostnads information för Azure per mätar instans. De tillhandahållna data har redan passerat genom kostnads mätnings systemet i Azure och kostnaden tillämpades, tillsammans med andra möjliga ändringar:

   - Ändringar av kontot för användning av förutbetalda betalnings åtaganden
   - Ändringar i kontot för användnings skillnader som identifieras av Azure

- Användnings- [API: et](/previous-versions/azure/reference/mt219003(v=azure.100)) tillhandahåller rå Azure-användnings information innan den passerar systemet för kostnads avläsning i Azure. Dessa data kanske inte har någon korrelation med användnings-eller avgifts beloppet som visas efter system för Azure Charge-avläsning.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Vad är skillnaden mellan API för faktura och användnings information?
Dessa API: er tillhandahåller en annan vy av samma data:

- [Faktura-API](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) : t är endast för WebDirect-kunder. Det ger en månatlig sammanställning av din faktura baserat på de aggregerade avgifterna för varje mätar typ. 

- [API för användnings information](https://docs.microsoft.com/rest/api/consumption/usagedetails) innehåller en detaljerad översikt över användnings-och kostnads posterna för varje dag. Både företags-och webb direkta kunder kan använda den.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Vad är skillnaden mellan pris dokumentets API och RateCard-API: et?
Dessa API: er tillhandahåller liknande data uppsättningar men har olika mål grupper:

- [Pris dokumentets API](https://docs.microsoft.com/rest/api/consumption/pricesheet) tillhandahåller den anpassade prissättningen som förhandlats för en företags kund.

- [Ratecard-API: et](/previous-versions/azure/reference/mt219005(v=azure.100)) tillhandahåller den offentliga prissättning som gäller för webb direkt kunder.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder Azure API: er för att program mera inblick i din Azure-användning finns i [Översikt över Azure förbruknings-API](billing-consumption-api-overview.md) och [Översikt över Azure fakturerings-API](billing-usage-rate-card-overview.md).

- Om du vill jämföra din faktura med den detaljerade dagliga användnings filen och kostnads hanterings rapporterna i Azure Portal, se [förstå din faktura för Microsoft Azure](billing-understand-your-bill.md).

- Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).
