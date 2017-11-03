---
title: "Konfigurera lösenord enkel inloggning för ett program för Azure AD-galleriet problemet | Microsoft Docs"
description: "Förstå de vanliga problem personer står inför när du konfigurerar lösenord enkel inloggning för program som redan ingår i Azure AD Application Gallery"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 58d29996a922fac6d295e753ba5d66d32e745a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera lösenord enkel inloggning för ett program för Azure AD-galleriet problem

Den här artikeln hjälper dig att förstå de vanliga problem personer står inför när du konfigurerar **lösenord enkel inloggning** med ett program för Azure AD-galleriet.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Autentiseringsuppgifterna är ifylld, men tillägget Skicka inte dem

Detta inträffar vanligtvis om leverantören av tillämpningsprogrammet har ändrats sina inloggningssidan nyligen du lägger till ett fält genom att ändra en underliggande identifierare vi används för att identifiera fälten användarnamn och lösenord eller ändra hur inloggningen uppleva fungerar för sina program. I många fall kan arbeta Microsoft Lyckligtvis med leverantörer att snabbt lösa dessa problem.

När Microsoft har tekniker för att automatiskt upptäcka när dessa integreringar bryter, men ibland vi kan inte hitta de här problemen direkt eller de ta lite tid att åtgärda. I fall när något av dessa integreringar inte fungerar korrekt, gärna vi om du har öppnat ett supportärende så att vi kan åtgärda det så snabbt som möjligt.

Förutom detta **om du inte har kontakt med programvaruleverantören,** **skicka dem till vår sätt** så att vi kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören för att den [visar en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) så att den startade.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Autentiseringsuppgifterna är ifylld och har skickats, men sidan anger autentiseringsuppgifterna är felaktiga

Lös problemet, kontrollera följande:

-   Se till att användaren först och försök att **logga in på webbplatsen för programmet direkt** med de autentiseringsuppgifter som lagras i dem.

  * Om det fungerar sedan för att användaren klickar på den **uppdatera autentiseringsuppgifterna** knappen på den **programmet panelen** i den **appar** avsnitt i den [programmet åtkomstpanelen](https://myapps.microsoft.com/) uppdatera dem till den senaste kända fungerande användarnamn och lösenord.

   * Om du eller en annan administratör har tilldelats autentiseringsuppgifterna för denna användare, hitta användaren eller gruppen programmet tilldelningen genom att navigera till den **användare och grupper** för programmet, markera tilldelningen och klicka på den **referenser uppdatering** knappen.

-   Om användaren har tilldelats sina egna autentiseringsuppgifter, har användaren **Kontrollera att lösenordet inte har gått ut i programmet** och i så fall **uppdatera sina utgångna lösenord** genom att logga in direkt till programmet.

   * När lösenordet har uppdaterats i programmet kan begära att klicka på den **uppdatera autentiseringsuppgifterna** knappen på den **programmet panelen** i den **appar** avsnitt i den [programmet åtkomstpanelen](https://myapps.microsoft.com/) uppdatera dem till den senaste kända fungerande användarnamn och lösenord.

   * Om du eller en annan administratör har tilldelats autentiseringsuppgifterna för denna användare, hitta användaren eller gruppen programmet tilldelningen genom att navigera till den **användare och grupper** för programmet, markera tilldelningen och klicka på den **referenser uppdatering** knappen.

-   Behörighet att uppdatera webbläsartillägget för åtkomst-panelen genom att följa stegen nedan i den [installera åtkomst panelen webbläsartillägget](#how-to-install-the-access-panel-browser-extension) avsnitt.

-   Se till att webbläsartillägget för åtkomst panelen körs och aktiverat i användarens webbläsare.

-   Se till att användarna inte försöker logga in till programmet från åtkomstpanelen när i **incognito InPrivate- eller privat läge**. Tillägget för åtkomst-panelen stöds inte i dessa lägen.

Om det inte fungerar, kan det vara fallet som en ändring inträffat på den sida i programmet som tillfälligt bruten programmets integrering med Azure AD. T.ex, kan detta inträffa när programvaruleverantören introducerar ett skript på webbsidan som fungerar annorlunda för manuell eller automatisk indata, som orsakar automated integration som våra egna, att bryta. I många fall kan arbeta Microsoft Lyckligtvis med leverantörer att snabbt lösa dessa problem.

När Microsoft har tekniker för att automatiskt upptäcka när dessa integreringar bryter, men ibland vi kan inte hitta de här problemen direkt eller de ta lite tid att åtgärda. I fall när något av dessa integreringar inte fungerar korrekt, gärna vi om du har öppnat ett supportärende så att vi kan åtgärda det så snabbt som möjligt.

Förutom detta **om du inte har kontakt med programvaruleverantören,** **skicka dem till vår sätt** så att vi kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören för att den [visar en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) så att den startade.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Tillägget fungerar i Chrome och Firefox, men inte i Internet Explorer

Det finns två huvudsakliga orsaker till problemet:

-   Beroende på säkerhetsinställningarna aktiverad i Internet Explorer, om webbplatsen inte är en del av en **zonen Betrodda**, ibland våra skript blockeras från att köras för programmet.

  *  Lös detta genom att be användaren att **Lägg till webbplatsen för programmets** till den **tillförlitliga platser** lista inom deras **säkerhetsinställningar för Internet Explorer**. Du kan skicka till användarna i [hur du lägger till en plats i listan med betrodda platser](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) artikel detaljerade anvisningar.

-   I sällsynta fall kan orsaka Internet Explorer säkerhetsvalidering ibland sidan för att läsa in långsammare än våra skript körs.

   * Tyvärr kan kan den här situationen variera beroende på webbläsarens version, DATORHASTIGHET eller webbplats som besöks. I det här fallet föreslår vi att du kontaktar supporten kan vi lösa integration för den här specifika program.

Förutom detta **om du inte har kontakt med programvaruleverantören,** **skicka dem till vår sätt** så att vi kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören för att den [visar en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) så att den startade.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Kontrollera om programmets inloggningssida har ändrats nyligen eller kräver ett ytterligare fält

Om programmets inloggningssida har ändrats drastiskt leder ibland detta till vår integreringar sidbrytning. Ett exempel på detta är när en programvaruleverantören läggs tecknet i fältet, captcha eller multifaktorautentisering i deras upplevelser. I många fall kan arbeta Microsoft Lyckligtvis med leverantörer att snabbt lösa dessa problem.

När Microsoft har tekniker för att automatiskt upptäcka när dessa integreringar Bryt, men ibland vi kan inte hitta de här problemen direkt. Annars kan de ta lite tid att åtgärda. När något av dessa integreringar inte fungerar korrekt i fall skulle vi uppskattar öppna ett supportärende så att vi kan åtgärda det så snabbt som möjligt.

Förutom detta **om du inte har kontakt med programvaruleverantören,** **skicka dem till vår sätt** så att vi kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören för att den [visar en lista över ditt program i Azure Active Directory-programgalleriet](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) så att den startade.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du Access panelen webbläsartillägg

Följ stegen nedan om du vill installera webbläsartillägget för åtkomst panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** på åtkomstpanelen.

3.  Fråga om att installera programvara, Välj **installera nu**.

4.  Baserat på din webbläsare du dirigeras till länken. **Lägg till** tillägg till webbläsaren.

5.  Om din webbläsare, Välj antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in till åtkomstpanelen och se om kan du **starta** lösenord SSO-program

Du kan också ladda ned tillägget för Chrome och Firefox från direkt med länkarna nedan:

-   [Tillägget för Chrome åtkomst panelen](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägget för Firefox åtkomst panelen](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)

