---
title: Problem med att konfigurera lösenord för enkel inloggning för ett Azure AD-galleriprogram | Microsoft Docs
description: Förstå de vanliga problem personer står inför när du konfigurerar lösenord för enkel inloggning för program som redan ingår i Azure AD-Programgalleriet
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 01b77fc2521c221594a38b3731694115adf82e2c
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600813"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem med att konfigurera lösenord för enkel inloggning för ett Azure AD-galleriprogram

Den här artikeln hjälper dig att förstå de vanliga problem personer står inför när du konfigurerar **lösenord för enkel inloggning** med en Azure AD-galleriprogram.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Autentiseringsuppgifterna är ifyllda, men tillägget lämnar inte dem

Det här problemet inträffar vanligtvis om programleverantören har ändrats sina inloggningssidan nyligen om du vill lägga till ett fält, ändras en identifierare som används för identifiering av fälten användarnamn och lösenord eller ändra hur den inloggningen fungerar för sina program. I många fall fungerar som tur är kan Microsoft med programleverantörer, för att snabbt lösa dessa problem.

När Microsoft har tekniker för att identifiera automatiskt när integreringar bryter, den kanske inte går att hitta problem direkt eller problemen ta lite tid att åtgärda. I fallet när någon av de här integreringarna inte fungerar korrekt, att öppna ett supportärende så att det kan åtgärdas så snart som möjligt.

**Om du är i kontakt med programleverantören,** skicka dem oss så att Microsoft kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören som den [lista ditt program i Azure Active Directory-programgalleriet](develop/howto-app-gallery-listing.md) få dem igång.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Autentiseringsuppgifterna är ifyllt och har skickats, men sidan anger autentiseringsuppgifterna är felaktiga

Lös problemet genom att först prova dessa saker:

