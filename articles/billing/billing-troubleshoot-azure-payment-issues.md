---
title: Felsöka betalningsproblem med Azure
description: Detta gäller lösning av ett problem vid uppdatering av konto för betalningsinformation i Microsoft Azure-portalen eller kontocenter.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2019
ms.locfileid: "69657091"
---
# <a name="troubleshoot-azure-payment-issues"></a>Felsöka betalningsproblem med Azure

Ett problem kan uppstå när du försöker uppdatera kontot för betalningsinformation i Microsoft Azure-portalen eller Azure-kontocenter. Innan du felsöker problemet bör du beakta följande riktlinjer:

- Kontrollera att de uppgifter som du har uppgett för din Azure-kontoprofil (däribland e-postadress, gatuadress och telefonnummer för kontakt) är korrekta.
- Kontrollera att kreditkortsuppgifterna stämmer.
- Kontrollera att du inte redan har ett Microsoft-konto där du använder samma uppgifter.

## <a name="issues"></a>Problem

Lös eventuella problem genom att välja det problem som uppstår för dig när du försöker registrera dig för Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Det går inte att ta bort ett kreditkort från en sparad betalningsmetod för fakturering

Det är avsiktligt att du inte kan ta bort ett kreditkort från den aktiva prenumerationen.

Om ett befintligt kort måste tas bort måste antingen ett nytt kort läggas till i prenumerationen så att det gamla betalningsmedlet kan tas bort, eller så måste du avbryta prenumerationen. Detta gör att prenumerationen tas bort permanent och att kortet tas bort.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Det går inte att ta bort en gammal betalningsmetod efter att en ny betalningsmetod har lagts till

Det nya betalningsmedlet är kanske inte kopplat till prenumerationen. Information om hur du associerar betalningsmedlet med prenumerationen finns i artikeln om hur du [lägger till, uppdaterar eller tar bort ett kredit- eller debetkort för Azure](billing-how-to-change-credit-card.md).

Information om hur du felsöker problem med ett avvisat kort finns i artikeln om hur du [felsöker ett avvisat kort vid Azure-registrering](billing-troubleshoot-declined-card.md).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Det går inte att ta bort en betalningsmetod på grund av felet *Det går inte att ta bort betalningsmetoden*

Detta inträffar på grund av ett utestående saldo. Åtgärda eventuella utestående saldon innan du tar bort betalningsmetoden.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Det går inte att visa prenumerationer i mitt konto för uppdatering av betalningsmetoden

Du kanske använder ett e-post-ID som skiljer sig från det som används för prenumerationerna.

Information om hur du felsöker det här problemet finns i [Inloggningsfelet Inga prenumerationer hittades för Azure-portalen eller Azure-kontocenter](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Det går inte att betala för en prenumeration

Om du får följande felmeddelande: *Betalningen har förfallit. Det föreligger ett problem med betalningsmetoden* eller *Det går inte att spara informationen. Stäng webbläsaren och försök igen.* finns det en väntande betalning på kortet eftersom kortet nekades av ditt finansinstitut.

Kontrollera att kreditkortet har tillräckligt med saldo för att betala. Om det inte har det använder du ett annat kort för att betala eller kontaktar finansinstitutet för att lösa problemet.

Kontakta din banken angående följande problem:

- Internationella transaktioner är inte aktiverade.
- Kortet har en kreditgräns och saldot måste kvittas.
- En återkommande betalning är aktiverad på kortet.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Det går inte att ändra betalningsmetod på grund av problem med webbläsaren (webbläsaren svarar inte, läser inte in och så vidare)

Logga ut från alla aktiva Azure-sessioner och följ sedan stegen i [artikeln om InPrivate-surfning i Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) för att starta en InPrivate-session i Microsoft Edge eller Internet Explorer.

I den privata sessionen följer du stegen i [Så ändrar du ett kreditkort](billing-how-to-change-credit-card.md) för att uppdatera eller ändra kreditkortsuppgifterna.

Du kan även prova följande:

- Uppdatera webbläsaren
- Använda en annan webbläsare
- Ta bort cachelagrade cookies

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Min prenumeration är fortfarande inaktiverad efter uppdatering av betalningsmetoden.

Det här problemet inträffar på grund av ett utestående saldo. Åtgärda eventuella utestående saldon innan du tar bort betalningsmetoden.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Det går inte att ändra betalningsmetod på grund av en XML-felsvarssida

Det här meddelandet visas om du använder [Azure-portalen](https://portal.azure.com/) för att lägga till ett nytt kreditkort.

Du lägger till kortuppgifter genom att logga in på Azure-kontoportalen med hjälp av kontoadministratörens e-postadress.

## <a name="additional-help-resources"></a>Fler hjälpresurser

Här är några fler felsökningsartiklar som gäller fakturering och prenumerationer i Azure

- [Nekat kort](billing-troubleshoot-declined-card.md)
- [Problem med prenumerationsinloggning](billing-troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](billing-no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](index.md)
