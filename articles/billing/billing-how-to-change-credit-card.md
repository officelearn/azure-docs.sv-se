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
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 69c24e08ce2434c39b8bb67cb53173f9ceaee51b
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581451"
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Lägga till, uppdatera eller ta bort ett kreditkort eller bankkort kort för Azure

I Kontocenter, kan du lägga till ett nytt kreditkort, uppdatera ett befintligt kreditkort eller ta bort ett kreditkort som du inte använder. Du måste vara en [kontoadministratör](billing-subscription-transfer.md#whoisaa) du gör dessa ändringar.

**Vill du istället för att betala med faktura?** Se [betala för Azure-prenumerationer med faktura](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Lägg till ett nytt kreditkort eller bankkort-kort

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj en prenumeration.
1. Välj **Hantera betalningssätt** till höger på sidan.

    ![Skärmbild som visar hantera metoder betalningsalternativ har valt.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Välj ”+” för att lägga till ett kort.

    ![Skärmbild som visar alternativet Redigera bredvid betalningsmetoden.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Ange kredit- eller debetkortsinformation.
1. Välj **Spara**. 

Om du får ett felmeddelande när du lägger till kreditkortet [kreditkort avvisade på Azure-registrering](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Uppdatera befintliga kredit- eller betalkort

Om ditt kreditkort hämtar förnyas och hur många densamma, uppdatera den befintliga informationen kreditkort som förfallodatumet. Om ändringarna för kreditkort eftersom kortet går förlorad, stulits eller upphört att gälla, följer du stegen i den [lägga till ett kreditkort som betalningssätt](#addcard) avsnittet. Du behöver inte uppdatera CVV.

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj den prenumeration som är länkad till kortet.
1. Välj **hantera betalningsmetoder**.
1. Välj **redigera** bredvid det kortet som du vill uppdatera.
1. Uppdatera kortinformationen kreditkort eller bankkort.
1. Välj **Spara**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Använd ett annat kreditkort för Azure-prenumeration

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj den prenumeration som är länkad till kortet.
1. Välj **Hantera betalningssätt** till höger på sidan.
1. Klicka på **Använd i stället** bredvid det kortet som du vill använda. Det här uppdaterar även andra prenumerationer som för närvarande är associerade med det här kortet. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Ta bort ett kreditkort eller bankkort kort från kontot

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj den prenumeration som är länkad till kortet.
3. Välj **Hantera betalningssätt** till höger på sidan.
4. Klicka på **ta bort** för kreditkort som du vill ta bort.

Om ditt kreditkort är associerad med andra aktiva prenumerationer för Microsoft, du kan inte ta bort det från ditt Azure-konto. Ta bort kreditkortet från alla aktiva prenumerationer som du har med Microsoft och försök igen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Min prenumeration har inaktiverats. Varför kan jag ta bort mitt kreditkort nu?

När prenumerationen har inaktiverats eller har avbrutits, vänta vi 90 dagar innan din prenumeration tas bort permanent. Vi håller din betalningsmetod på fil under kvarhållningsperioden om du vill återaktivera prenumerationen. Efter det är helt ta bort prenumerationen.

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

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
