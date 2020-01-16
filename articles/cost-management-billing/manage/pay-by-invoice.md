---
title: Betala för Azure-prenumerationer med faktura
description: Beskriver hur du betalar för Azure-prenumerationer med faktura.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: banders
ms.openlocfilehash: a0f012145788d2d1d4935e10691859e5aaf71255
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75994342"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Betala för din Azure-prenumeration med faktura

Om du byter till att betala med faktura innebär det att du betalar din faktura inom 30 dagar från fakturadatumet via check eller banköverföring. För att du ska kunna betala Azure-prenumerationen med faktura måste du först skicka en begäran till Azure-supporten. När din begäran har godkänts kan du byta till betalning med faktura (check/banköverföring) i [Azure-portalen](https://portal.azure.com).

> [!IMPORTANT]
> * Fakturabetalning (check/banköverföring) är bara tillgängligt för företagskonton.
> * Betala alla utestående avgifter innan du byter till fakturabetalning.
> * För närvarande stöds inte faktura betalning för Global Azure i Kina.

## <a name="request-to-pay-by-invoice"></a>Begära att betala via faktura

1. Gå till [Azure Portal](https://portal.azure.com) för att skicka in en support förfrågan. Sök efter och välj **Hjälp + Support**.

    ![Sök efter hjälp och support, Microsoft Azure-portalen](./media/pay-by-invoice/search-for-help-and-support.png)

2. Välj **Ny supportbegäran**.

    ![Ny länk till support begär Ande, hjälp och support-skärmen, Microsoft Azure-portalen](./media/pay-by-invoice/help-and-support.png)

2. Välj **Fakturering** som **Problemtyp**. *Problemtyp* är kategorin för supportbegäran. Välj den prenumeration du vill betala för med faktura. Välj sedan ett supportavtal och därefter **Nästa**.

3. Välj **Betalning** som **Problemtyp**. *Problemtypen* är underkategorin för supportbegäran.

4. Välj **växel att betala efter faktura** som **typ av problem**.

5. Ange följande information i rutan **Information** och välj sedan **Nästa**.

         New or existing customer:
         If existing, current payment method:
         Order ID (requesting for invoice option):
         Account Admins Live ID (or Org ID) (should be company domain):
         Commerce Account ID:
         Company Name (as registered under VAT or Government Website):
         Company Address (as registered under VAT or Government Website):
         Company Website:
         Country:
         TAX ID/ VAT ID:
         Company Established on (Year):
         Any prior business with Microsoft:
         Contact Name:
         Contact Phone:
         Contact Email:
         Justification on why you prefer Invoice option over credit card:

         For cores increase, provide the following additional information:

         (Old quota) Existing Cores:
         (New quota) Requested cores:
         Specific region & series of Subscription:

    - **Företagsnamn** och **Företagsadress** ska matcha den information som du angav för Azure-kontot. Om du vill visa eller uppdatera informationen går du till [Ändra information för din Azure-kontoprofil](change-azure-account-profile.md).
    - Lägg till dina kontaktuppgifter för fakturering i Azure-portalen innan kreditgränsen kan godkännas. Kontaktuppgifterna bör vara relaterade till företagets leverantörsreskontra eller ekonomiavdelning. Om du vill uppdatera kontaktuppgifterna för fakturering går du till [Azure-kontocenter](https://account.azure.com/Profile).

6. Verifiera din kontakt information och önskad kontakt metod och välj sedan **skapa**.

Om vi behöver utföra en kreditkontroll på grund av den mängd kredit som du behöver skickar vi en ansökan om kreditkontroll.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Byta till fakturabetalning (check/banköverföring)

När du har godkänts för betalning med faktura kan du byta till betalning med faktura (check/banköverföring) i Azure-portalen.

Om du har ett Microsoft Online Services-programkonto kan du byta till att betala Azure-prenumerationen via check/banköverföring. Med ett Microsoft-kundavtal kan du byta faktureringsprofil till check/banköverföring. [Lär dig hur du kontrollerar din kontotyp](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Byta Azure-prenumeration till check/banköverföring

Följ stegen nedan för att byta din Azure-prenumeration till fakturabetalning (check/banköverföring). *När du har bytt till fakturabetalning (check/banköverföring) kan du inte byta tillbaka till betalning med kreditkort*.

1. Gå till [Azure Portal](https://portal.azure.com) för att logga in som konto administratör. Sök efter och välj **Cost Management + fakturering**.

    ![Sök efter Cost Management och fakturering Microsoft Azure-portalen](./media/pay-by-invoice/search.png)

1. Välj den prenumeration som du vill börja betala med faktura.
1. Välj **Betalningsmetoder**.
1. I kommando fältet väljer du knappen **betala per faktura** .

    ![Knappen betala per faktura, betalnings sätt Microsoft Azure-portalen](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Byta faktureringsprofil till check/banköverföring

Följ stegen nedan om du vill byta faktureringsprofil till check/banköverföring. Endast den person som registrerade sig för Azure kan ändra standardbetalningsmetoden för en faktureringsprofil.

1. Gå till [Azure Portal](https://portal.azure.com) Visa din fakturerings information. Sök efter och välj **Cost Management + fakturering**.
1. I menyn väljer du **fakturerings profiler**.

    ![Meny alternativ för fakturerings profiler, Cost Management och fakturering Microsoft Azure-portalen](./media/pay-by-invoice/billing-profile.png)

1. Välj en faktureringsprofil.
1. I menyn **fakturerings profil** väljer du **betalnings sätt**.

   ![Meny alternativ för betalnings metoder, fakturerings profiler, Cost Management Microsoft Azure-portalen](./media/pay-by-invoice/billing-profile-payment-methods.png)

1. Välj banderollen som säger att du är berättigad att betala enligt check-/överförings överföring.

    ![Banderoll för att växla till kontroll/tråd, betalnings metoder Microsoft Azure-portalen](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Om det behövs uppdaterar du dina kontaktuppgifter för fakturering i [Azure-kontocenter](https://account.azure.com/Profile).
