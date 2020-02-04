---
title: E-postmeddelande om förfallet belopp från Azure
description: Beskriver hur du betalar om din Azure-prenumeration har ett förfallet belopp
author: genlin
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: 7216af00413b1f8022957ac134f67a5c27b6cc78
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "75987907"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Lösa förfallet belopp för din Azure-prenumeration

Den här artikeln gäller för kunder med ett Microsoft Online Services-programkonto.

Om din betalning inte tas emot eller om vi inte kan behandla betalningen kan det hända att du får ett e-postmeddelande eller ser en avisering i Azure-portalen eller Kontocenter.
Om du är [kontoadministratör](billing-subscription-transfer.md#whoisaa) kan du kvitta utestående avgifter i [Azure-portalen](https://portal.azure.com). Om du betalar med faktura skickar du betalningen till den plats som anges längst ned på fakturan.

> [!IMPORTANT]
> * Om du har flera prenumerationer som använder samma kreditkort och samtliga av dessa har förfallit måste du betala hela det utestående beloppet samtidigt.
> * Det betalningsmedel som du använder för att kvitta de utestående avgifterna blir den nya aktiva betalningsmetoden för alla prenumerationer som använde den misslyckade betalningsmetoden.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Lösa förfallet belopp i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.
1. På sidan Översikt visas en lista över dina prenumerationer. Om din prenumerationsstatus är förfallen klickar du på länken **Reglera saldo**.
    ![Skärmbild som visar länken Reglera saldo](./media/resolve-past-due-balance/settle-balance-entry-point.png)
1. Det totala utestående beloppet visar utestående avgifter för alla Microsoft-tjänster där den misslyckade betalningsmetoden används.
1. Välj en betalningsmetod för att betala beloppet. Den här betalningsmetoden blir den aktiva betalningsmetoden för alla prenumerationer där den misslyckade betalningsmetoden för närvarande används.
    ![Skärmbild som visar länk för att välja betalningsmetod](./media/resolve-past-due-balance/settle-balance-screen.png)
1. Om den valda betalningsmetoden också har utestående avgifter för Microsoft-tjänster visas detta i det totala utestående beloppet. Du måste även betala dessa utestående avgifter.
1. Klicka på **Betala**.

## <a name="troubleshoot-declined-credit-card"></a>Felsök nekade kreditkort

Om din betalning med ett kreditkort nekades av ditt finansinstitut kontaktar du finansinstitutet för att lösa problemet. Kontrollera med din bank för att se till att:
- Internationella transaktioner är aktiverade.
- Kortet har en tillräckligt hög kreditgräns eller tillräckligt med pengar på kontot för att genomföra betalningen.
- Återkommande betalningar är aktiverat på kortet.

## <a name="not-getting-billing-email-notifications"></a>Får du inte e-postaviseringar om fakturering?

Om du är kontoadministratör [kontrollerar du vilken e-postadress som används för meddelanden](change-azure-account-profile.md). Vi rekommenderar att du använder en e-postadress som du kontrollerar regelbundet. Om e-postadressen är korrekt kontrollerar du skräppostmappen.

## <a name="if-i-forget-to-pay-what-happens"></a>Vad händer om jag glömmer att betala?

Tjänsten avbryts och dina resurser slutar vara tillgängliga. Dina Azure-data tas bort 90 dagar efter att tjänsten har avbrutits. Mer information finns i [Microsoft Trust Center – Så hanterar vi dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Om du vet att din betalning har behandlats men din prenumeration fortfarande är inaktiverad kontaktar du [Azure-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
