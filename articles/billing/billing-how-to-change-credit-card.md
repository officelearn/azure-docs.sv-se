---
title: Ändra ditt kredit kort för Azure
description: Beskriver hur du ändrar det kredit kort som används för att betala för en Azure-prenumeration.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: banders
ms.openlocfilehash: 7719ae83525883a6d3f014dbb99877b7319f2ccd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383657"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Lägga till, uppdatera eller ta bort ett kredit kort för Azure

I Azure Portal kan du lägga till ett nytt kredit kort, uppdatera ett befintligt kredit kort eller ta bort ett kredit kort som du inte använder. Du måste vara [konto administratör](billing-subscription-transfer.md#whoisaa) för att göra dessa ändringar.

Om du har ett [kund avtal för Microsoft](#check-access-to-a-microsoft-customer-agreement)är dina betalnings metoder kopplade till fakturerings profiler. Lär dig hur du [ändrar standard betalnings metoden för en fakturerings profil](#change-payment-method-for-a-billing-profile). Endast den användare som registrerade sig för Azure kan uppdatera betalnings metoden.

**Vill du byta till betala per faktura (kontroll/överföring)?** Se [betala för Azure-prenumerationer per faktura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Lägg till ett nytt kredit kort i en Azure-prenumeration

1. Logga in på [Azure Portal](https://portal.azure.com) som konto administratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärm bild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kredit kortet till.
1. Välj **Betalningsmetoder**.

    ![Skärm bild som visar alternativet hantera betalnings metoder valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Välj ”+” i det övre vänstra hörnet om du vill lägga till ett kort. Ett kreditkortsformulär visas till höger.
1. Ange kreditkorts information.

    ![Skärm bild som visar hur du lägger till ett nytt kort.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Om du vill göra det här kortet till din aktiva betalnings metod markerar du kryss rutan bredvid **gör denna aktiva betalnings metod** ovanför formuläret. Det här kortet blir det aktiva betalningsmedlet för alla prenumerationer som använder samma kort som den valda prenumerationen.

1. Välj **Nästa**.

Om du får ett fel meddelande när du har lagt till kredit kortet, se [kredit kort avböjt vid Azure-registrering](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Uppdatera befintligt kredit kort

Om kredit kortet förnyas och värdet förblir detsamma uppdaterar du befintliga kreditkorts uppgifter som förfallo datum. Om ditt kreditkorts nummer ändras eftersom kortet tappas bort, blir stulen eller upphör att gälla följer du stegen i avsnittet [Lägg till ett kredit kort som betalnings metod](#addcard) . Du behöver inte uppdatera CVV.

1. Logga in på [Azure Portal](https://portal.azure.com) som konto administratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärm bild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj **Betalningsmetoder**.

    ![Skärm bild som visar alternativet hantera betalnings metoder valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klicka på det kredit kort som du vill redigera. Ett kreditkortsformulär visas till höger.

    ![Skärm bild som visar kredit kort valt.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Uppdatera kreditkorts informationen.
1. Välj **Spara**.

## <a name="use-a-different-credit-card"></a>Använd ett annat kredit kort

Om fler än en av dina prenumerationer har samma aktiva betalnings metod, uppdaterar även den aktiva betalnings metoden för någon av dessa prenumerationer den aktiva betalnings metoden för de andra.

1. Logga in på [Azure Portal](https://portal.azure.com) som konto administratör.
1. Sök efter **Cost Management + fakturering**.

    ![Skärm bild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kredit kortet till.
1. Välj **Betalningsmetoder**.

    ![Skärm bild som visar alternativet hantera betalnings metoder valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera kryss rutan bredvid kortet som du vill göra till den aktiva betalnings metoden.
1. Klicka på **Ange aktiv**.
    ![Skärm bild som visar kredit kortet valt och aktiverat.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Ta bort ett kredit kort från kontot

1. Logga in på [Azure Portal](https://portal.azure.com) som konto administratör.
1. Välj **Cost Management + fakturering** till vänster på sidan.

    ![Skärm bild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Under **fakturering**väljer du **betalnings sätt**.

    ![Skärm bild som visar alternativet hantera betalnings metoder valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera rutan bredvid kortet som du vill ta bort.
1. Klicka på **Ta bort**.

Om ditt kredit kort är den aktiva betalnings metoden för någon av dina Microsoft-prenumerationer kan du inte ta bort den från ditt Azure-konto. Ändra den aktiva betalnings metoden för alla prenumerationer som är kopplade till det här kredit kortet och försök igen
<!-- # Add, update, or remove a credit card for Azure

In the Account Center, you can add a new credit card, update an existing credit card, or delete a credit card that you don't use. You must be an [Account Administrator](billing-subscription-transfer.md#whoisaa) to make these changes.

**Want to switch to pay by invoice?** See [Pay for Azure subscriptions by invoice](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## Add a new credit card

1. Sign in to the [Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select a subscription.
1. On the right side of the page, select **Manage payment methods**.

    ![Screenshot that shows Manage payment methods option selected.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Select “+” to add a card.

    ![Screenshot that shows the edit option next to the payment method.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Enter credit card details.
1. Select **Save**.

If you get an error after you add the credit card, see [Credit card declined at Azure sign-up](billing-credit-card-fails-during-azure-sign-up.md).

## Update existing credit card

If your credit card gets renewed and the number remains the same, update the existing credit card details like the expiration date. If your credit card number changes because the card is lost, stolen, or expired, follow the steps in the [Add a credit card as a payment method](#addcard) section. You don't need to update the CVV.

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. Select **Manage payment methods**.
1. Select **Edit** next to the card you want to update.
1. Update the credit card details.
1. Select **Save**.

## Use a different credit card for the Azure subscription

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
1. On the right side of the page, select **Manage payment methods**.
1. Click **Use Instead** next to the card that you want to use. This also updates any other subscriptions currently associated with this card.

## Remove a credit card from the account

1. Sign in to the [Azure Account Center](https://account.windowsazure.com/Subscriptions) as the [Account Administrator](billing-subscription-transfer.md#whoisaa).
1. Select the subscription that's linked to the card.
3. On the right side of the page, select **Manage payment methods**.
4. Click **Delete** for the credit card that you want to delete.

If your credit card is associated with other active Microsoft subscriptions, you can't remove it from your Azure account. Remove the credit card from all active subscriptions that you have with Microsoft and try again. -->

## <a name="change-payment-method-for-a-billing-profile"></a>Ändra betalnings metod för en fakturerings profil

Om du vill ändra betalnings metod för en fakturerings profil måste du vara den person som registrerade dig för Azure.

Om du vill byta standard betalnings metod för att kontrol lera/byta överföring kan du läsa om hur du [växlar en fakturerings profil för att kontrol lera/byta överföring](billing-how-to-pay-by-invoice.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök på **Cost Management + fakturering**.
1. Klicka på **fakturerings profiler**i menyn till vänster.

    ![skärm bild som visar fakturerings profil i menyn](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Välj en fakturerings profil.
1. I menyn till vänster väljer du **betalnings sätt**.

   ![Skärm bild som visar betalnings metoder på menyn](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Klicka på **ändra**ovanför standard betalnings metoden.

    ![Skärm bild som visar knappen Ändra](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Välj ett befintligt kort eller Lägg till ett nytt.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
I följande avsnitt besvaras vanliga frågor om att ändra kreditkorts information.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Min prenumeration är inaktive rad. Varför kan jag inte ta bort mitt kredit kort nu?

När din prenumeration har inaktiverats eller avbrutits väntar vi 90 dagar innan prenumerationen tas bort permanent. Vi behåller din betalnings metod på fil under kvarhållningsperioden om du vill återaktivera prenumerationen. Efter det tas prenumerationen bort permanent.

Om du behöver ta bort kredit kortet innan lagrings perioden på 90 dagar upphör, [återaktivera du din prenumeration](billing-subscription-become-disable.md). Om du inte kan återaktivera kan du [kontakta Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)-supporten.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Varför kan jag fortsätta "din inloggningssession har upphört att gälla. Logga in igen genom att klicka här

Om du fortfarande får det här fel meddelandet även om du redan har loggat ut och tillbaka i, kan du försöka igen med en privat webbläsarsession.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hur gör jag för att använda ett annat kort för varje prenumeration?

Om dina prenumerationer redan använder samma kort går det tyvärr inte att separera dem till att använda olika kort. När du registrerar dig för en ny prenumeration kan du dock välja att använda en ny betalnings metod för den prenumerationen.

### <a name="how-do-i-make-payments"></a>Hur gör jag för att gör du betalningar?

Om du skapar ett kredit kort som betalnings metod debiterar vi kortet automatiskt efter varje fakturerings period. Du behöver inte göra något.

Om du [betalar per faktura](billing-how-to-pay-by-invoice.md)skickar du betalningen till den plats som anges längst ned på fakturan.

### <a name="how-do-i-change-the-tax-id"></a>Hur gör jag för att ändra skatte-ID?

Om du vill lägga till eller uppdatera skatte-ID uppdaterar du din profil i [Azure-kontocenter](https://account.azure.com/Profile)och väljer sedan **moms post**. Det här skatte-ID:t används för att beräkna skattebefrielse och visas på fakturan.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrol lera åtkomsten till ett Microsofts kund avtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en support förfrågan](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure-reservationer](billing-save-compute-costs-reservations.md) för att se om de kan spara pengar.
