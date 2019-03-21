---
title: Ändra ditt kreditkort för Azure | Microsoft Docs
description: Beskriver hur du så här ändrar du kreditkortet som används för att betala för en Azure-prenumeration
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/13/2019
ms.author: banders
ms.openlocfilehash: b910cb3061b1451ea80b9843e2aa4047a784548f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876876"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Lägga till, uppdatera eller ta bort ett kreditkort eller bankkort kort för Azure

I Azure-portalen kan du lägga till ett nytt kreditkort, uppdatera ett befintligt kreditkort eller ta bort ett kreditkort som du inte använder. Du måste vara en [kontoadministratör](billing-subscription-transfer.md#whoisaa) du gör dessa ändringar.

<!-- If your Angola, Belize, Algeria, Vietnam, or Virgin Islands. -->

**Vill du istället för att betala med faktura?** Se [betala för Azure-prenumerationer med faktura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card-to-an-azure-subscription"></a>Lägga till ett nytt kreditkort eller bankkort kort i en Azure-prenumeration

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök på **Cost Management + fakturering**.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kredit- eller betalkort kortet till.
1. Välj **betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. I det övre vänstra hörnet, väljer ”+” att lägga till ett kort. Ett kreditkort formulär visas till höger.
1. Ange kredit- eller debetkortsinformation.

    ![Skärmbild som visar att lägga till ett nytt kort.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Att göra det här kortet din aktiva betalningsmetod, markerar du kryssrutan bredvid **gör det här till min aktiva betalningsmetod** ovan i formuläret. Det här kortet blir aktiv betalningsmedlet för alla prenumerationer som använder samma kort som den valda prenumerationen.

1. Välj **Nästa**.

Om du får ett felmeddelande när du lägger till kreditkortet [kreditkort avvisade på Azure-registrering](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Uppdatera befintliga kredit- eller betalkort

Om ditt kreditkort hämtar förnyas och talet förblir oförändrat, uppdatera den befintliga informationen kreditkort som förfallodatumet. Om ändringarna för kreditkort eftersom kortet går förlorad, stulits eller upphört att gälla, följer du stegen i den [lägga till ett kreditkort som betalningssätt](#addcard) avsnittet. Du behöver inte uppdatera CVV.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök på **Cost Management + fakturering**.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj **betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klicka på kredit eller debitera kortet som du vill redigera. Ett kreditkort formulär visas till höger.

    ![Skärmbild som visar kreditkort eller bankkort kortet markerat.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Uppdatera kortinformationen kreditkort eller bankkort.
1. Välj **Spara**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Använd ett annat kreditkort för Azure-prenumeration

Om mer än en av dina prenumerationer har samma aktiv betalningsmetod, uppdateras ändra den aktiva betalningsmetoden på någon av de här prenumerationerna dessutom aktiv betalningsmetod på de andra.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök på **Cost Management + fakturering**.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kredit- eller betalkort kortet till.
1. Välj **betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan bredvid det kortet som du vill göra den aktiv betalningsmetoden.
1. Klicka på **Ställ in som aktiv**.
    ![Skärmbild som visar kreditkort eller bankkort valt och Ställ in som aktiv.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Ta bort ett kreditkort eller bankkort kort från kontot

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Välj **kostnadshantering + fakturering** till vänster på sidan.

    ![Skärmbild som visar sökning](./media/billing-how-to-change-credit-card/search.png)

1. Under **fakturering**väljer **betalningsmetoder**.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan bredvid det kortet som du vill ta bort.
1. Klicka på **Ta bort**.

Om ditt kreditkort är aktiv betalningsmetod för någon av dina Microsoft-prenumerationer kan du inte ta bort det från ditt Azure-konto. Ändra den aktiva betalningsmetoden för alla prenumerationer som är länkad till det här kortet med kreditkort eller bankkort och försök igen
<!-- # Add, update, or remove a credit or debit card for Azure

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
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Min prenumeration har inaktiverats. Varför kan jag ta bort mitt kreditkort nu?

När prenumerationen har inaktiverats eller har avbrutits, vänta vi 90 dagar innan din prenumeration tas bort permanent. Vi håller din betalningsmetod på fil under kvarhållningsperioden om du vill återaktivera prenumerationen. Efter det raderas prenumerationen.

Om du vill ta bort ditt kreditkort eller bankkort kort innan 90-dagars kvarhållning har löpt ut, [återaktivera din prenumeration](billing-subscription-become-disable.md). Om du inte kan aktivera [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Varför får jag ”din inloggningssession har upphört att gälla. Klicka här för att logga in igen ”?

Om du får detta felmeddelande även om du redan har loggat och tillbaka i, försök igen med en privat surfning.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hur använder jag ett nytt kort för varje prenumeration som jag har?

Tyvärr om dina prenumerationer redan använder samma kort, går det inte att separera dem för att använda olika kort. När du registrerar dig för en ny prenumeration kan välja du dock att använda en ny betalningsmetod för den aktuella prenumerationen.

### <a name="how-do-i-make-payments"></a>Hur gör jag betalningar?

Om du har konfigurerat ett kreditkort eller bankkort krävs en som din betalningsmetod debiteras automatiskt ditt kort efter varje faktureringsperiod. Du behöver inte göra något.

Om du är [betala med faktura](billing-how-to-pay-by-invoice.md), skicka betalningen till platsen som visas längst ned på fakturan.

### <a name="how-do-i-make-a-one-time-payment"></a>Hur gör jag en engångsbetalning?

Tyvärr stöder Azure för närvarande inte enstaka betalningar för kreditkort eller bankkort kort. 

### <a name="how-do-i-change-the-tax-id"></a>Hur ändrar jag skatte-ID?

Om du vill lägga till eller uppdatera skatte-ID, besök [ **profil** i Azure-Kontocenter](https://account.azure.com/Profile)och välj sedan **skatt post**. Det här skatte-ID:t används för att beräkna skattebefrielse och visas på fakturan.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
