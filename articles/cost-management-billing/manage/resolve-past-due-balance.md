---
title: E-postmeddelande om förfallet belopp från Azure
description: Beskriver hur du betalar om din Azure-prenumeration har ett förfallet belopp
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 3c42db58017dc3a692c227f6eb454cf7b597e77b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91297592"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Lösa förfallet belopp för din Azure-prenumeration

Den här artikeln gäller för kunder som har registrerat sig för Azure Online med ett kreditkort och som har ett faktureringskonto för Microsoft Online Services-programmet. Läs om hur du [kontrollerar vilken typ av faktureringskonto du har](#check-the-type-of-your-account). Om du har ett faktureringskonto för Microsoft-kundavtal läser du [Betala fakturan för Microsoft Azure](../understand/pay-bill.md) i stället.

Om vi inte får din betalning eller om vi inte kan bearbeta den, får du ett e-postmeddelande och en varning på Azure-portalen som meddelar att förfallodatumet för prenumerationen har passerat. Om din standardbetalningsmetod är kreditkort kan [kontoadministratören](../understand/subscription-transfer.md#whoisaa) kvitta de utestående kostnaderna på Azure-portalen. Om du betalar per faktura (check/överföring) skickar du betalningen till den plats som anges längst ned på fakturan.

> [!Viktigt]
> * Om du har flera prenumerationer som använder samma kreditkort och samtliga av dessa har förfallit måste du betala hela det utestående beloppet samtidigt.
> * Det kreditkort som du använder för att kvitta de utestående kostnaderna blir den nya standardbetalningsmetoden för alla prenumerationer som använde betalningsmetoden som misslyckades.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Lösa förfallet belopp i Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com) som kontoadministratör.
1. Sök efter **Kostnadshantering och fakturering**.
1. Välj prenumerationen vars förfallodatum har passerat på sidan **Översikt**.
1. Klicka på den röda banderollen som anger att förfallodatumet har gått ut på sidan med **prenumerationsöversikten** för att reglera saldot.
    > [!NOTE]
    > Du måste vara kontoadministratör för att kunna reglera saldot.
1. Klicka på **Välj betalningsmetod** på den nya sidan **Reglera saldo**.
    ![Skärmbild som visar länk för att välja betalningsmetod](./media/resolve-past-due-balance/settle-balance-screen.png)

1. På det nya bladet till höger väljer du ett kreditkort i listrutan eller lägger till ett nytt genom att klicka på den blå länken **Lägg till ny betalningsmetod**. Det här kreditkortet blir den aktiva betalningsmetoden för alla prenumerationer som för närvarande använder betalningsmetoden som misslyckades.
     > [!NOTE]
     > * Det totala utestående beloppet visar utestående avgifter för alla Microsoft-tjänster där den misslyckade betalningsmetoden används.
     > * Om den valda betalningsmetoden också har utestående avgifter för Microsoft-tjänster visas detta i det totala utestående beloppet. Du måste även betala dessa utestående avgifter.
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

## <a name="check-the-type-of-your-account"></a>Kontrollera vilken typ av konto du har
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).
