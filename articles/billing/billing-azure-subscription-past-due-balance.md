---
title: E-postmeddelande från Azure har förfallit | Microsoft Docs
description: Beskriver hur du betalar om din Azure-prenumeration har en tidigare på grund av saldo
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
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491460"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Lös passerat betalningsdatum balans för din Azure-prenumeration

Om betalningen inte emot eller om vi inte behandla din betalning, kan du få ett e-postmeddelande eller ett meddelande i Azure portal eller Kontocenter.
Om du är den [kontoadministratör](billing-subscription-transfer.md#whoisaa), du kan reglera utestående debiteringar den [Azure-portalen](https://portal.azure.com). Om du är på en faktura-läget för betalning, skickar du betalningen till den plats som anges längst ned på fakturan.

> [!IMPORTANT]
> * Om du har flera prenumerationer med hjälp av samma kreditkort och de är alla har passerat, måste du betala hela utestående saldo på samma gång.
> * Det betalningsinstrument som du använder för att reglera de utestående debiteringarna blir den nya aktiva betalningsmetoden för alla prenumerationer som har använt den misslyckade betalningsmetoden.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Lös passerat betalningsdatum saldo i Azure portal

1. Logga in på den [Azure-portalen](https://portal.azure.com) som kontoadministratör
1. Sök efter **Cost Management + fakturering**.
1. På sidan översikt visas en lista över dina prenumerationer. Om prenumerationsstatus för din har förfallit, klickar du på den **reglera Saldo** länk.
    ![Skärmbild som visar reglera saldo länk](./media/billing-azure-subscription-past-due-balance/settle-balance-entry-point.png)
1. Totalt antal utestående saldo återspeglar utestående debiteringar över alla Microsoft-tjänster med misslyckade betalningssätt.
1. Välj en betalningsmetod betala balans. Den här betalningsmetoden blir aktiv betalningsmetod för alla prenumerationer som för närvarande använder misslyckade betalningsmetoden.
    ![Skärmbild som visar Välj betalning metoden länk](./media/billing-azure-subscription-past-due-balance/settle-balance-screen.png)
1. Om den valda betalningsmetoden har också obetalda avgifter för Microsoft-tjänster, återspeglas detta i totalt antal utestående saldo. Du måste betala för de utestående debiteringar.
1. Klicka på **betala**.

## <a name="not-getting-billing-email-notifications"></a>Inte får e-postaviseringar till fakturering?

Om du är kontoadministratör, [Kontrollera vilken e-postadress används för aviseringar](billing-how-to-change-azure-account-profile.md). Vi rekommenderar att du använder en e-postadress som du kontrollerar regelbundet. Om e-postmeddelandet är rätt, kontrollera mappen för skräppost.

## <a name="if-i-forget-to-pay-what-happens"></a>Vad händer om jag har glömt att betala?

Tjänsten avbryts och dina resurser är inte längre tillgängliga. Dina Azure-data tas bort 90 dagar efter att tjänsten avslutas. Mer information finns i [Microsoft Trust Center – hur vi hanterar dina data](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Om du vet att din betalning har bearbetats men prenumerationen fortfarande är inaktiverad, kontakta [Azure-supporten](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
