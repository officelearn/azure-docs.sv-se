---
title: Felsök problem med Azure-betalning
description: Lösa ett problem när du uppdaterar kontot för betalnings information i Microsoft Azure-portalen eller konto Center.
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
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657091"
---
# <a name="troubleshoot-azure-payment-issues"></a>Felsök problem med Azure-betalning

Ett problem kan uppstå när du försöker uppdatera betalnings informations kontot i Microsoft Azure-portalen eller Azure konto Center. Innan du felsöker problemet bör du tänka på följande rikt linjer:

- Kontrol lera att den information som du har angett för din profil för Azure-kontot (inklusive e-postadress för kontakt), gatuadress och telefonnummer) är korrekt.
- Kontrollera att kreditkortsuppgifterna stämmer.
- Kontrollera att du inte redan har ett Microsoft-konto där du använder samma uppgifter.

## <a name="issues"></a>Problem

Om du vill lösa eventuella fel väljer du det problem som du stöter på när du försöker registrera dig för Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Det går inte att ta bort ett kredit kort från en sparad fakturerings betalnings metod

Som design kan du inte ta bort ett kredit kort från den aktiva prenumerationen.

Om ett befintligt kort måste tas bort måste ett nytt kort läggas till i prenumerationen så att det gamla betalnings medlet kan tas bort, eller så måste du avbryta prenumerationen. Prenumerationen tas bort permanent och kortet tas bort.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Det går inte att ta bort en gammal betalnings metod när en ny betalnings metod har lagts till

Det nya betalningsmedlet är kanske inte kopplat till prenumerationen. Information om hur du associerar betalnings medlet med prenumerationen finns i [lägga till, uppdatera eller ta bort ett kredit-eller betalkort för Azure](billing-how-to-change-credit-card.md).

Information om hur du felsöker problem med ett avvisat kort finns i [fel sökning av ett nekat kort på Azure-registrering](billing-troubleshoot-declined-card.md).

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Det går inte att ta bort en betalnings metod eftersom *det inte går att ta bort betalnings metod* fel

Detta inträffar på grund av ett utestående saldo. Rensa eventuella utestående saldon innan du tar bort betalnings metoden.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Det gick inte att se prenumerationer under mitt konto för att uppdatera betalnings metoden

Du kanske använder ett e-post-ID som skiljer sig från det som används för prenumerationerna.

Information om hur du felsöker det här problemet finns i [inga prenumerationer hittade inloggnings fel för Azure Portal eller Azure konto Center](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Det går inte att betala för en prenumeration

Om du får fel meddelandet: *Betalningen har förfallit. Det har uppstått ett problem med din betalnings* metod *eller så kan du tyvärr inte spara informationen. Stäng webbläsaren och försök igen.* , det finns en väntande betalning på kortet eftersom kortet nekades av ditt finansiella institut.

Kontrol lera att kredit kortet har tillräckligt med saldo för att betala. Om den inte gör det använder du ett annat kort för att betala eller kontakta finans Institutet för att lösa problemet.

Kontakta banken för att se följande problem:

- Internationella transaktioner är inte aktiverade.
- Kortet har en kredit gräns och saldot måste kvittas.
- En återkommande betalning är aktive rad på kortet.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Det går inte att ändra betalnings metod på grund av webb läsar problem (webbläsaren svarar inte, inläsning och så vidare)

Logga ut från alla aktiva Azure-sessioner och följ sedan stegen i [artikeln Browse InPrivate i Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) för att starta en InPrivate-session i Microsoft Edge eller Internet Explorer.

I den privata sessionen följer du stegen i [så här ändrar du ett kredit kort](billing-how-to-change-credit-card.md) för att uppdatera eller ändra kreditkorts informationen.

Du kan också försöka med följande:

- Uppdatera webbläsaren
- Använd en annan webbläsare
- Ta bort cachelagrade cookies

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Min prenumeration är fortfarande inaktive rad när du har uppdaterat betalnings metoden.

Det här problemet uppstår på grund av ett utestående saldo. Rensa eventuella utestående saldon innan du tar bort betalnings metoden.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Det går inte att ändra betalnings metod på grund av en XML-felsvars sida

Du får det här meddelandet om du använder [Azure Portal](https://portal.azure.com/) för att lägga till ett nytt kredit kort.

Om du vill lägga till kort information loggar du in på Azure-konto portalen genom att använda konto administratörens e-postadress.

## <a name="additional-help-resources"></a>Ytterligare hjälp resurser

Andra fel söknings artiklar för Azure-fakturering och-prenumerationer

- [Nekat kort](billing-troubleshoot-declined-card.md)
- [Problem med prenumerations inloggning](billing-troubleshoot-sign-in-issue.md)
- [Inga prenumerationer hittades](billing-no-subscriptions-found.md)
- [Företagskostnadsvy inaktiverad](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Kontakta oss om du behöver hjälp

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om Azure-fakturering](index.md)
