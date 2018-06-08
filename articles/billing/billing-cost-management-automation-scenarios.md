---
title: Azure fakturerings- och kostnaden för automation hanteringsscenarier | Microsoft Docs
description: 'Lär dig hur vanliga fakturering och kostnadshanteringen scenarier är mappas till olika API: er.'
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
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: ac0c4c0bad98c3057fb554e6011f1c601cd97dee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851327"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Fakturerings- och automation hanteringsscenarier

Vanliga scenarier för hantering av fakturerings- och utrymmet nedan och mappas till olika API: er som kan användas i dessa scenarier. En sammanfattning av alla tillgängliga API: er och funktioner som de erbjuder finns under scenario för API-mappning. 

## <a name="common-scenarios"></a>Vanliga scenarier 

Du kan använda debitering och kostnadshanteringen API: er i en mängd olika scenarier för att besvara kostnad och användning relaterade frågor.  En översikt över vanliga scenarier finns nedan.

- **Faktura avstämning** -har Microsoft debiterar mig rätt belopp?  Vad är min faktura och kan jag beräkna den själv?

- **Mellan avgifter** – nu när jag vet hur mycket jag är att du debiteras, som i min organisation behöver betala?

- **Kostnad optimering** -jag vet hur mycket jag har debiterats, men hur kan jag få ut mer av pengar jag utgifter i Azure?

- **Kostnadsuppföljning** – du vill se hur mycket jag utgifter och använder Azure över tid. Vad är trenderna? Hur kan jag göra bättre?

- **Azure utgifter under månaden** – hur mycket är min aktuella månaden har tillbringar hittills? Behöver jag göra eventuella justeringar i min utgifter och/eller användning av Azure? När under månaden kan jag använda Azure mest?

- **Ställa in aviseringar** – jag skulle vilja konfigurera resursbaserade förbrukning eller monetära-baserade aviseringar.

## <a name="scenario-to-api-mappings"></a>Scenario till API-mappningar

|         API/Scenario        | Fakturan avstämning    | Mellan avgifter    | Kostnad optimering    | Kostnadsuppföljning    | Mitt i månaden tillbringar    | Aviseringar    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgetar                     |                           |                  |           X          |                  |                    |     X     |
| Marknadsplatser                |             X             |         X        |           X          |         X        |          X         |     X     |
| Prisdokument                 |             X             |         X        |           X          |         X        |          X         |           |
| Rekommendationer för reservation |                           |                  |           X          |                  |                    |           |
| Reservation information         |                           |                  |           X          |         X        |                    |           |
| Reservation sammanfattningar       |                           |                  |           X          |         X        |                    |           |
| Användningsinformation               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fakturering punkter             |             X             |         X        |           X          |         X        |                    |           |
| Fakturor                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Oklassificerade användning               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenariot för att API mappningar nedan innehåller inte API: er för Enterprise-förbrukning. Om möjligt bör du använda allmänna förbrukning API: erna för att åtgärda net utvecklingsscenarier som vidarebefordras.

## <a name="api-summaries"></a>API-sammanfattningar

### <a name="consumption"></a>Förbrukning
(*Web direkt + företagskunder för alla API: er Förutom dessa kallas ut under*)

