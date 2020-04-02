---
title: Felsöka betalningsproblem med Azure
description: Detta gäller lösning av ett problem vid uppdatering av konto för betalningsinformation i Microsoft Azure-portalen eller kontocenter.
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: jaserano
ms.openlocfilehash: 59cad232ae4d16b8f74942e1e6026171ae1b8269
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408421"
---
# <a name="troubleshoot-azure-payment-issues"></a>Felsöka betalningsproblem med Azure

Ett problem eller fel kan uppstå när du försöker uppdatera kontot för betalningsinformation i antingen Microsoft Azure-portalen eller Azure-kontocentret.

För att lösa problemet väljer du det ämne som närmast liknar ditt fel.

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>Mitt kreditkort avvisades när jag försökte registrera mig i Azure

Information om hur du felsöker problem med ett avvisat kort finns i artikeln [Felsök ett avvisat kort vid Azure-registrering](troubleshoot-declined-card.md).

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Det går inte att visa prenumerationer i mitt konto för uppdatering av betalningsmetoden

Du kanske använder ett e-post-ID som skiljer sig från det som används för prenumerationerna.

Information om hur du felsöker det här problemet finns i [Inloggningsfelet Inga prenumerationer hittades för Azure-portalen eller Azure-kontocenter](no-subscriptions-found.md).

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>Det går inte att använda ett virtuellt kort, ett förbetald kreditkort eller ett betalkort som betalningsmetod.

*   Virtuella och förbetalda kreditkort kan inte användas som betalningsmedel för Azure-prenumerationer.
*   Betalkort kan inte användas som betalningsmedel för Azure-prenumerationer.

Mer information finns i [Felsöka ett avvisat kort vid Azure-registrering](troubleshoot-declined-card.md).

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Det går inte att ta bort ett kreditkort från en sparad betalningsmetod för fakturering

Det är avsiktligt att du inte kan ta bort ett kreditkort från den aktiva prenumerationen.

Om ett befintligt kort måste tas bort måste antingen ett nytt kort läggas till i prenumerationen så att det gamla betalningsmedlet kan tas bort, eller så måste du avbryta prenumerationen. Detta gör att prenumerationen tas bort permanent och att kortet tas bort.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Det går inte att ta bort en gammal betalningsmetod efter att en ny betalningsmetod har lagts till

Det nya betalningsmedlet är kanske inte kopplat till prenumerationen. Information om hur du associerar betalningsmedlet med prenumerationen finns i artikeln om hur du [lägger till, uppdaterar eller tar bort ett kreditkort för Azure](change-credit-card.md).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Det går inte att ta bort en betalningsmetod på grund av felet *Det går inte att ta bort betalningsmetoden*

Detta inträffar på grund av ett utestående saldo. Åtgärda eventuella utestående saldon innan du tar bort betalningsmetoden.

## <a name="unable-to-make-payment-for-a-subscription"></a>Det går inte att betala för en prenumeration

Om du får följande felmeddelande: *Betalningen har förfallit. Det föreligger ett problem med betalningsmetoden* eller *Det går inte att spara informationen. Stäng webbläsaren och försök igen.* finns det en väntande betalning på kortet eftersom kortet nekades av ditt finansinstitut.

Kontrollera att kreditkortet har tillräckligt med saldo för att betala. Om det inte har det använder du ett annat kort för att betala eller kontaktar finansinstitutet för att lösa problemet.

Kontakta din banken angående följande problem:

- Internationella transaktioner är inte aktiverade.
- Kortet har en kreditgräns och saldot måste kvittas.
- En återkommande betalning är aktiverad på kortet.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Det går inte att ändra betalningsmetod på grund av problem med webbläsaren (webbläsaren svarar inte, läser inte in och så vidare)

Logga ut från alla aktiva Azure-sessioner och följ sedan stegen i [artikeln om InPrivate-surfning i Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) för att starta en InPrivate-session i Microsoft Edge eller Internet Explorer.

I den privata sessionen följer du stegen i [Så ändrar du ett kreditkort](change-credit-card.md) för att uppdatera eller ändra kreditkortsuppgifterna.

Du kan även prova följande:

- Uppdatera webbläsaren
- Använda en annan webbläsare
- Ta bort cachelagrade cookies

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Min prenumeration är fortfarande inaktiverad efter uppdatering av betalningsmetoden.

Det här problemet inträffar på grund av ett utestående saldo. Åtgärda eventuella utestående saldon innan du tar bort betalningsmetoden.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Det går inte att ändra betalningsmetod på grund av en XML-felsvarssida

Det här meddelandet visas om du använder [Azure-portalen](https://portal.azure.com/) för att lägga till ett nytt kreditkort.

Du lägger till kortuppgifter genom att logga in på Azure-kontoportalen med hjälp av kontoadministratörens e-postadress.

## <a name="additional-help-resources"></a>Fler hjälpresurser

Här är några fler felsökningsartiklar som gäller fakturering och prenumerationer i Azure

- [Nekat kort](troubleshoot-declined-card.md)
- [Problem med prenumerationsinloggning](troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](../../billing/index.md)
