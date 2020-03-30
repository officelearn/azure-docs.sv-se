---
title: Få hjälp med portalen Mina appar – Azure Active Directory| Microsoft-dokument
description: Få hjälp med att logga in på och utföra vanliga uppgifter i portalen Mina appar.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: f72dd5595d67ae989cec5681d22def9a2f929adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253161"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Felsöka problem med portalen Mina appar

Om du har problem med att logga in på eller använda portalen **Mina appar** kan du prova de här felsökningstipsen innan du kontaktar helpdesk eller administratören för att få hjälp.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Jag har problem med att installera tillägget För säker inloggning för mina appar

Om du har problem med att installera tillägget För säker inloggning för mina appar:

- Kontrollera att du använder en webbläsare som stöds, inklusive:

    - **Microsoft Edge.** Körs på Windows 10 Anniversary Edition eller senare.

    - **Google Chrome.** Körs på Windows 7 eller senare, och på Mac OS X eller senare.

    - **Mozilla Firefox 26.0 eller senare.** Körs på Windows XP SP2 eller senare och på Mac OS X 10.6 eller senare.

    - **Internet Explorer 11.** Körs på Windows 7 eller senare (begränsat stöd).

- Kontrollera att inställningarna för webbläsartillägg är aktiverade.

- Försök starta om webbläsaren och logga in på portalen **Mina appar** igen.

- Försök att rensa webbläsarens cookies och starta sedan om och logga in på portalen **Mina appar** igen.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Jag kan inte logga in på portalen **Mina appar**

Om du har problem med att logga in på portalen **Mina appar** kan du prova följande:

- Kontrollera att du använder rätt webbadress. Det bör https://myapps.microsoft.com vara eller en anpassad sida https://myapps.microsoft.com/contoso.comför din organisation, till exempel .

- Kontrollera att lösenordet är korrekt och inte har upphört att gälla. Mer information finns i [Återställa ditt arbets- eller skollösenord](active-directory-passwords-update-your-own-password.md).

- Kontrollera att din verifieringsinformation är aktuell och korrekt. Mer information finns i [Vad betyder Azure Multi-Factor Authentication för mig?](multi-factor-authentication-end-user.md) [Changing your security info methods and information](security-info-add-update-methods-overview.md)

- Lägg till url:en för **min appportal** i inställningen **för Internet-egenskaper > säkerhet > betrodda platser.**

- Rensa webbläsarens cacheminne och försök logga in igen.

## <a name="my-password-isnt-working"></a>Mitt lösenord fungerar inte

Om du har glömt ditt lösenord, aldrig fått något från din organisation, är utelåst från ditt konto eller vill ändra ditt lösenord läser [du Hjälpen, jag har glömt mitt Azure AD-lösenord](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Jag vill kunna återställa mitt eget lösenord

För att kunna återställa ditt eget lösenord måste administratören först aktivera funktionen för din organisation och sedan måste du uppdatera och verifiera de verifieringsmetoder som krävs. Mer information om hur du uppdaterar dina verifieringsmetoder finns i [Registrera dig för återställning av lösenord med självbetjäning](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Jag får ett meddelande om nekad åtkomst när jag startar en app

Om du får ett meddelande om **nekad åtkomst** när du har startat en app från **portalen Min app** kan du prova följande:

- Kontrollera att du har installerat [tillägget För säker inloggning i Mina appar](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) och att du använder en webbläsare som [stöds.](my-apps-portal-end-user-access.md#supported-browsers)

- Kontrollera att du använder rätt URL för appen och att webbadressen finns i listan **över Internetegenskaper > säkerhet > betrodda webbplatser.**

- Kontrollera att lösenordet är korrekt och inte har upphört att gälla. Mer information finns i [Återställa ditt arbets- eller skollösenord](active-directory-passwords-update-your-own-password.md).

- Kontrollera att din verifieringsinformation är aktuell och korrekt. Mer information finns i [Vad betyder Azure Multi-Factor Authentication för mig?](multi-factor-authentication-end-user.md) [Changing your security info methods and information](security-info-add-update-methods-overview.md)

- Rensa webbläsarens cacheminne och försök logga in igen.

Om du efter att ha provat dessa saker fortfarande inte kan komma åt din app måste du kontakta organisationens supportavdelning för att få hjälp.

## <a name="next-steps"></a>Nästa steg

När du har loggat in på Portalen **Mina appar** kan du också uppdatera din profil- och kontoinformation, din gruppinformation och åtkomstgranskningsinformation (om du har behörighet).

- [Komma åt och använda appar på portalen Mina appar](my-apps-portal-end-user-access.md).

- [Ändra din profilinformation](my-apps-portal-end-user-update-profile.md).

- [Visa och uppdatera grupprelaterad information](my-apps-portal-end-user-groups.md).

- [Utför dina egna åtkomstrecensioner](my-apps-portal-end-user-access-reviews.md).
