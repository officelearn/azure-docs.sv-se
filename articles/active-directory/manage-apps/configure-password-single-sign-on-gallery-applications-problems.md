---
title: Problem med att konfigurera lösenord SSO för Azure AD Gallery-appen
description: Förstå de vanliga problem som personer ställs inför när du konfigurerar Enkel inloggning för lösenord för program som redan finns med i Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e933c82229415a71182096a6aca9a2e535934d89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159054"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem med att konfigurera lösenord enkel inloggning för ett Azure AD Gallery-program

Den här artikeln hjälper dig att förstå de vanliga problem som personer ställs inför när du konfigurerar enkel inloggning för lösenord med ett Azure AD **Gallery-program.**

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Autentiseringsuppgifterna är ifyllda, men tillägget skickar dem inte

Det här problemet uppstår vanligtvis om programleverantören nyligen har ändrat sin inloggningssida för att lägga till ett fält, ändrat en identifierare som används för att identifiera fälten användarnamn och lösenord eller ändrat hur inloggningsupplevelsen fungerar för deras program. Lyckligtvis kan Microsoft i många fall arbeta med programleverantörer för att snabbt lösa dessa problem.

Microsoft har tekniker för att automatiskt identifiera när integreringar bryts, men det kanske inte går att hitta problemen direkt eller så tar det lite tid att åtgärda problemen. Om en av dessa integreringar inte fungerar korrekt öppnar du ett supportärende så att det kan åtgärdas så snabbt som möjligt.

