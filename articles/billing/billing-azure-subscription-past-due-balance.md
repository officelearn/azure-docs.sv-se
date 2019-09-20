---
title: E-postmeddelande om förfallet belopp från Azure | Microsoft Docs
description: Beskriver hur du betalar om din Azure-prenumeration har ett förfallet belopp
services: ''
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.assetid: d0b88c92-fb9d-4d12-931b-c26104ad63e9
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: banders
ms.openlocfilehash: f58969b167b6c0f0a66d46731ad76c1f6e9acc41
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491460"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Lösa förfallet belopp för din Azure-prenumeration

Om din betalning inte tas emot eller om vi inte kan behandla betalningen kan det hända att du får ett e-postmeddelande eller ser en avisering i Azure-portalen eller Kontocenter.
Om du är [kontoadministratör](billing-subscription-transfer.md#whoisaa) kan du kvitta utestående avgifter i [Azure-portalen](https://portal.azure.com). Om du betalar med faktura skickar du betalningen till den plats som anges längst ned på fakturan.

> [!IMPORTANT]
> * Om du har flera prenumerationer som använder samma kreditkort och samtliga av dessa har förfallit måste du betala hela det utestående beloppet samtidigt.
> * Det betalningsmedel som du använder för att kvitta de utestående avgifterna blir den nya aktiva betalningsmetoden för alla prenumerationer som använde den misslyckade betalningsmetoden.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Lösa förfallet belopp i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering + fakturering**.
1. På sidan Översikt visas en lista över dina prenumerationer. Om din prenumerationsstatus är förfallen klickar du på länken **Reglera saldo**.
    ![Skärmbild som visar länken Reglera saldo](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. Det totala utestående beloppet visar utestående avgifter för alla Microsoft-tjänster där den misslyckade betalningsmetoden används.
1. Välj en betalningsmetod för att betala beloppet. Den här betalningsmetoden blir den aktiva betalningsmetoden för alla prenumerationer där den misslyckade betalningsmetoden för närvarande används.
    ![Skärmbild som visar länk för att välja betalningsmetod](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Om den valda betalningsmetoden också har utestående avgifter för Microsoft-tjänster visas detta i det totala utestående beloppet. Du måste även betala dessa utestående avgifter.
1. Klicka på **Betala**.

## <a name="not-getting-billing-email-notifications"></a>Får du inte e-postaviseringar om fakturering?

Om du är kontoadministratör [kontrollerar du vilken e-postadress som används för meddelanden](billing-how-to-change-azure-account-profile.md). Vi rekommenderar att du använder en e-postadress som du kontrollerar regelbundet. Om e-postadressen är korrekt kontrollerar du skräppostmappen.

## <a name="if-i-forget-to-pay-what-happens"></a>Vad händer om jag glömmer att betala?

Tjänsten avbryts och dina resurser slutar vara tillgängliga. Dina Azure-data tas bort 90 dagar efter att tjänsten har avbrutits. Mer information finns i [Microsoft Trust Center – Så hanterar vi dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Om du vet att din betalning har behandlats men din prenumeration fortfarande är inaktiverad kontaktar du [Azure-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
