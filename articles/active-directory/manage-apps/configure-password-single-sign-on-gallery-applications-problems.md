---
title: Problem med att konfigurera lösen ord för SSO för Azure AD Gallery-appen
description: Förstå vanliga problem när du konfigurerar enkel inloggning för lösen ord för program som redan finns med i Azure AD-programgalleriet
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89fda2657a68fc8a3fd293c0c6001a71f5970548
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763524"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem med att konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program

Den här artikeln hjälper dig att förstå vanliga problem med att konfigurera **enkel inloggning för lösen ord** med ett Azure AD Gallery-program.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Autentiseringsuppgifterna fylls i, men tillägget skickas inte

Det här problemet uppstår vanligt vis om program leverantören har ändrat sin inloggnings sida nyligen för att lägga till ett fält, ändrat en identifierare som används för att identifiera fälten för användar namn och lösen ord eller ändra hur inloggnings upplevelsen fungerar för programmet. I många fall kan Microsoft samar beta med program leverantörer för att snabbt lösa problemen.

Microsoft har teknik för att automatiskt identifiera när integreringen bryts, men det kanske inte går att hitta problemen direkt, eller så tar det en stund att åtgärda problemet. Om någon av dessa integreringar inte fungerar som den ska kan du öppna ett support ärende så att det kan åtgärdas så snabbt som möjligt.

