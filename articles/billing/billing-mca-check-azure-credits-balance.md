---
title: Spåra Azure kreditsaldo för Microsoft kundavtal | Microsoft Docs
description: Lär dig att kontrollera Azure kreditsaldo för Microsoft kundavtal.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1c1a706fb423f440d40c49a9a19cc54ed63ac636
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249176"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Spåra Azure kreditsaldo för Microsoft kundavtal

Du kan kontrollera Azure saldot för Microsoft kundavtal i Azure-portalen. Du kan använda krediter för att betala för produkter som omfattas av krediterna.

Du debiteras när du använder produkter som inte omfattas av krediterna eller användningen överskrider din kreditsaldo. Mer information finns i [produkter som inte täcks av Azure-krediter.](#products-that-arent-covered-by-azure-credits)

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Kontrollera kreditsaldo i Azure portal

1. Logga in på [Azure Portal]( http://portal.azure.com).

2. Sök på **Cost Management + fakturering**.

   ![Skärmbild som visar search på portalen för kostnadshantering + fakturering](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Gå till fakturering profilen. Beroende på din åtkomst kan du behöva välja ett faktureringskonto. Faktureringskonto, Välj **fakturering profiler** och sedan en profil för fakturering.

4. Välj **Azure-krediter**.

5. Azure-krediter sidan visar följande information:

   ![Skärmbild av kreditsaldo och transaktioner för en profil för fakturering](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Period               | Definition                           |
   |--------------------|--------------------------------------------------------|
   | Uppskattat saldo  | Uppskattade mängden krediter som du har när du överväger alla faktureras och pågående transaktioner |
   | Aktuellt saldo    | Mängden krediter från och med den senaste fakturan. De omfattar inte eventuella väntande transaktioner |
   | Transaktioner       | Alla fakturering transaktioner som påverkade ditt saldo i Azure-kredit |

   När din uppskattade saldo sjunker till 0, debiteras du för all användning, inklusive för produkter som omfattas av krediter.

6. Välj **krediter lista** att visa en lista över krediter för fakturerings-profilen. Krediter listan innehåller följande information:

   ![Skärmbild av krediter listor för en profil för fakturering](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Period                 | Definition                           |
   |----------------------|--------------------------------------------------------|
   | Källa               | Förvärv källan kredit |
   | Startdatum           | Det datum när du har köpt kredit |
   | Upphörandedatum      | Det datum då krediten upphör att gälla |
   | Saldo              | Saldo från och med den senaste fakturan |
   | Ursprungligt belopp      | Ursprunglig mängd kredit |
   | Status               | Den aktuella statusen för kredit. Status kan vara aktiv, används, upphört att gälla eller upphör att gälla |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Hur krediter används i Microsoft kundavtal

I något faktureringskonto för en Microsoft-kundavtal använder du fakturering profiler för att hantera dina fakturor och betalningsmetoder. En månadsfaktura genereras för varje fakturering profil och du använder betalningsmetoder för att betala fakturan.

Azure-krediter är en av betalningsmetoder. Du får kredit från Microsoft som erbjudanden kredit och nivån servicekrediter. Dessa krediter har tilldelats till en profil för fakturering. När en faktura genereras för fakturering profilen tillämpas automatiskt krediter på totalt fakturerat belopp att beräkna den mängd som du behöver betala. Du betalar återstående belopp med en annan betalningsmetod som kontrollen eller banköverföring.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkter som inte täcks av Azure-krediter

 Följande produkter är inte omfattas av dina Azure-krediter. Du debiteras för användning av dessa produkter oavsett dina kreditsaldo:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Registrerad användare
- Openlogic
- Fjärråtkomst Rights XenApp Essentials registrerad användare
- Ubuntu Advantage
- Visual Studio Enterprise (per månad)
- Visual Studio Enterprise (årligt)
- Visual Studio Professional (per månad)
- Visual Studio Professional (årligt)
- Azure Marketplace-produkter
- Azure-supportplaner

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.

## <a name="next-steps"></a>Nästa steg

- [Förstå faktureringskonto för Microsoft kundavtal](billing-mca-overview.md)
- [Förstå villkoren på din faktura för Microsoft kundavtal](billing-mca-understand-your-invoice.md)