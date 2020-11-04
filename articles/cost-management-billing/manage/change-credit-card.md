---
title: Ändra ditt kreditkort för Azure
description: Beskriver hur du ändrar det kreditkort som används för att betala för en Azure-prenumeration.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: banders
ms.openlocfilehash: f773c964d900c45a51eac433c3616a6e4b8978ae
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "92131147"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Lägga till eller uppdatera ett kreditkort för Azure

Det här dokumentet gäller för kunder som har registrerat sig för Azure med ett kreditkort online.

I Azure-portalen kan du ändra din standardbetalningsmetod till ett nytt kreditkort och uppdatera kreditkortsinformationen. Du måste vara [kontoadministratör](../understand/subscription-transfer.md#whoisaa) för att göra dessa ändringar.

Om du vill ta bort ett kreditkort kan du läsa [Ta bort en betalningsmetod för Azure-fakturering](delete-azure-payment-method.md).

De betalningsmetoder som kan användas i Microsoft Azure är kreditkort och check/banköverföring. Information om hur du blir godkänd för att betala med check/banköverföring finns i [Betala för Azure-prenumerationer med faktura](pay-by-invoice.md).

Med ett Microsoft-kundavtal är dina betalningsmetoder associerade med faktureringsprofiler. Läs om hur du [kontrollerar åtkomsten till ett Microsoft-kundavtal](#check-the-type-of-your-account). Om du har ett Microsoft-kundavtal kan du gå vidare till avsnittet om att [hantera kreditkort för ett Microsoft-kundavtal](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Hantera kreditkort för en Azure-prenumeration

Följande avsnitt gäller för kunder som har ett Microsoft Online Services Program-faktureringskonto. Läs om hur du [kontrollerar vilken typ av faktureringskonto du har](#check-the-type-of-your-account). Om din faktureringskontotyp är Microsoft Online Services-program är betalningsmetoderna kopplade till enskilda Azure-prenumerationer. Om det uppstår ett fel när du har lagt till kreditkortet kan du läsa avsnittet om att [kreditkortet nekas vid Azure-registrering](./troubleshoot-declined-card.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Ändra kreditkort för en prenumeration genom att lägga till ett nytt kreditkort

Du kan ändra standardkreditkortet för din Azure-prenumeration till ett nytt kreditkort eller ett tidigare sparat kreditkort i Azure-portalen. Du måste vara kontoadministratör för att ändra kreditkort. Om flera prenumerationer har samma aktiva betalningsmetod gör en ändring av den aktiva betalningsmetoden för någon av prenumerationerna även att den aktiva betalningsmetoden uppdateras för de andra.

Så här ändrar du standardkreditkortet för din prenumeration till ett nytt kreditkort:

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar sökningen](./media/change-credit-card/search.png)
1. Välj den prenumeration som du vill lägga till kreditkortet till.
1. Välj **Betalningsmetoder**.  
    ![Skärmbild som visar det valda alternativet Hantera betalningsmetoder](./media/change-credit-card/payment-methods-blade-x.png)
1. Välj ”+” i det övre vänstra hörnet om du vill lägga till ett kort. Ett kreditkortsformulär visas till höger.
1. Ange kreditkortsinformation.  
    ![Skärmbild som visar tillägg av ett nytt kort](./media/change-credit-card/sub-add-new-x.png)
1. Om du vill göra det här kortet till din aktiva betalningsmetod markerar du kryssrutan intill **Gör det här till min aktiva betalningsmetod** ovanför formuläret. Det här kortet blir det aktiva betalningsmedlet för alla prenumerationer som använder samma kort som den valda prenumerationen.
1. Välj **Nästa**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Ändra kreditkort för en prenumeration till ett tidigare sparat kreditkort

Du kan också ändra standardkreditkortet för prenumerationen till ett kreditkort som redan har sparats på ditt konto. Gör så här:

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.  
    ![Skärmbild som visar sökningen](./media/change-credit-card/search.png)
1. Välj den prenumeration som du vill lägga till kreditkortet till.
1. Välj **Betalningsmetoder**.
    ![Skärmbild som visar det valda alternativet Hantera betalningsmetoder](./media/change-credit-card/payment-methods-blade-x.png)
1. Markera kryssrutan intill det kort som du vill ange som aktiv betalningsmetod.
1. Välj **Ange som aktiv**.
    ![Skärmbild som visar valt och aktiverat kreditkort](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Ange kreditkortsinformation

Om ditt kreditkort förnyas och siffran förblir densamma uppdaterar du befintliga kreditkortsuppgifter, däribland förfallodatum. Om ditt kreditkortsnummer ändras eftersom kortet tappas bort, blir stulet eller upphör att gälla följer du stegen i avsnittet [Lägga till ett kreditkort som betalningsmetod](#addcard). Du behöver inte uppdatera CVV.

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.
    ![Skärmbild som visar sökningen](./media/change-credit-card/search.png)
1. Välj **Betalningsmetoder**.
    ![Skärmbild som visar det valda alternativet Hantera betalningsmetoder](./media/change-credit-card/payment-methods-blade-x.png)
1. Välj det kreditkort som du vill redigera. Ett kreditkortsformulär visas till höger.
    ![Skärmbild som visar valt kreditkort](./media/change-credit-card/edit-card-x.png)
1. Uppdatera kreditkortsinformationen.
1. Välj **Spara**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Hantera kreditkort för ett Microsoft-kundavtal

Följande avsnitt gäller för kunder som har ett Microsoft-kundavtal och som har registrerat sig för Azure Online med ett kreditkort. [Läs om hur du kontrollerar om du har ett Microsoft-kundavtal](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Ändra standardkreditkort

Om du har ett Microsoft-kundavtal är ditt kreditkort kopplat till en faktureringsprofil. För att kunna ändra betalningsmetoden för en faktureringsprofil måste du vara den person som registrerade sig för Azure och skapade faktureringskontot.

Om du vill ändra standardbetalningsmetoden till check/banköverföring kan du läsa [Betala för Azure-prenumerationer med faktura](pay-by-invoice.md).

Så här ändrar du kreditkort:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering och fakturering**.
1. I menyn till vänster väljer du **Faktureringsprofiler**.
1. Välj en faktureringsprofil.
1. I menyn till vänster väljer du **Betalningsmetoder**.  
   ![Skärmbild som visar betalningsmetoder i menyn](./media/change-credit-card/payment-methods-tab-mca.png)
1. I avsnittet **Standardbetalningsmetod** väljer du **Ersätt**.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Skärmbild som visar alternativet Ersätt" :::
1. I det nya området till höger väljer du antingen ett befintligt kort i listrutan eller lägger till ett nytt genom att välja den blå länken **Lägg till ny betalningsmetod**.

### <a name="edit-a-credit-card"></a>Redigera ett kreditkort

Du kan redigera kreditkortsinformation (till exempel uppdatera utgångsdatumet) i Azure-portalen. 

Så här redigerar du ett kreditkort:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Sök efter **Kostnadshantering och fakturering**.
1. I menyn till vänster väljer du **Faktureringsprofiler**.
1. Välj en faktureringsprofil.
1. I menyn till vänster väljer du **Betalningsmetoder**.  
   ![Skärmbild som visar betalningsmetoder i menyn](./media/change-credit-card/payment-methods-tab-mca.png)
1. I avsnittet **Dina kreditkort** letar du upp det kreditkort som du vill redigera.
1. Klicka på ellipsen (`...`) i slutet av raden.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Skärmbild på ellipsen" :::
1. Om du vill redigera kreditkortsinformationen väljer du **Redigera** på snabbmenyn.

## <a name="troubleshooting"></a>Felsökning

Azure har inte stöd för virtuella eller förbetalda kort. Om du får felmeddelanden när du lägger till eller uppdaterar ett giltigt kreditkort kan du prova att öppna webbläsaren i privat läge.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

I följande avsnitt besvaras vanliga frågor om att ändra kreditkortsinformation.

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
- Om du vill ta bort ett kreditkort kan du läsa [Ta bort en betalningsmetod för Azure-fakturering](delete-azure-payment-method.md).