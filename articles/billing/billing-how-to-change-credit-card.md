---
title: Ändra ditt kreditkort för Azure
description: Beskriver hur du ändrar det kreditkort som används för att betala för en Azure-prenumeration.
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
ms.openlocfilehash: e652237e6faa705aa3ea09e7cf80c4eb692acc98
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375594"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Lägga till, uppdatera eller ta bort ett kreditkort för Azure

Det här dokumentet gäller för kunder som har registrerat sig för Azure med ett kreditkort online.

I Azure-portalen kan du lägga till ett nytt kreditkort, uppdatera ett befintligt kreditkort eller ta bort ett kreditkort som du inte använder. Du måste vara [kontoadministratör](billing-subscription-transfer.md#whoisaa) för att göra dessa ändringar.

Om du har ett [Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement) är dina betalningsmetoder associerade med faktureringsprofiler. Lär dig hur du [ändrar standardbetalningsmetoden för en faktureringsprofil](#change-payment-method-for-a-billing-profile). Endast den användare som har registrerat sig för Azure kan uppdatera betalningsmetoden.

**Vill du byta till att betala med faktura (check/banköverföring)?** Se [Betala för Azure-prenumerationer med faktura](billing-how-to-pay-by-invoice.md).

<a id="addcard"></a>

## <a name="add-a-new-credit-card-to-an-azure-subscription"></a>Lägga till ett nytt kredit i en Azure-prenumeration

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar sökningen](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kreditkortet till.
1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Välj ”+” i det övre vänstra hörnet om du vill lägga till ett kort. Ett kreditkortsformulär visas till höger.
1. Ange kreditkortsinformation.

    ![Skärmbild som visar hur du lägger till ett nytt kort.](./media/billing-how-to-change-credit-card/sub-add-new-x.png)

1. Om du vill göra det här kortet till din aktiva betalningsmetod markerar du kryssrutan intill **Gör det här till min aktiva betalningsmetod** ovanför formuläret. Det här kortet blir det aktiva betalningsmedlet för alla prenumerationer som använder samma kort som den valda prenumerationen.

1. Välj **Nästa**.

Om det uppstår ett fel när du har lagt till kreditkortet kan du läsa avsnittet om att [kreditkortet nekas vid Azure-registrering](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-card"></a>Uppdatera befintligt kreditkort

Om ditt kreditkort förnyas och siffran förblir densamma uppdaterar du befintliga kreditkortsuppgifter, däribland förfallodatum. Om ditt kreditkortsnummer ändras eftersom kortet tappas bort, blir stulet eller upphör att gälla följer du stegen i avsnittet [Lägga till ett kreditkort som betalningsmetod](#addcard). Du behöver inte uppdatera CVV.

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar sökningen](./media/billing-how-to-change-credit-card/search.png)

1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Klicka på det kreditkort som du vill redigera. Ett kreditkortsformulär visas till höger.

    ![Skärmbild som visar valt kreditkort.](./media/billing-how-to-change-credit-card/edit-card-x.png)

1. Uppdatera kreditkortsinformationen.
1. Välj **Spara**.

## <a name="use-a-different-credit-card"></a>Använda ett annat kreditkort

Om fler än en av dina prenumerationer har samma aktiva betalningsmetod gör en ändring av den aktiva betalningsmetoden för någon av dessa prenumerationer även att den aktiva betalningsmetoden uppdateras för de andra.

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar sökningen](./media/billing-how-to-change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kreditkortet till.
1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan intill det kort som du vill ange som aktiv betalningsmetod.
1. Klicka på **Ange som aktiv**.
    ![Skärmbild som visar valt och aktiverat kreditkort.](./media/billing-how-to-change-credit-card/sub-change-active-x.png)

## <a name="remove-a-credit-card-from-the-account"></a>Ta bort ett kreditkort från kontot

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Välj **Kostnadshantering + fakturering** till vänster på sidan.

    ![Skärmbild som visar sökningen](./media/billing-how-to-change-credit-card/search.png)

1. Under **Fakturering** väljer du **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/billing-how-to-change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan intill det kort som du vill ta bort.
1. Klicka på **Ta bort**.

Om ditt kreditkort är den aktiva betalningsmetoden för någon av dina Microsoft-prenumerationer kan du inte ta bort det från ditt Azure-konto. Ändra den aktiva betalningsmetoden för alla prenumerationer som är kopplade till det här kreditkortet och försök igen

## <a name="change-payment-method-for-a-billing-profile"></a>Ändra betalningsmetod för en faktureringsprofil

För att kunna ändra betalningsmetoden för en faktureringsprofil måste du vara den person som registrerade sig för Azure.

Om du vill byta standardbetalningsmetoden till check/banköverföring lär du dig att [byta en faktureringsprofil till check/banköverföring](billing-how-to-pay-by-invoice.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering och fakturering**.
1. I menyn till vänster klickar du på **Faktureringsprofiler**.

    ![skärmbild som visar faktureringsprofil i menyn](./media/billing-how-to-change-credit-card/billing-profile.png)

1. Välj en faktureringsprofil.
1. I menyn till vänster väljer du **Betalningsmetoder**.

   ![Skärmbild som visar betalningsmetoder i menyn](./media/billing-how-to-change-credit-card/billing-profile-payment-methods.png)

1. Ovanför standardbetalningsmetoden klickar du på **Ändra**.

    ![Skärmbild som visar knappen Ändra](./media/billing-how-to-change-credit-card/customer-led-switch-credit-card.png)

1. Välj ett befintligt kort eller lägg till ett nytt.

## <a name="troubleshooting"></a>Felsökning
Du kan inte använda virtuella eller förbetalda kort. Om du får felmeddelanden när du lägger till eller uppdaterar ett giltigt kreditkort kan du prova att öppna webbläsaren i privat läge.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
I följande avsnitt besvaras vanliga frågor om att ändra kreditkortsinformation.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Min prenumeration är inaktiverad. Varför kan jag inte ta bort mitt kreditkort nu?

När din prenumeration har inaktiverats eller avbrutits väntar vi 90 dagar innan prenumerationen tas bort permanent. Vi sparar din betalningsmetod under kvarhållningsperioden ifall du vill återaktivera prenumerationen. Efter det tas prenumerationen bort permanent.

Om du behöver ta bort ditt kreditkort innan kvarhållningsperioden på 90 dagar har passerat kan du [återaktivera prenumerationen](billing-subscription-become-disable.md). Om du inte kan återaktivera bör du [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Varför får jag felet ”Din inloggningssession har upphört. Klicka här om du vill logga in igen”?

Om du fortfarande får det här felmeddelandet även efter att du redan har loggat ut och in igen kan du försöka igen med en privat webbläsarsession.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hur använder jag olika kort för varje prenumeration som jag har?

Om dina prenumerationer redan använder samma kort går det tyvärr inte att separera dem till att använda olika kort. När du registrerar dig för en ny prenumeration kan du dock välja att använda en ny betalningsmetod för den prenumerationen.

### <a name="how-do-i-make-payments"></a>Hur gör jag betalningar?

Om du konfigurerar ett kreditkort som betalningsmetod debiterar vi kortet automatiskt efter varje faktureringsperiod. Du behöver inte göra någonting.

Om du [betalar med faktura](billing-how-to-pay-by-invoice.md) skickar du betalningen till den plats som anges längst ned på fakturan.

### <a name="how-do-i-change-the-tax-id"></a>Hur ändrar jag organisationsnummer?

Om du vill lägga till eller uppdatera organisationsnummer uppdaterar du din profil i [Azure-kontocentret](https://account.azure.com/Profile) och väljer **Skattepost**. Det här skatte-ID:t används för att beräkna skattebefrielse och visas på fakturan.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure-reservationer](billing-save-compute-costs-reservations.md) för att se om de kan minska dina utgifter.