-   Användaren försöker först **logga in till webbplatsen program direkt** med de autentiseringsuppgifter som lagras i dem.

  * Om inloggningen fungerar, så finns användaren klickar du på den **uppdatera autentiseringsuppgifterna** knappen på den **program sida vid sida** i den **appar** delen av den [programåtkomst Panelen](https://myapps.microsoft.com/) du uppdatera dem till det senaste kända fungerande användarnamn och lösenord.

   * Om du eller en annan administratör har tilldelat autentiseringsuppgifterna för den här användaren kommer att hitta användaren eller gruppens programtilldelning genom att navigera till den **användare och grupper** fliken program, markerar tilldelningen och klickar på  **Uppdatera autentiseringsuppgifter** knappen.

-   Om du har tilldelat sina egna autentiseringsuppgifter, har användaren **kontroll för att se till att deras lösenord inte har gått ut i programmet** och i så fall, **uppdatera sina utgångna lösenord** genom att logga in till programmet direkt.

   * När lösenordet har uppdaterats i programmet, kan du begära att klicka på den **uppdatera autentiseringsuppgifterna** knappen på den **program sida vid sida** i den **appar** delen av den [Programåtkomstpanelen](https://myapps.microsoft.com/) du uppdatera dem till det senaste kända fungerande användarnamn och lösenord.

   * Om du eller en annan administratör har tilldelat autentiseringsuppgifterna för den här användaren kommer att hitta användaren eller gruppens programtilldelning genom att navigera till den **användare och grupper** fliken program, markerar tilldelningen och klickar på  **Uppdatera autentiseringsuppgifter** knappen.

-   Få användaren att uppdatera programåtkomstpanelens webbläsartillägg genom att följa stegen nedan i den [så här installerar du åtkomst till panelen webbläsartillägget](#how-to-install-the-access-panel-browser-extension) avsnittet.

-   Kontrollera att programåtkomstpanelens webbläsartillägg som körs och aktiverat i användarens webbläsare.

-   Se till att användarna inte försöker logga in till programmet från åtkomstpanelen samtidigt i **inkognito, InPrivate- eller privata läge**. Access panel-tillägg stöds inte i dessa lägen.

Om förslag som tidigare inte fungerar, kan det om en förändring har gjorts på programmet sida som har tillfälligt brytas programmets integrering med Azure AD. Exempel: Detta kan inträffa när programleverantören introducerar ett skript på webbsidan som fungerar annorlunda för manuell eller automatisk indata, som orsakar automatisk integrering som vår egen, dela. I många fall fungerar som tur är kan Microsoft med programleverantörer, för att snabbt lösa dessa problem.

När Microsoft har tekniker för att identifiera automatiskt när programmet integreringar bryter, det kanske inte går att hitta problem direkt eller problem som kan ta lite tid att åtgärda. När en integration inte fungerar korrekt, kan du öppna ett supportärende för att få det åtgärdat så snart som möjligt. 

Utöver detta är **om du är i kontakt med programleverantören,** **skicka dem till oss** så att vi kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören som den [lista ditt program i Azure Active Directory-programgalleriet](develop/howto-app-gallery-listing.md) få dem igång.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Tillägget fungerar i Chrome och Firefox, men inte i Internet Explorer

Det finns två huvudsakliga orsaker till det här problemet:

-   Beroende på säkerhetsinställningarna som är aktiverad i Internet Explorer om webbplatsen inte är en del av en **zonen Betrodda**, ibland våra skript blockeras från att köra för programmet.

  *  Lös detta genom att instruera användaren att **Lägg till programmets webbplats** till den **tillförlitliga platser** lista inom deras **säkerhetsinställningarna för Internet Explorer**. Du kan skicka användarna till den [hur du lägger till en plats i listan med betrodda platser](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) artikeln detaljerade anvisningar.

-   I sällsynta fall kan kan Säkerhetsverifieringen för Internet Explorer ibland orsaka sidan för att laddas långsammare än våra skript körs.

   * Tyvärr kan den här situationen variera beroende på webbläsarversion, DATORHASTIGHET eller webbplats som besöks. I det här fallet rekommenderar vi att du kontaktar support kan vi lösa integreringen för den här specifika program.

Utöver detta är **om du är i kontakt med programleverantören,** **skicka dem till oss** så att vi kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören som den [lista ditt program i Azure Active Directory-programgalleriet](develop/howto-app-gallery-listing.md) få dem igång.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Kontrollera om programmets inloggningssida har nyligen ändrats eller kräver ytterligare ett fält

Om programmets inloggningssida har ändrats dramatiskt, leder ibland detta till vår integreringar att avbryta. Ett exempel på detta är när en programleverantören lägger till ett fält för inloggning, captcha eller multifaktorautentisering i sina erfarenheter. I många fall fungerar som tur är kan Microsoft med programleverantörer, för att snabbt lösa dessa problem.

När Microsoft har tekniker för att identifiera automatiskt när programmet integreringar bryter, det kanske inte går att hitta problem direkt eller problem som kan ta lite tid att åtgärda. När en integration inte fungerar korrekt, kan du öppna ett supportärende för att få det åtgärdat så snart som möjligt. 

Utöver detta är **om du är i kontakt med programleverantören,** **skicka dem till oss** så att vi kan arbeta med dem för att integrera internt sina program med Azure Active Directory. Du kan skicka leverantören som den [lista ditt program i Azure Active Directory-programgalleriet](develop/howto-app-gallery-listing.md) få dem igång.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst till panelen webbläsartillägg

Följ stegen nedan om du vill installera webbläsartillägget för åtkomst till panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en av de webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** i åtkomstpanelen.

3.  I meddelandet som ber att installera programvaran, väljer **installera nu**.

4.  Beroende på din webbläsare, dirigeras du till länken. **Lägg till** tillägget till din webbläsare.

5.  Om webbläsaren ber, väljer du antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in på åtkomstpanelen på och se om kan du **starta** lösenord SSO-program

Du kan också ladda ned tillägget för Chrome och Firefox från direkt länkarna nedan:

-   [Chrome Access Panel-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-tillägg](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

