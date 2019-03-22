---
title: Få hjälp med Mina appar-portal – Azure Active Directory | Microsoft Docs
description: Få hjälp med att logga in på och utföra vanliga uppgifter i portalen Mina appar.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e87ae0a8adf28176d9a97cbf1b78943179884a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340116"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Felsöka problem med portalen Mina appar
Om du upplever problem med att logga in på eller använda den **Mina appar** portal, försök med dessa felsökningstips innan du kontaktar supportavdelningen eller administratören om du behöver hjälp.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Jag har problem med att installera den Mina appar skyddat Inloggningstillägg
Om du har problem med att installera den Mina appar skyddat Inloggningstillägg:

- Kontrollera att du använder en webbläsare som stöds, inklusive:

    - **Microsoft Edge.** Körs på Windows 10 Anniversary Edition eller senare.
    - **Google Chrome.** Körs på Windows 7 eller senare, och i Mac OS X eller senare.
    - **Mozilla Firefox 26.0 eller senare.** Körs på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.
    - **Internet Explorer 11.** Körs på Windows 7 eller senare (begränsat stöd).

- Kontrollera att dina webbläsarinställningar för tillägget är aktiverade.

- Försök att starta om webbläsaren och logga in på den **Mina appar** portalen igen.

- Försöka ta bort cookies i webbläsaren, och sedan starta om och logga in på den **Mina appar** portalen igen.

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Jag kan inte logga in till den **Mina appar** portal
Om du har problem med att logga in på den **Mina appar** portal, kan du försöka med följande:

- Kontrollera att du använder rätt URL. Det bör vara https://myapps.microsoft.com eller en anpassad sida för din organisation, till exempel https://myapps.microsoft.com/contoso.com.

- Kontrollera att lösenordet är korrekt och inte har gått ut. Mer information finns i [återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Kontrollera att din information för verifiering är aktuell och korrekt. Mer information finns i [vad Azure Multi-Factor Authentication innebär för mig?](multi-factor-authentication-end-user.md) eller [ändrar säkerhetsmetoder för information och information](security-info-add-update-methods-overview.md).

- Lägg till den **mitt program** portal URL: en till den **Internetegenskaper > Säkerhet > betrodda platser** inställningen.

- Rensa webbläsarens cacheminne och försök att logga in igen.

## <a name="my-password-isnt-working"></a>Mitt lösenord fungerar inte
Om du har glömt ditt lösenord, aldrig fått något från din organisation, är utelåst från ditt konto eller vill du ändra ditt lösenord, se [hjälp, jag har glömt mitt Azure AD-lösenord](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Jag vill kunna återställa mitt eget lösenord
Om du vill kunna återställa ditt eget lösenord administratören måste aktivera funktionen för din organisation och sedan du måste uppdatera och verifiera dina krävs verifieringsmetoder. Läs mer om hur du uppdaterar din verifieringsmetoder [registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Jag får ett meddelande om nekad åtkomst när jag påbörjar en app
Om du får en **åtkomst nekad** meddelande när du startar en app från den **mitt program** portal, kan du försöka med följande:

- Kontrollera att du har installerat den [Mina appar skyddat Inloggningstillägg](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) och att du använder en [webbläsare som stöds](my-apps-portal-end-user-access.md#supported-browsers).

- Kontrollera att du använder rätt URL för appen och att URL: en är på din **Internetegenskaper > Säkerhet > betrodda platser** lista.

- Kontrollera att lösenordet är korrekt och inte har gått ut. Mer information finns i [återställa lösenordet för arbets- eller skolkonto](active-directory-passwords-update-your-own-password.md).

- Kontrollera att din information för verifiering är aktuell och korrekt. Mer information finns i [vad Azure Multi-Factor Authentication innebär för mig?](multi-factor-authentication-end-user.md) eller [ändrar säkerhetsmetoder för information och information](security-info-add-update-methods-overview.md).

- Rensa webbläsarens cacheminne och försök att logga in igen.

Om när du har försökt dessa saker du fortfarande inte komma åt din app, måste du kontakta organisationens supportavdelning om du behöver hjälp.

## <a name="next-steps"></a>Nästa steg
När du loggar in på den **Mina appar** portal, kan du även uppdatera din profil och kontoinformation, din gruppinformation och åtkomst granska information (om du har behörighet).

- [Komma åt och använda appar på portalen Mina appar](my-apps-portal-end-user-access.md).

- [Ändra profilinformationen](my-apps-portal-end-user-update-profile.md).

- [Visa och uppdatera dina grupper-relaterad information](my-apps-portal-end-user-groups.md).

- [Utföra egna åtkomstgranskningar](my-apps-portal-end-user-access-reviews.md).