**Om du är kontakt med den här appens leverantör kan du** skicka dem på vårt sätt så att Microsoft kan samar beta med dem för att internt integrera sina program med Azure Active Directory. Du kan skicka leverantören till [listan över ditt program i Azure Active Directory program galleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Autentiseringsuppgifterna fylls i och skickas, men sidan indikerar att autentiseringsuppgifterna är felaktiga

Prova med följande saker för att lösa problemet:

- Låt användaren först försöka logga in på **program webbplatsen direkt** med de autentiseringsuppgifter som lagras för dem.

  * Om inloggningen fungerar kan du klicka på knappen **uppdatera autentiseringsuppgifter** på **program** panelen i avsnittet **appar** i [program åtkomst panelen](https://myapps.microsoft.com/) för att uppdatera dem till det senaste kända fungerande användar namnet och lösen ordet.

  * Om du eller någon annan administratör har tilldelat autentiseringsuppgifterna för den här användaren, hittar du användarens eller gruppens program tilldelning genom att gå till fliken **användare & grupper** i programmet, välja tilldelningen och klicka på knappen **uppdatera autentiseringsuppgifter** .

- Om användaren har tilldelat sina egna autentiseringsuppgifter, måste användaren **kontrol lera att deras lösen ord inte har gått ut i programmet** , och i så fall **uppdatera sitt utgångna lösen ord** genom att logga in till programmet direkt.

  * När lösen ordet har uppdaterats i programmet ber du användaren att klicka på knappen **uppdatera autentiseringsuppgifter** på **program** panelen i avsnittet **appar** i [program åtkomst panelen](https://myapps.microsoft.com/) för att uppdatera dem till det senaste kända fungerande användar namnet och lösen ordet.

  * Om du eller någon annan administratör har tilldelat autentiseringsuppgifterna för den här användaren, hittar du användarens eller gruppens program tilldelning genom att gå till fliken **användare & grupper** i programmet, välja tilldelningen och klicka på knappen **uppdatera autentiseringsuppgifter** .

- Låt användaren uppdatera åtkomst panelens webb läsar tillägg genom att följa stegen nedan i avsnittet [Installera åtkomst panelens webb läsar tillägg](#how-to-install-the-access-panel-browser-extension) .

- Se till att åtkomst panelens webb läsar tillägg körs och är aktiverade i användarens webbläsare.

- Se till att användarna inte försöker logga in på programmet från åtkomst panelen i **Incognito, InPrivate eller privat läge**. Åtkomst panels tillägget stöds inte i de här lägena.

Om de tidigare förslagen inte fungerar, kan det vara fallet att en ändring har inträffat på den program sida där det tillfälligt har brutits mellan programmets integrering med Azure AD. Detta kan till exempel inträffa när program leverantören introducerar ett skript på sidan som beter sig annorlunda för manuella kontra automatiserade ingångar, vilket innebär att automatiserad integrering, som vår egen, ska brytas. I många fall kan Microsoft samar beta med program leverantörer för att snabbt lösa problemen.

Microsoft har teknik för att automatiskt identifiera när program integreringen bryts, men det kanske inte går att hitta problemen direkt, eller så kan det ta lite tid att åtgärda problemet. När en integrering inte fungerar som den ska kan du öppna ett support ärende för att få den åtgärdad så snabbt som möjligt. 

Förutom detta, **om du är i kontakt med den här appens leverantör, skickar du** **dem på vårt sätt** så att vi kan samar beta med dem för att internt integrera sina program med Azure Active Directory. Du kan skicka leverantören till [listan över ditt program i Azure Active Directory program galleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Tillägget fungerar i Chrome och Firefox, men inte i Internet Explorer

Det finns två huvudsakliga orsaker till det här problemet:

- Beroende på vilka säkerhets inställningar som Aktiver ATS i Internet Explorer, om webbplatsen inte är en del av en **betrodd zon**, blockeras ibland vårt skript från att köras för programmet.

  *  Du löser detta genom att instruera användaren att **lägga till programmets webbplats** i listan över **Betrodda platser** i **säkerhets inställningarna för Internet Explorer**. Du kan skicka dina användare till artikeln [så här lägger du till en plats i listan Mina betrodda webbplatser](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) för detaljerade anvisningar.

- I sällsynta fall kan Internet Explorers säkerhets validering ibland medföra att sidan läses in långsammare än med körningen av vårt skript.

  * Den här situationen kan tyvärr variera beroende på webbläsarens version, datorns hastighet eller besöks plats. I det här fallet rekommenderar vi att du kontaktar supporten så att vi kan åtgärda integreringen för det specifika programmet.

Förutom detta, **om du är i kontakt med den här appens leverantör, skickar du** **dem på vårt sätt** så att vi kan samar beta med dem för att internt integrera sina program med Azure Active Directory. Du kan skicka leverantören till [listan över ditt program i Azure Active Directory program galleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Kontrol lera om programmets inloggnings sida har ändrats nyligen eller kräver ett ytterligare fält

Om programmets inloggnings sida har ändrats drastiskt gör det ibland att våra integrationer kan brytas. Ett exempel på detta är när en program leverantör lägger till ett inloggnings fält, en captcha eller Multi-Factor Authentication i deras upplevelser. I många fall kan Microsoft samar beta med program leverantörer för att snabbt lösa problemen.

Microsoft har teknik för att automatiskt identifiera när program integreringen bryts, men det kanske inte går att hitta problemen direkt, eller så kan det ta lite tid att åtgärda problemet. När en integrering inte fungerar som den ska kan du öppna ett support ärende för att få den åtgärdad så snabbt som möjligt. 

Förutom detta, **om du är i kontakt med den här appens leverantör, skickar du** **dem på vårt sätt** så att vi kan samar beta med dem för att internt integrera sina program med Azure Active Directory. Du kan skicka leverantören till [listan över ditt program i Azure Active Directory program galleriet](../azuread-dev/howto-app-gallery-listing.md) för att komma igång.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst panelens webb läsar tillägg

Om du vill installera åtkomst panelens webb läsar tillägg följer du stegen nedan:

1.  Öppna [åtkomst panelen](https://myapps.microsoft.com) i någon av de webbläsare som stöds och logga in som en **användare** i Azure AD.

2.  Klicka på ett **Password-SSO-program** på åtkomst panelen.

3.  I uppmana att du uppmanas att installera program varan väljer du **Installera nu**.

4.  Baserat på din webbläsare dirigeras du till nedladdnings länken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du antingen **Aktivera** eller **Tillåt** tillägget.

6.  **Starta om** webbläsarsessionen när den har installerats.

7.  Logga in på åtkomst panelen och se om du kan **Starta** dina inloggnings program för lösen ord

Du kan också ladda ned tillägget för Chrome och Firefox från de direkta länkarna nedan:

-   [Tillägg för Chrome Access panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Åtkomst panels tillägg för Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

