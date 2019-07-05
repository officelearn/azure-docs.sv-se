---
title: Ändra ditt kreditkort för Azure
description: Beskriver hur du ändrar du kreditkortet som används för att betala för en Azure-prenumeration.
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
ms.openlocfilehash: 7c04e33d6199d3930be28ce84458e9c3a743eb8a
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491330"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Lägga till, uppdatera eller ta bort ett kreditkort för Azure

I Azure-portalen kan du lägga till ett nytt kreditkort, uppdatera ett befintligt kreditkort eller ta bort ett kreditkort som du inte använder. Du måste vara en [kontoadministratör](billing-subscription-transfer.md#whoisaa) du gör dessa ändringar.

Om du har en [Microsoft kundavtal](#check-access-to-a-microsoft-customer-agreement), betalningsmetoder är associerade med fakturering profiler. Lär dig hur du [ändra standardbetalningsmetod för en profil för fakturering](#change-payment-method-for-a-billing-profile). Endast användare som registrerat sig för Azure kan uppdatera betalningsmetoden.

**Vill du istället för att betala med faktura (kontrollera/banköverföring)?** Se [betala för Azure-prenumerationer med faktura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Lägga till ett nytt kreditkort i en Azure-prenumeration

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kreditkort till.
1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Välj ”+” i det övre vänstra hörnet om du vill lägga till ett kort. Ett kreditkortsformulär visas till höger.
1. Ange kreditkortsinformation.

    ![Skärmbild som visar att lägga till ett nytt kort.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Att göra det här kortet din aktiva betalningsmetod, markerar du kryssrutan bredvid **gör det här till min aktiva betalningsmetod** ovan i formuläret. Det här kortet blir det aktiva betalningsmedlet för alla prenumerationer som använder samma kort som den valda prenumerationen.

1. Välj **Nästa**.

Om du får ett felmeddelande när du lägger till kreditkortet [kreditkort avvisade på Azure-registrering](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Uppdatera befintligt kreditkort

Om ditt kreditkort hämtar förnyas och talet förblir oförändrat, uppdatera den befintliga informationen kreditkort som förfallodatumet. Om ändringarna för kreditkort eftersom kortet går förlorad, stulits eller upphört att gälla, följer du stegen i den [lägga till ett kreditkort som betalningssätt](#addcard) avsnittet. Du behöver inte uppdatera CVV.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klicka på kreditkortet som du vill redigera. Ett kreditkortsformulär visas till höger.

    ![Skärmbild som visar kreditkort som valts.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Uppdatera informationen för kreditkort.
1. Välj **Spara**.

## <a name="use-a-different-credit-card"></a>Använd ett annat kreditkort

Om mer än en av dina prenumerationer har samma aktiv betalningsmetod, uppdateras ändra den aktiva betalningsmetoden på någon av de här prenumerationerna dessutom aktiv betalningsmetod på de andra.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kreditkort till.
1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan bredvid det kortet som du vill göra den aktiv betalningsmetoden.
1. Klicka på **Ställ in som aktiv**.
    ![Skärmbild som visar kreditkort valt och Ställ in som aktiv.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Ta bort ett kreditkort från kontot

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Välj **kostnadshantering + fakturering** till vänster på sidan.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Under **fakturering**väljer **betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan bredvid det kortet som du vill ta bort.
1. Klicka på **Ta bort**.

Om ditt kreditkort är aktiv betalningsmetod för någon av dina Microsoft-prenumerationer kan du inte ta bort det från ditt Azure-konto. Ändra den aktiva betalningsmetoden för alla prenumerationer som är länkad till det här kreditkortet och försök igen
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit or debit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit or debit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit or debit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit or debit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit or debit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Ändra betalningsmetod för en profil för fakturering

Du måste vara den person som registrerat sig för Azure om du vill ändra betalningsmetod för en profil för fakturering.

Om du vill växla standardbetalningsmetod att kontrollera/ledare överföring och lär dig hur du [växla en fakturering profil för att kontrollera/banköverföring överföring](billing-how-to-pay-by-invoice.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **Cost Management + fakturering**.
1. I menyn till vänster, klickar du på **fakturering profiler**.

    ![Skärmbild som visar fakturering profil i menyn](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Välj en profil för fakturering.
1. I menyn till vänster väljer **betalningsmetoder**.

   ![Skärmbild som visar Betalningsmetoder i menyn](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Ovan standardbetalningsmetod, klickar du på **ändra**.

    ![Skärmbild som visar ändra knappen](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Välj ett befintligt kort eller lägga till en ny.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
I följande avsnitt besvara vanliga frågor om hur du ändrar din kortinformation kreditkort eller bankkort.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Min prenumeration har inaktiverats. Varför kan jag ta bort mitt kreditkort nu?

När prenumerationen har inaktiverats eller har avbrutits, vänta vi 90 dagar innan din prenumeration tas bort permanent. Vi håller din betalningsmetod på fil under kvarhållningsperioden om du vill återaktivera prenumerationen. Efter det raderas prenumerationen.

Om du vill ta bort ditt kreditkort innan 90-dagars kvarhållning har löpt ut, [återaktivera din prenumeration](billing-subscription-become-disable.md). Om du inte kan aktivera [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Varför får jag ”din inloggningssession har upphört att gälla. Klicka här för att logga in igen ”?

Om du får detta felmeddelande även om du redan har loggat och tillbaka i, försök igen med en privat surfning.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hur använder jag ett nytt kort för varje prenumeration som jag har?

Tyvärr om dina prenumerationer redan använder samma kort, går det inte att separera dem för att använda olika kort. När du registrerar dig för en ny prenumeration kan välja du dock att använda en ny betalningsmetod för den aktuella prenumerationen.

### <a name="how-do-i-make-payments"></a>Hur gör jag betalningar?

Om du har registrerat ett kreditkort som din betalningsmetod debiteras automatiskt ditt kort efter varje faktureringsperiod. Du behöver inte göra något.

Om du är [betala med faktura](billing-how-to-pay-by-invoice.md), skicka betalningen till platsen som visas längst ned på fakturan.

### <a name="how-do-i-change-the-tax-id"></a>Hur ändrar jag skatte-ID?

Om du vill lägga till eller uppdatera skatte-ID genom att uppdatera din profil i den [Azures Kontocenter](https://account.azure.com/Profile)och välj sedan **skatt post**. Det här skatte-ID:t används för att beräkna skattebefrielse och visas på fakturan.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure reservationer](billing-save-compute-costs-reservations.md) att se om de kan spara pengar.
