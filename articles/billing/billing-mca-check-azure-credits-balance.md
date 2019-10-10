---
title: Spåra Azure-kreditbalansen för ett Microsoft-kundavtal
description: Lär dig hur du spårar Azure-kreditbalansen för ett Microsoft-kundavtal.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ea3fc21891f1e4d4e744449032a4b2cfcdfbb2f0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177534"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Spåra Azure-kreditbalansen för Microsoft-kundavtal

Du kan kontrollera Azure-kreditbalansen för Microsoft-kundavtal i Azure-portalen. Du använder krediter för att betala för avgifter som omfattas av krediterna.

Du debiteras när du använder produkter som inte omfattas av krediterna eller när din användning överskrider ditt kreditbelopp. Mer information finns i [Produkter som inte omfattas av Azure-krediter](#products-that-arent-covered-by-azure-credits).

Den här artikeln gäller ett faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Kontrollera ditt kreditsaldo

1. Logga in på [Azure-portalen]( https://portal.azure.com).

2. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar en sökning i portalen efter kostnadshantering och fakturering](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Välj **Azure-krediter** på vänster sida. Beroende på din åtkomst kan du behöva välja ett faktureringskonto eller en faktureringsprofil och sedan välja **Azure-krediter**.

4. På sidan Azure-krediter visas följande information:

   ![Skärmbild av kreditsaldo och transaktioner för en faktureringsprofil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Period               | Definition                           |
   |--------------------|--------------------------------------------------------|
   | Uppskattat saldo  | Uppskattat belopp med krediter som du har när alla fakturerade och väntande transaktioner har tagits med i beräkningen |
   | Aktuellt saldo    | Summan av krediterna från din senaste faktura. Inga väntande transaktioner ingår |
   | Transaktioner       | Alla faktureringstransaktioner som påverkar ditt Azure-kreditsaldo |

   När ditt beräknade saldo sjunker till 0 debiteras du för all användning, inklusive för produkter som omfattas av krediter.

6. Välj **Kreditlista** om du vill visa en lista med krediter för faktureringsprofilen. Kreditlistan innehåller följande information:

   ![Skärmbild av kreditlistor för en faktureringsprofil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Period | Definition |
   |---|---|
   | Uppskattat saldo | Den mängd Azure-kredit som du har efter att subtraktion av ej fakturerade, kreditberättigade avgifter från ditt aktuella saldo|
   | Aktuellt saldo | Den mängd Azure-kredit som du har innan ej fakturerade, kreditberättigade avgifter tas med i beräkningen. Det beräknas genom att nya Azure-krediter som du har fått läggs till i kreditsaldot vid tiden för sin senaste faktura|
   | Källa | Kreditens förvärvskälla |
   | Startdatum | Det datum då du skaffade krediten |
   | Förfallodatum | Det datum då krediten upphör att gälla |
   | Saldo | Saldot från din senaste faktura |
   | Ursprungligt belopp | Den ursprungliga mängden kredit |
   | Status | Aktuell kreditstatus. Statusen kan vara aktiv, används, upphörd eller håller på att upphöra |

## <a name="how-credits-are-used"></a>Så används krediter

I ett faktureringskonto för ett Microsoft-kundavtal använder du faktureringsprofiler för att hantera fakturor och betalningsmetoder. En månadsfaktura skapas för varje faktureringsprofil, och du använder betalningsmetoderna för att betala fakturan.

Azure-krediter är en av betalningsmetoderna. Du får kredit från Microsoft såsom kampanjkredit och kredit på tjänstnivå. Dessa krediter tilldelas till en faktureringsprofil. När en faktura genereras för faktureringsprofilen tillämpas krediter automatiskt på det totala fakturerade beloppet för beräkning av det belopp som du behöver betala. Du betalar återstående belopp med en annan betalningsmetod, till exempel check eller banköverföring.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkter som inte omfattas av Azure-krediter

 Följande produkter omfattas inte av dina Azure-krediter. Du debiteras för användning av dessa produkter oavsett ditt kreditsaldo:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registrerad användare
- Openlogic
- Registrerad användare för Remote Access Rights XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (månatligen)
- Visual Studio Enterprise (årsvis)
- Visual Studio Professional (månatligen)
- Visual Studio Professional (årsvis)
- Azure Marketplace-produkter
- Azure-supportplaner

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.

Om du behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonton för Microsofts-kundavtal](billing-mca-overview.md)
- [Förstå termerna på fakturan för ditt Microsoft-kundavtal](billing-mca-understand-your-invoice.md)
