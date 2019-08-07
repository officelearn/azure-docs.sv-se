---
title: Betala för Azure-prenumerationer efter faktura
description: Beskriver hur du betalar för Azure-prenumerationer per faktura.
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
ms.openlocfilehash: 90c7014e3c22ac7186854d5c01b911aa630d20dd
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774667"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Betala för din Azure-prenumeration efter faktura

Om du växlar till betala per faktura innebär det att du betalar din faktura inom 30 dagar från faktura datumet med check-/överförings överföring. För att bli berättigad att betala för din Azure-prenumeration via faktura skickar du en förfrågan till Azure-supporten. När din begäran har godkänts kan du växla till faktura betalning (check/Wire Transfer) i [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> * Faktura lön (kontroll/överföring) är endast tillgängligt för företags konton.
> * Du måste betala alla utestående avgifter innan du växlar till faktura lönen.

## <a name="request-to-pay-by-invoice"></a>Begäran om att betala med faktura

1. Logga in på [Azure Portal](https://portal.azure.com/). Välj **Hjälp + Support** > **ny supportbegäran**.

    ![Länk till hjälp och support](./media/billing-how-to-pay-by-invoice/help-and-support.png)

2. Välj **fakturering** som **ärende typ**. *Ärende typen* är kategorin support förfrågan. Välj den prenumeration som du vill betala per faktura, Välj en Support plan och välj sedan **Nästa**.

3. Välj **betalning** som **problem typ**. *Problem typen* är under kategorin support förfrågan.

4. Välj **växel att betala efter faktura** som **typ av problem**

5. Ange följande information i rutan **information** och välj sedan **Nästa**.

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

    - **Företags namnet** och **företags adressen** ska matcha den information som du har angett för Azure-kontot. Om du vill visa eller uppdatera informationen går du till [ändra profil information för Azure-kontot](billing-how-to-change-azure-account-profile.md).
    - Du måste lägga till fakturerings kontakt uppgifter i Azure Portal innan kredit gränsen kan godkännas. Kontakt uppgifterna bör vara relaterade till företagets leverantörs reskontra eller ekonomi avdelning. Om du vill uppdatera fakturerings kontakt informationen går du till [Azure-kontocenter](https://account.azure.com/Profile).

6. Verifiera din kontaktinformation, din önskade kontaktmetod och klicka på **Skapa**.

Om vi behöver köra en kredit kontroll på grund av den kredit kredit som du behöver skickar vi ett kredit kontroll program.

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>Växla till faktura betalning (kontroll/överföring)

När du har godkänt att betala med faktura kan du växla till faktura betalning (check/Wire Transfer) i Azure Portal.

Om du har ett program konto för Microsoft Online Services kan du byta Azure-prenumeration för att kontrol lera/byta överföring. Om du har ett kund avtal från Microsoft kan du byta fakturerings profil för att kontrol lera/byta överföring. [Lär dig hur du kontrollerar konto typen](#check-access-to-a-microsoft-customer-agreement).

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Växla Azure-prenumeration för att kontrol lera/byta överföring

Följ stegen nedan för att byta ut din Azure-prenumeration till faktura lön (check/Wire Transfer). **När du växlar till faktura betalning (kontroll/överföring) kan du inte byta tillbaka till kredit kortet**.

1. Logga in på [Azure Portal](https://portal.azure.com) som konto administratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärm bild som visar sökning](./media/billing-how-to-pay-by-invoice/search.png)

1. Välj den prenumeration som du vill byta till faktura betalning.
1. Välj **Betalningsmetoder**.
1. I kommando fältet klickar du på knappen **betala per faktura** .

    ![Skärm bild som visar knappen betala per faktura](./media/billing-how-to-pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>Växla fakturerings profil till kontroll/överföring

Följ stegen nedan om du vill byta fakturerings profil för att kontrol lera/byta överföring. Observera att bara den person som har registrerat sig för Azure kan ändra standard betalnings metoden för en fakturerings profil.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **Cost Management + fakturering**.
1. Klicka på **fakturerings profiler**i menyn till vänster.

    ![skärm bild som visar fakturerings profil i menyn](./media/billing-how-to-pay-by-invoice/billing-profile.png)

1. Välj en fakturerings profil.
1. I menyn till vänster väljer du **betalnings sätt**.

   ![Skärm bild som visar betalnings metoder på menyn](./media/billing-how-to-pay-by-invoice/billing-profile-payment-methods.png)

1. Klicka på den blå banderollen som säger att du är berättigad till kontroll/överföring.

    ![Skärm bild som visar blå banderoll för att växla till kontroll/tråd](./media/billing-how-to-pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrol lera åtkomsten till ett Microsofts kund avtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Om det behövs uppdaterar du din fakturerings kontakt information på [Azure-kontocenter](https://account.azure.com/Profile).
