---
title: Betala för Azure-prenumerationer med faktura
description: Beskriver hur du betala för Azure-prenumerationer med faktura.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 9ca726ef737ce4750018d2461bc4bcd6c7ebb5f5
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491241"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Betala för din Azure-prenumeration med faktura

Om du växlar för att betala med faktura, det innebär att du betalar din faktura inom 30 dagar från fakturadatum genom att kontrollera/banköverföring. Skicka en begäran till Azure-supporten om du vill bli berättigad att betala för din Azure-prenumeration med faktura. När din begäran har godkänts, du kan byta till faktura betala (kontrollera/banköverföring) i den [Azure-portalen](https://portal.azure.com).

> [!IMPORTANT]
> * Faktura betala (kontrollera/banköverföring) är endast tillgänglig för företag-konton.
> * Du måste betala alla utestående debiteringar innan du byter till faktura betala per användning.

## <a name="request-to-pay-by-invoice"></a>Begäran om att betala med faktura

1. Logga in på [Azure Portal](https://portal.azure.com/). Välj **hjälp + support** > **ny supportbegäran**.

    ![Hjälp och support länk](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Välj **fakturering** som den **typ av problem**. Den *typ av problem* är kategorin support begäran. Välj den prenumeration som du vill betala med faktura, Välj ett supportavtal och välj sedan **nästa**.

3. Välj **Pay by Invoice** (Betala med faktura) i rutan **Problem Type** (Problemtyp). Den *problemtyp* är underkategorin support begäran.

4. Ange följande information i den **information** och väljer sedan **nästa**.

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

    - Den **företagsnamn** och **företagets adress** måste matcha den information som du angav för Azure-konto. Om du vill visa eller uppdatera information, se [ändra profilinformationen Azure-konto](billing-how-to-change-azure-account-profile.md).
    - Du måste lägga till din kontaktinformation för fakturering i Azure-portalen innan det att kreditgränsen kan godkännas. Kontaktuppgifter måste vara relaterat till företagets Leverantörsreskontra eller ekonomi avdelning. Om du vill uppdatera fakturering kontaktinformation, går du till [Azure Kontocenter](https://account.azure.com/Profile).

5. Verifiera din kontaktinformation, din önskade kontaktmetod och klicka på **Skapa**.

Om vi behöver göra en kreditkontroll på grund av mängden kredit som du behöver skickar vi du en kredit.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Växla till faktura betala (kontrollera/banköverföring)

När du har godkänt för att betala med faktura, kan du byta till faktura betala (kontrollera/banköverföring) i Azure-portalen.

Om du har ett konto med Microsoft onlinetjänster-programmet kan växla du din Azure-prenumeration för att kontrollera/banköverföring överföring. Om du har ett avtal för Microsoft-kund kan växla du din faktureringsinformation profil för att kontrollera/banköverföring överföring. [Lär dig att kontrollera vilken typ av konto](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Växla Azure-prenumeration till check/banköverföring överföring

Följ stegen nedan för att växla din Azure-prenumeration till faktura betala (kontrollera/banköverföring). **När du byter till faktura betala (kontrollera/banköverföring) du kan inte växla tillbaka till kreditkort**.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärmbild som visar sökning](./media/billing-how-to-pay-by-invoice/search.png)

1. Välj den prenumeration som du vill byta till faktura betalning.
1. Välj **Betalningsmetoder**.
1. I kommandofältet klickar du på den **betala med faktura** knappen.

    ![Skärmbild som visar betala med faktura-knappen](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Växla fakturering profil för att kontrollera/banköverföring överföring

Följ stegen nedan för att växla en fakturering profil för att kontrollera/banköverföring överföring. Observera att endast den person som registrerat sig för Azure kan ändra standardbetalningsmetod för en profil för fakturering.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **Cost Management + fakturering**.
1. I menyn till vänster, klickar du på **fakturering profiler**.

    ![Skärmbild som visar fakturering profil i menyn](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Välj en profil för fakturering.
1. I menyn till vänster väljer **betalningsmetoder**.

   ![Skärmbild som visar Betalningsmetoder i menyn](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Klicka på den blå banderoll som säger att du är berättigad att betala med kontroll/banköverföring.

    ![Skärmbild som visar blå banderoll växla för att kontrollera/banköverföring](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Om det behövs uppdaterar fakturering kontaktinformationen på den [Azure Kontocenter](https://account.azure.com/Profile).
