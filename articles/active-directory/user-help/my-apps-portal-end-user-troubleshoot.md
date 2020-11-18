---
title: Få hjälp med portalen Mina appar – Azure Active Directory | Microsoft Docs
description: Få hjälp med att logga in på och utföra vanliga uppgifter i portalen Mina appar.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5ae946f82590f2270719570e4b589d49542ffec8
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833987"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Felsök problem med portalen för Mina appar

Om du har problem med att logga in på eller använda **Mina Apps** -portalen kan du prova följande fel söknings tips innan du kontaktar supportavdelningen eller administratören för hjälp.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Jag har problem med att installera tillägget Mina appar säker inloggning

Om du har problem med att installera säkra inloggnings tillägg för Mina appar:

- Kontrol lera att du använder en webbläsare som stöds, inklusive:

    - **Microsoft Edge.** Körs på Windows 10-jubileums version eller senare.

    - **Google Chrome.** Körs på Windows 7 eller senare och på Mac OS X eller senare.

    - **Mozilla Firefox 26,0 eller senare.** Körs på Windows XP SP2 eller senare och på Mac OS X 10,6 eller senare.

    - **Internet Explorer 11.** Körs på Windows 7 eller senare (begränsat stöd).

- Kontrol lera att inställningarna för webb läsar tillägget är aktiverade.

- Försök att starta om webbläsaren och logga in på portalen **Mina appar** igen.

- Försök att rensa webbläsarens cookies och starta sedan om och logga in på portalen **Mina appar** igen.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Jag kan inte logga in på **min Apps** -Portal

Om du har problem med att logga in på portalen för **Mina appar** kan du prova följande:

- Kontrol lera att du använder rätt URL. Den bör vara https://myapps.microsoft.com eller en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com .

- Kontrol lera att lösen ordet är rätt och att det inte har gått ut. Mer information finns i [återställa lösen ordet för ditt arbets-eller skol](active-directory-passwords-update-your-own-password.md)konto.

- Kontrol lera att verifierings informationen är aktuell och korrekt. Mer information finns i [vad kan Azure AD Multi-Factor Authenticationa för mig?](./multi-factor-authentication-end-user-first-time.md) eller [ändra dina metoder och information om säkerhets information](./security-info-setup-auth-app.md).

- Lägg till **min app** portal-URL till inställningen **Internet egenskaper > säkerhets > betrodda platser** .

- Rensa webbläsarens cache och försök att logga in igen.

## <a name="my-password-isnt-working"></a>Mitt lösen ord fungerar inte

Om du har glömt ditt lösen ord, aldrig har fått ett från din organisation, är utelåst från ditt konto eller vill ändra ditt lösen ord, se [hjälp, jag har glömt mitt Azure AD-lösenord](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Jag vill kunna återställa mitt eget lösen ord

För att kunna återställa ditt eget lösen ord måste administratören först aktivera funktionen för din organisation, och sedan måste du uppdatera och verifiera dina nödvändiga verifierings metoder. Mer information om hur du uppdaterar dina verifierings metoder finns i [Registrera för lösen ords återställning via självbetjäning](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Jag får ett meddelande om nekad åtkomst när jag startar en app

Om du får ett meddelande om **nekad åtkomst** efter att du har startat en app från **min app** -Portal kan du prova följande:

- Kontrol lera att du har installerat det [säkra inloggnings tillägget för Mina appar](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) och att du använder en [webbläsare som stöds](my-apps-portal-end-user-access.md#supported-browsers).

- Kontrol lera att du använder rätt URL för appen och att URL: en finns på **Internet egenskaper > säkerhets > listan över betrodda platser** .

- Kontrol lera att lösen ordet är rätt och att det inte har gått ut. Mer information finns i [återställa lösen ordet för ditt arbets-eller skol](active-directory-passwords-update-your-own-password.md)konto.

- Kontrol lera att verifierings informationen är aktuell och korrekt. Mer information finns i [vad kan Azure AD Multi-Factor Authenticationa för mig?](./multi-factor-authentication-end-user-first-time.md) eller [ändra dina metoder och information om säkerhets information](./security-info-setup-auth-app.md).

- Rensa webbläsarens cache och försök att logga in igen.

Om du efter att ha försökt med de här åtgärderna fortfarande inte kan komma åt din app, måste du kontakta din organisations supportavdelning om du behöver hjälp.

## <a name="next-steps"></a>Nästa steg

När du har loggat in på **Mina Apps** -portalen kan du också uppdatera din profil-och konto information, din grupp information och åtkomst gransknings information (om du har behörighet).

- [Få åtkomst till och använda appar på Mina appar-portalen](my-apps-portal-end-user-access.md).

- [Ändra din profil information](./my-account-portal-settings.md).

- [Visa och uppdatera dina grupper – relaterad information](my-apps-portal-end-user-groups.md).

- [Utför dina egna åtkomst granskningar](my-apps-portal-end-user-access-reviews.md).