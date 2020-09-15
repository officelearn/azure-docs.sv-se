---
title: Ändra ditt kreditkort för Azure
description: Beskriver hur du ändrar det kreditkort som används för att betala för en Azure-prenumeration.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: c7622c4916b186a39aad9dafb54a1e2404458794
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569364"
---
# <a name="add-update-or-remove-a-credit-card-for-azure"></a>Lägga till, uppdatera eller ta bort ett kreditkort för Azure

Det här dokumentet gäller för kunder som har registrerat sig för Azure med ett kreditkort online.

I Azure-portalen kan du ändra din standardbetalningsmetod till ett nytt kreditkort, uppdatera kreditkortsinformationen och ta bort kreditkort som du inte använder. Du måste vara [kontoadministratör](billing-subscription-transfer.md#whoisaa) för att göra dessa ändringar.

De betalningsmetoder som kan användas i Microsoft Azure är kreditkort och check/banköverföring. Information om hur du blir godkänd för att betala med check/banköverföring finns i [Betala för Azure-prenumerationer med faktura](pay-by-invoice.md).

Om du har ett Microsoft-kundavtal är dina betalningsmetoder associerade med faktureringsprofiler. Läs om hur du [kontrollerar åtkomsten till ett Microsoft-kundavtal](#check-the-type-of-your-account). Om du har ett Microsoft-kundavtal kan du gå vidare till avsnittet om att [hantera kreditkort för ett Microsoft-kundavtal](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Hantera kreditkort för en Azure-prenumeration

Följande avsnitt gäller för kunder som har ett Microsoft Online Services Program-faktureringskonto. Läs om hur du [kontrollerar vilken typ av faktureringskonto du har](#check-the-type-of-your-account). Om din faktureringskontotyp är Microsoft Online Services-program är betalningsmetoderna kopplade till enskilda Azure-prenumerationer. Om det uppstår ett fel när du har lagt till kreditkortet kan du läsa avsnittet om att [kreditkortet nekas vid Azure-registrering](../../billing/billing-credit-card-fails-during-azure-sign-up.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Ändra kreditkort för en prenumeration genom att lägga till ett nytt kreditkort

Du kan ändra standardkreditkortet för din Azure-prenumeration till ett nytt kreditkort eller ett tidigare sparat kreditkort i Azure-portalen. Du måste vara kontoadministratör för att ändra kreditkort. Om fler än en av dina prenumerationer har samma aktiva betalningsmetod gör en ändring av den aktiva betalningsmetoden för någon av dessa prenumerationer även att den aktiva betalningsmetoden uppdateras för de andra.


Så här ändrar du standardkreditkortet för din prenumeration till ett nytt kreditkort:

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar sökningen](./media/change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kreditkortet till.
1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/change-credit-card/payment-methods-blade-x.png)

1. Välj ”+” i det övre vänstra hörnet om du vill lägga till ett kort. Ett kreditkortsformulär visas till höger.
1. Ange kreditkortsinformation.

    ![Skärmbild som visar hur du lägger till ett nytt kort.](./media/change-credit-card/sub-add-new-x.png)

1. Om du vill göra det här kortet till din aktiva betalningsmetod markerar du kryssrutan intill **Gör det här till min aktiva betalningsmetod** ovanför formuläret. Det här kortet blir det aktiva betalningsmedlet för alla prenumerationer som använder samma kort som den valda prenumerationen.

1. Välj **Nästa**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Ändra kreditkort för en prenumeration till ett tidigare sparat kreditkort

Du kan också ändra standardkreditkortet för prenumerationen till ett kreditkort som redan har sparats på ditt konto. Gör så här:

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar sökningen](./media/change-credit-card/search.png)

1. Välj den prenumeration som du vill lägga till kreditkortet till.
1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan intill det kort som du vill ange som aktiv betalningsmetod.
1. Klicka på **Ange som aktiv**.
    ![Skärmbild som visar valt och aktiverat kreditkort.](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Ange kreditkortsinformation

Om ditt kreditkort förnyas och siffran förblir densamma uppdaterar du befintliga kreditkortsuppgifter, däribland förfallodatum. Om ditt kreditkortsnummer ändras eftersom kortet tappas bort, blir stulet eller upphör att gälla följer du stegen i avsnittet [Lägga till ett kreditkort som betalningsmetod](#addcard). Du behöver inte uppdatera CVV.

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.

    ![Skärmbild som visar sökningen](./media/change-credit-card/search.png)

1. Välj **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/change-credit-card/payment-methods-blade-x.png)

1. Klicka på det kreditkort som du vill redigera. Ett kreditkortsformulär visas till höger.

    ![Skärmbild som visar valt kreditkort.](./media/change-credit-card/edit-card-x.png)

1. Uppdatera kreditkortsinformationen.
1. Välj **Spara**.

### <a name="delete-a-credit-card-from-the-account"></a>Ta bort ett kreditkort från kontot

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Välj **Kostnadshantering + fakturering** till vänster på sidan.

    ![Skärmbild som visar sökningen](./media/change-credit-card/search.png)

1. Under **Fakturering** väljer du **Betalningsmetoder**.

    ![Skärmbild som visar valt alternativ Hantera betalningsmetoder.](./media/change-credit-card/payment-methods-blade-x.png)

1. Markera kryssrutan intill det kort som du vill ta bort.
1. Klicka på **Ta bort**.

Om ditt kreditkort är den aktiva betalningsmetoden för någon av dina Microsoft-prenumerationer kan du inte ta bort det från ditt Azure-konto. Ändra den aktiva betalningsmetoden för alla prenumerationer som är kopplade till det här kreditkortet och försök igen

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Hantera kreditkort för ett Microsoft-kundavtal

Följande avsnitt gäller för kunder som har ett Microsoft-kundavtal och som har registrerat sig för Azure Online med ett kreditkort. [Läs om hur du kontrollerar om du har ett Microsoft-kundavtal](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Ändra standardkreditkort

Om du har ett Microsoft-kundavtal är ditt kreditkort kopplat till en faktureringsprofil. För att kunna ändra betalningsmetoden för en faktureringsprofil måste du vara den person som registrerade sig för Azure och skapade faktureringskontot.

Om du vill ändra standardbetalningsmetoden till check/banköverföring kan du läsa [Betala för Azure-prenumerationer med faktura](pay-by-invoice.md).

Så här ändrar du kreditkort:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering och fakturering**.
1. I menyn till vänster klickar du på **Faktureringsprofiler**.
1. Välj en faktureringsprofil.
1. I menyn till vänster väljer du **Betalningsmetoder**.

   ![Skärmbild som visar betalningsmetoder i menyn](./media/change-credit-card/payment-methods-tab-mca.png)

1. I avsnittet **Standardbetalningsmetod** klickar du på **Ändra**.

    ![Skärmbild som visar knappen Ändra](./media/change-credit-card/change-payment-method-mca.png)

1. På det nya bladet till höger väljer du antingen ett befintligt kort i listrutan eller lägger till ett nytt genom att klicka på länken ”Lägg till ny betalningsmetod”.

### <a name="edit-or-delete-a-credit-card"></a>Redigera eller ta bort ett kreditkort

Du kan redigera kreditkortsinformation (till exempel uppdatera förfallodatumet) och ta bort kreditkort från ditt konto i Azure-portalen. Du kan inte ta bort ett kreditkort om det är kopplat till en Azure-prenumeration eller faktureringsprofil. Om kreditkortet är kopplat till en inaktiverad Azure-prenumeration måste du vänta tills prenumerationen har tagits bort (30-90 dagar efter annulleringen) för att kunna ta bort kreditkortet.

Så här redigerar eller tar du bort ett kreditkort:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering och fakturering**.
1. I menyn till vänster klickar du på **Faktureringsprofiler**.
1. Välj en faktureringsprofil.
1. I menyn till vänster väljer du **Betalningsmetoder**.

   ![Skärmbild som visar betalningsmetoder i menyn](./media/change-credit-card/payment-methods-tab-mca.png)

1. I avsnittet **Dina kreditkort**  letar du reda på kreditkortet du vill redigera eller ta bort.
1. Klicka på ellipsen (`...`) i slutet av raden.

    ![Skärmbild på ellipsen](./media/change-credit-card/edit-delete-credit-card-mca.png)

1. Om du vill redigera kreditkortsinformationen väljer du **Redigera** på snabbmenyn.
1. Om du vill ta bort kreditkortet väljer du **Ta bort** på snabbmenyn.

## <a name="troubleshooting"></a>Felsökning

Du kan inte använda virtuella eller förbetalda kort. Om du får felmeddelanden när du lägger till eller uppdaterar ett giltigt kreditkort kan du prova att öppna webbläsaren i privat läge.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

I följande avsnitt besvaras vanliga frågor om att ändra kreditkortsinformation.

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Min prenumeration är inaktiverad. Varför kan jag inte ta bort mitt kreditkort nu?

När din prenumeration har inaktiverats eller avbrutits väntar vi 90 dagar innan prenumerationen tas bort permanent. Vi sparar din betalningsmetod under kvarhållningsperioden ifall du vill återaktivera prenumerationen. Efter det tas prenumerationen bort permanent.

Om du behöver ta bort ditt kreditkort innan kvarhållningsperioden på 90 dagar har passerat kan du [återaktivera prenumerationen](subscription-disabled.md). Om du inte kan återaktivera bör du [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Varför får jag felet ”Din inloggningssession har upphört. Klicka här om du vill logga in igen”?

Om du fortfarande får det här felmeddelandet även efter att du redan har loggat ut och in igen kan du försöka igen med en privat webbläsarsession.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hur använder jag olika kort för varje prenumeration som jag har?

Om dina prenumerationer redan använder samma kort går det tyvärr inte att separera dem till att använda olika kort. När du registrerar dig för en ny prenumeration kan du dock välja att använda en ny betalningsmetod för den prenumerationen.

### <a name="how-do-i-make-payments"></a>Hur gör jag betalningar?

Om du konfigurerar ett kreditkort som betalningsmetod debiterar vi kortet automatiskt efter varje faktureringsperiod. Du behöver inte göra någonting.

Om du [betalar med faktura](pay-by-invoice.md) skickar du betalningen till den plats som anges längst ned på fakturan.

### <a name="how-do-i-change-the-tax-id"></a>Hur ändrar jag organisationsnummer?

Om du vill lägga till eller uppdatera organisationsnummer uppdaterar du din profil i [Azure-portalen](https://portal.azure.com) och väljer **Skattepost**. Det här skatte-ID:t används för att beräkna skattebefrielse och visas på fakturan.

## <a name="check-the-type-of-your-account"></a>Kontrollera vilken typ av konto du har

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure-reservationer](../reservations/save-compute-costs-reservations.md) för att se om de kan minska dina utgifter.
