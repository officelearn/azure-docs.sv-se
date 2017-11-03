---
title: "Ändra ditt kreditkort för Azure | Microsoft Docs"
description: "Beskriver hur du så här ändrar du det kreditkort som används för att betala för en Azure-prenumeration"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 03764377b3ea0e17d4a192a7e05bb495ec56f331
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Lägga till, uppdatera eller ta bort ett kredit- eller kort för Azure

Mitt konto kan du lägga till ett nytt kreditkort, uppdatera ett befintligt kreditkort eller ta bort ett kreditkort som du inte använder. Du måste vara [kontoadministratör](billing-subscription-transfer.md#whoisaa) att göra dessa ändringar.

**Vill du istället för att betala fakturan?** Se [betala för Azure-prenumerationer per faktura](billing-how-to-pay-by-invoice.md).
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>Lägg till ett nytt kredit- eller -kort

1. Logga in på den [Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj en prenumeration.
1. Välj **Hantera betalningssätt** till höger på sidan.

    ![Skärmbild som visar hantera metoder betalningsalternativ markerad.](./media/billing-how-to-change-credit-card/changesub_new.png)
1. Välj ”+” för att lägga till ett kort.

    ![Skärmbild som visar alternativet Redigera bredvid betalningsmetoden.](./media/billing-how-to-change-credit-card/editcard_new.png)
1. Ange kredit eller debitera kortinformation.
1. Välj **Spara**. 

Om du får ett felmeddelande när du lägger till kreditkortet [kreditkort nekades på Azure anmälan](billing-credit-card-fails-during-azure-sign-up.md).

## <a name="update-existing-credit-or-debit-card"></a>Uppdatera befintliga kredit- eller betalkort

Om ditt kreditkort hämtar förnyas och talet förblir detsamma, uppdatera den befintliga kreditkortsinformationen som upphör att gälla. Om ändringarna för ditt kreditkort eftersom kortet går förlorad, stulen eller upphört att gälla, följer du stegen i den [lägga till ett kreditkort som en betalningsmetod](#addcard) avsnitt. Du behöver inte uppdatera CVV.

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj den prenumeration som är kopplad till kortet.
1. Välj **hantera betalningssätt**.
1. Välj **redigera** bredvid det kortet som du vill uppdatera.
1. Uppdatera informationen kredit- eller kort.
1. Välj **Spara**.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Använd ett annat kreditkort för Azure-prenumeration

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj den prenumeration som är kopplad till kortet.
1. Välj **Hantera betalningssätt** till höger på sidan.
1. Klicka på **Använd i stället** bredvid det kortet som du vill använda. Då uppdateras även andra prenumerationer som för närvarande är associerade med det här kortet. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>Ta bort ett kredit- eller kort från kontot

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som kontoadministratör.
1. Välj den prenumeration som är kopplad till kortet.
3. Välj **Hantera betalningssätt** till höger på sidan.
4. Klicka på **ta bort** för kreditkortet som du vill ta bort.

Om ditt kreditkort associeras med andra aktiva prenumerationer från Microsoft, inte du ta bort den från din Azure-konto. Ta bort kreditkortet från alla aktiva prenumerationer som du har med Microsoft och försök igen.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>Min prenumeration har inaktiverats. Varför kan jag för att bort mitt kontokort nu?

När din prenumeration är inaktiverad eller avbryts, vänta vi 90 dagar före permanent ta bort din prenumeration. Vi behåller din betalningsmetod på filen under kvarhållningsperioden om du vill aktivera prenumerationen. Efter att är prenumerationen bort helt.

Om du behöver ta bort din kredit- eller kort innan 90-dagars kvarhållningsperioden slutar [kontakta Azure-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Varför får jag ”din inloggningssession har upphört att gälla. Klicka här för att logga in igen ”?

Om du får detta felmeddelande även om loggat och sedan på försök igen med en privat webbläsarsession.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hur använder ett annat kort för varje prenumeration som jag har?

Tyvärr om dina prenumerationer redan använder samma kort, går det inte att separera dem för att använda olika kort. När du registrerar dig för en ny prenumeration kan välja du dock att använda en ny betalningsmetod för den prenumerationen.

### <a name="how-do-i-make-payments"></a>Hur gör jag betalningar

Om du har ställt in ett kreditkort eller en bankkort som din betalningsmetod debiterar vi automatiskt kortet efter varje faktureringsperioden. Du behöver inte göra något.

Om du är [betalning via faktura](billing-how-to-pay-by-invoice.md), skickar du betalningen till platsen som anges längst ned på fakturan.

### <a name="how-do-i-make-a-one-time-payment"></a>Hur gör jag en enstaka betalning

Tyvärr stöds Azure för närvarande inte enstaka betalningar för kredit- eller kort. 

## <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten

Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.