**Om du har kontakt med programmets leverantör skickar du** dem på vårt sätt så att Microsoft kan arbeta med dem för att integrera sitt program med Azure Active Directory. Du kan skicka leverantören till [listningen av ditt program i Azure Active Directory-programgalleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Autentiseringsuppgifterna fylls i och skickas, men sidan anger att autentiseringsuppgifterna är felaktiga

Lös problemet genom att först prova följande:

- Låt användaren först försöka **logga in på programmets webbplats direkt** med de autentiseringsuppgifter som lagras för dem.

  * Om inloggningen fungerar ska användaren klicka på knappen **Uppdatera autentiseringsuppgifter** på **programpanelen** i avsnittet **Appar** på [programåtkomstpanelen](https://myapps.microsoft.com/) för att uppdatera dem till det senast kända användarnamnet och lösenordet för fungerande.

  * Om du eller en annan administratör har tilldelat autentiseringsuppgifterna för den här användaren letar du reda på användarens eller gruppens programtilldelning genom att navigera till fliken **Användare & grupper** i programmet, väljer tilldelningen och klickar på knappen Uppdatera **autentiseringsuppgifter.**

- Om användaren har tilldelat sina egna autentiseringsuppgifter ska användaren **kontrollera att lösenordet inte har upphört att gälla i programmet** och i så fall uppdatera sitt **utgångna lösenord** genom att logga in på programmet direkt.

  * När lösenordet har uppdaterats i programmet ber du användaren att klicka på knappen **Uppdatera autentiseringsuppgifter** på **programpanelen** i avsnittet **Appar** på [programåtkomstpanelen](https://myapps.microsoft.com/) för att uppdatera dem till det senast kända fungerande användarnamnet och lösenordet.

  * Om du eller en annan administratör har tilldelat autentiseringsuppgifterna för den här användaren letar du reda på användarens eller gruppens programtilldelning genom att navigera till fliken **Användare & grupper** i programmet, väljer tilldelningen och klickar på knappen Uppdatera **autentiseringsuppgifter.**

- Låt användaren uppdatera webbläsartillägget för åtkomstpanelen genom att följa stegen nedan i avsnittet [Installera webbläsartillägg för åtkomstpanelen.](#how-to-install-the-access-panel-browser-extension)

- Kontrollera att webbläsartillägget för åtkomstpanelen körs och är aktiverat i användarens webbläsare.

- Kontrollera att användarna inte försöker logga in på programmet från åtkomstpanelen i **inkognito-, inPrivat eller Privat läge**. Tillägget för åtkomstpanelen stöds inte i dessa lägen.

Om de tidigare förslagen inte fungerar kan det vara så att en ändring har skett på programsidan som tillfälligt har brutit programmets integrering med Azure AD. Detta kan till exempel inträffa när programleverantören introducerar ett skript på sin sida som fungerar annorlunda för manuell vs automatiserad indata, vilket gör att automatiserad integrering, som vår egen, går sönder. Lyckligtvis kan Microsoft i många fall arbeta med programleverantörer för att snabbt lösa dessa problem.

Microsoft har tekniker för att automatiskt identifiera när programintegreringar bryts, men det kanske inte går att hitta problemen direkt eller så kan problemen ta lite tid att åtgärda. När en integrering inte fungerar korrekt kan du öppna ett supportärende för att få det åtgärdat så snabbt som möjligt. 

Dessutom, om **du är i kontakt med det här programmets leverantör,** **skicka dem vårt sätt** så att vi kan arbeta med dem för att integrera deras program med Azure Active Directory. Du kan skicka leverantören till [listningen av ditt program i Azure Active Directory-programgalleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Tillägget fungerar i Chrome och Firefox, men inte i Internet Explorer

Det finns två huvudorsakare till problemet:

- Beroende på vilka säkerhetsinställningar som är aktiverade i Internet Explorer, om webbplatsen inte är en del av en **betrodd zon**, blockeras ibland vårt skript från att köras för programmet.

  *  Lös detta genom att instruera användaren att **lägga till programmets webbplats** i listan **Betrodda platser** i sina **säkerhetsinställningar i Internet Explorer**. Du kan skicka användarna till artikeln [Hur du lägger till en webbplats i listan över betrodda webbplatser](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) för detaljerade instruktioner.

- I sällsynta fall kan Internet Explorers säkerhetsvalidering ibland leda till att sidan läses in långsammare än körningen av vårt skript.

  * Tyvärr kan den här situationen variera beroende på webbläsarversion, datorhastighet eller besökt webbplats. I det här fallet föreslår vi att du kontaktar supporten så att vi kan åtgärda integreringen för det här specifika programmet.

Dessutom, om **du är i kontakt med det här programmets leverantör,** **skicka dem vårt sätt** så att vi kan arbeta med dem för att integrera deras program med Azure Active Directory. Du kan skicka leverantören till [listningen av ditt program i Azure Active Directory-programgalleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Kontrollera om programmets inloggningssida har ändrats nyligen eller kräver ytterligare ett fält

Om programmets inloggningssida har ändrats drastiskt, ibland detta orsakar våra integrationer att bryta. Ett exempel på detta är när en programleverantör lägger till ett inloggningsfält, en captcha eller multifaktorautentisering i sina upplevelser. Lyckligtvis kan Microsoft i många fall arbeta med programleverantörer för att snabbt lösa dessa problem.

Microsoft har tekniker för att automatiskt identifiera när programintegreringar bryts, men det kanske inte går att hitta problemen direkt eller så kan problemen ta lite tid att åtgärda. När en integrering inte fungerar korrekt kan du öppna ett supportärende för att få det åtgärdat så snabbt som möjligt. 

Dessutom, om **du är i kontakt med det här programmets leverantör,** **skicka dem vårt sätt** så att vi kan arbeta med dem för att integrera deras program med Azure Active Directory. Du kan skicka leverantören till [listningen av ditt program i Azure Active Directory-programgalleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du webbläsartillägget för åtkomstpanelen

Så här installerar du webbläsartillägget för åtkomstpanelen:

1.  Öppna [åtkomstpanelen](https://myapps.microsoft.com) i en av webbläsarna som stöds och logga in som **användare** i din Azure AD.

2.  klicka på ett **lösenords-SSO-program** på åtkomstpanelen.

3.  I prompten som ber om att installera programvaran väljer du **Installera nu**.

4.  Baserat på din webbläsare dirigeras du till nedladdningslänken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du att **aktivera** eller **tillåt** tillägget.

6.  Starta om webbläsarsessionen **när** den har installerats.

7.  Logga in på åtkomstpanelen och se om du kan **starta** dina lösenords-SSO-program

Du kan också ladda ner tillägget för Chrome och Firefox från de direkta länkarna nedan:

-   [Tillägg till panelen Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägg till Firefox-åtkomstpanelen](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)