-   **Budgetar** (*Enterprise-kunder*): Använd den [budgetar API](https://docs.microsoft.com/rest/api/consumption/budgets) att skapa kostnaden eller användning budgetar för resurser, resursgrupper eller fakturering mätare.  När du har skapat budgetar kan aviseringar konfigureras för att meddela när användardefinierade budget tröskelvärden överskrids. Åtgärder kan också konfigureras uppstår när budgetbelopp har uppnåtts.
-   **Marknadsplatser**: Använd den [Marketplace avgifter API](https://docs.microsoft.com/rest/api/consumption/marketplaces) att hämta data för kostnad och användning på alla Marketplace-resurser (Azure 3 part erbjudanden). Den här informationen kan användas för att lägga till kostnader för alla Marketplace-resurser eller undersöka kostnader/användning på specifika resurser.
-   **Prisdokument** (*Enterprise-kunder*): Enterprise-kunder kan använda den [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) att hämta sina egna priser för alla mätare. Företag kan använda informationen i kombination med information om användning av information och marknadsplatser användning för att utföra beräkningarna använder användnings-och marketplace. 
-   **Rekommendationer för reservation**: Använd den [Reservation rekommendationer API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) att få rekommendationer om köp av VM reserverade instanser. Rekommendationer är avsedda att möjligheten för kunder att analysera förväntade besparingar och inköp.
-   **Reservation information**: Använd den [Reservation information API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) att visa information på tidigare köpt VM-reservationer, till exempel hur mycket förbrukning har reserverats jämfört med hur mycket faktiskt används. Du kan se data i detalj per-VM-nivå.
-   **Reservation sammanfattningar**: Använd den [Reservation sammanfattningar API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) att se samlar in information om tidigare köpt VM-reservationer, till exempel hur mycket förbrukning har reserverats jämfört med hur mycket faktiskt används i mängden. 
-   **Användningsinformation**: Använd den [användning information API](https://docs.microsoft.com/rest/api/consumption/usagedetails) få kostnad och användning på alla Azure 1 part resurser. Informationen är i form av information om poster, som för närvarande orsakat en gång per mätaren per dag. Informationen kan användas för att lägga till upp kostnaderna för alla resurser eller undersöka kostnader/användning på specifika resurser.
-   **RateCard**: Web direkt kunder kan använda den [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) få sina mätaren priser. De kan sedan använda returnerade informationen med deras användning resursinformationen för att beräkna förväntade faktura manuellt. 
-   **Oklassificerat användning**: du kan använda den [oklassificerade användning API](https://msdn.microsoft.com/library/azure/mt219003.aspx) att hämta rådata användningsinformation innan alla avläsning/debitering av Azure.

### <a name="billing"></a>Fakturering
-   **Fakturering punkter**: Använd den [fakturering punkter API](https://docs.microsoft.com/rest/api/billing/billingperiods) att fastställa faktureringsperiod att analysera, tillsammans med fakturan ID-numret för denna period. Fakturan ID kan användas med faktura API: et nedan. 
-   **Fakturor**: Använd den [fakturor API](https://docs.microsoft.com/rest/api/billing/invoices) att hämta nedladdnings-URL för en faktura för en given faktureringsperioden i PDF-format.

### <a name="enterprise-consumption"></a>Enterprise-förbrukning
*(Alla API: er Enterprise endast)*

-   **Belastningsutjämna sammanfattning**: Använd den [saldo sammanfattning API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) att hämta ett månatligt för information om saldon, nya inköp, Azure Marketplace serviceavgifter, justeringar och överförbrukning avgifter. Du kan hämta den här informationen för den aktuella faktureringsperioden eller föregående period. Företag kan använda informationen för att utföra en jämförelse med manuellt beräknade sammanfattning avgifter. Detta API innehåller inte information om resurs eller en aggregerad vy över kostnaderna.
-   **Användningsinformation**: Använd den [användning information API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) få 1 part Azure användning detaljerad information om den aktuella månaden, en specifik faktureringsperiod eller en anpassad period. Företag kan använda dessa data för att beräkna manuellt faktura baserat på hastighet och förbrukning av och kan också använda avdelning/organisation informationen till attributet kostnader mellan olika organisationer. Data innehåller en resurs-specifikt för användning/kostnad.
-   **Marketplace Store kostnad**: Använd den [Marketplace Store kostnad API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) få 3 part Azure användning detaljerad information om den aktuella månaden, en specifik faktureringsperiod eller en anpassad period. Företag kan använda dessa data för att beräkna manuellt faktura baserat på hastighet och förbrukning av och kan också använda avdelning/organisation informationen till attributet kostnader mellan olika organisationer. Marketplace store kostnad API innehåller en resurs-specifikt för användning/kostnad.
-   **Prisdokument**: den [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) ger den tillämpliga hastigheten för varje mätaren för den angivna registrering och fakturering Period. Hastigheten med vilken informationen kan användas tillsammans med information om användning av information och marknadsplatser användning för att beräkna förväntade faktura manuellt.
-   **Fakturering punkter**: Använd den [fakturering punkter API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) att hämta en lista över fakturering punkter tillsammans med en egenskap som pekar på API-väg för de fyra datauppsättningarna Enterprise-API som hör till den faktureringsperioden - BalanceSummary, UsageDetails Marketplace-debiteringar och Prisdokument.
-   **Reserverade instans rekommendationer**: den [reserverade instans rekommendationer API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) tittar på kundens 7 dagarna, 30 dagar eller 60 dagar användning av virtuella datorer och rekommendationer för enskilda och delade inköp. Den reserverade instansen API ger kunder möjlighet att analysera förväntade besparingar och rekommenderade inköp.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder Azure Billing API: er att genom programmering få bättre överblick på din användning av Azure finns [fakturering API översikt över Azure](billing-usage-rate-card-overview.md).