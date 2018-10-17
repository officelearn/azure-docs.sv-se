---
title: 'Azure AD Connect: Direktautentisering – vanliga frågor och svar | Microsoft Docs'
description: Svar på vanliga frågor om Azure Active Directory-direktautentisering
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4f2ceae349c921ce0d83fb7401e3b18404722763
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49362916"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory-direktautentisering: Vanliga frågor och svar

Den här artikeln tar upp vanliga frågor och svar om Azure Active Directory (Azure AD)-direktautentisering. Kontrollera tillbaka för uppdaterat innehåll.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Vilka metoder för att logga in på Azure AD, direktautentisering, lösenord hash-synkronisering och Active Directory Federation Services (AD FS) ska jag välja?

Granska [den här guiden](https://docs.microsoft.com/azure/security/azure-ad-choose-authn) en jämförelse av de olika Azure AD-inloggning metoderna och hur du väljer den direkt inloggningsmetoden för din organisation.

## <a name="is-pass-through-authentication-a-free-feature"></a>Är en kostnadsfri funktion för direktautentisering?

Direktautentisering är en kostnadsfri funktion. Du behöver inte några betald utgåvor av Azure AD för att använda den.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Finns direktautentisering i den [Microsoft Azure Tyskland](http://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government-molnet](https://azure.microsoft.com/features/gov/)?

Nej. Direktautentisering är endast tillgängligt i den globala instansen av Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Har [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) fungerar med direktautentisering?

Ja. Alla funktioner för villkorlig åtkomst, inklusive Azure Multi-Factor Authentication fungerar med direktautentisering.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Stöder direktautentisering ”alternativa ID” som användarnamn, i stället för ”userPrincipalName”?

Ja. Har stöd för direktautentisering `Alternate ID` som användarnamn när du konfigurerade i Azure AD Connect. Mer information finns i [anpassad installation av Azure AD Connect](how-to-connect-install-custom.md). Inte alla Office 365-program som stöder `Alternate ID`. Finns det specifika programmet dokumentation support-instruktionen.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Synkronisering av lösenordshash fungera som reserv för direktautentisering?

Nej. Direktautentisering _inte_ automatiskt växla över till synkronisering av lösenordshash. Om du vill undvika användaren inloggningar, bör du konfigurera direktautentisering för [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxymanage-appsapplication-proxymd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kan jag installera en [Azure AD Application Proxy](../manage-apps/application-proxy.md) anslutningsapp på samma server som en Autentiseringsagenten för direktautentisering?

Ja. Rebranded versioner av Autentiseringsagenten för direktautentisering, version 1.5.193.0 eller senare, stöder den här konfigurationen.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Vilka versioner av Azure AD Connect och Autentiseringsagenten för direktautentisering behöver du?

För den här funktionen ska fungera måste version 1.1.750.0 eller senare för Azure AD Connect och 1.5.193.0 eller senare för Autentiseringsagenten för direktautentisering. Installera all programvara på servrar med Windows Server 2012 R2 eller senare.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Vad händer om min användarens lösenord har upphört att gälla och de försöker logga in med hjälp av direktautentisering?

Om du har konfigurerat [tillbakaskrivning av lösenord](../authentication/concept-sspr-writeback.md) för en viss användare, och om användaren loggar in med hjälp av direktautentisering, de kan ändra eller återställa sina lösenord. Lösenorden som skrivs tillbaka till den lokala Active Directory som förväntat.

Om du inte har konfigurerat tillbakaskrivning av lösenord för en viss användare eller om användaren inte har en giltig Azure AD-licens, användaren kan inte uppdatera sina lösenord i molnet. De kan inte uppdatera sina lösenord, även om deras lösenord har upphört att gälla. I stället ser användaren det här meddelandet: ”din organisation tillåter inte att du ändrar ditt lösenord på den här platsen. Uppdatera den enligt den metod som din organisation rekommenderar eller be din administratör om du behöver hjälp ”. Användaren eller administratören måste återställa sina lösenord i den lokala Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hur direktautentisering skyddar dig mot lösenord brute force-attacker?

[Information om Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Vad direkt Autentiseringsagenter kommunicerar via port 80 och 443?

- Autentisering-agenter gör HTTPS-begäranden via port 443 för alla åtgärder för funktionen.
- Autentisering-agenter gör HTTP-förfrågningar via port 80 för att ladda ned de SSL listor över återkallade certifikat (CRL).

     >[!NOTE]
     >De senaste uppdateringarna minskar antalet portar som kräver funktionen. Om du har äldre versioner av Azure AD Connect eller -Autentiseringsagenten kan ha dessa portar öppna samt: 5671, 8080, 9090, 9091, 9350, 9352 och 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Autentiseringsagenter direktautentisering kan kommunicera via en utgående webbproxyserver?

Ja. Om Web Proxy Auto-Discovery (WPAD) är aktiverad i din lokala miljö, försöker Autentiseringsagenter automatiskt hitta och använda en proxyserver i nätverket.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kan jag installera två eller flera Autentiseringsagenter för direkt på samma server?

Nej, kan du endast installera en Autentiseringsagenten för direktautentisering på en enskild server. Om du vill konfigurera direktautentisering för hög tillgänglighet, [följa instruktionerna här](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Hur tar jag bort en Autentiseringsagenten för direktautentisering?

Så länge en Autentiseringsagenten för direktautentisering körs, förblir aktiv och kontinuerligt hanterar användarbegäranden logga in. Om du vill avinstallera en Agent för autentisering går du till **Kontrollpanelen -> program -> program och funktioner** och avinstallera både den **Microsoft Azure AD Connect-Autentiseringsagenten** och  **Microsoft Azure AD Connect-agenten Updater** program.

Om du markerar bladet direktautentisering på den [Azure Active Directory Administrationscenter](https://aad.portal.azure.com) när du har slutfört föregående steg, ser du autentiseringsagent dyker som **inaktiv**. Det här är _förväntat_. Autentisering-agenten släpps automatiskt i listan efter några dagar.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Jag använder redan AD FS för att logga in på Azure AD. Hur växlar jag det till direktautentisering?

Om du migrerar från AD FS (eller andra tekniker för federation) till direktautentisering, rekommenderar vi att du följer våra detaljerad Distributionsguide publicerade [här](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true).

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Kan jag använda direktautentisering i en Active Directory-miljö med flera skogar?

Ja. Miljöer med Multi-Forest stöds om det finns skogsförtroenden mellan dina Active Directory-skogar och om routning är korrekt konfigurerad.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hur många direkt Autentiseringsagenter jag behöver installera?

Installera flera Autentiseringsagenter för vidarekoppling säkerställer [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Men det ger inte deterministisk belastningsutjämning mellan agenter för autentisering.

Överväg att högsta och genomsnittliga belastningen för inloggningsförfrågningar du förväntar dig att se på din klient. Som prestandamått, kan en enda autentiseringsagent hantera 300 och 400 autentiseringar per sekund på en standard processor i 4 kärnor, 16 GB RAM-servern.

Du kan beräkna nätverkstrafik, använder du följande storlek-riktlinjer:
- Varje begäran har en nyttolast på (0,5 k + 1 K * num_of_agents) byte. d.v.s. utgående data från Azure AD-autentisering-agenten. Här anger ”num_of_agents” antalet Autentiseringsagenter registrerad på din klient.
- Varje svar har en nyttolast på 1K byte. d.v.s. utgående data från agenten autentisering till Azure AD.

Två eller tre Autentiseringsagenter totalt är tillräckliga för hög tillgänglighet och kapacitet för de flesta kunder. Du bör installera Autentiseringsagenter nära domänkontrollanterna för att förbättra inloggningen svarstid.

>[!NOTE]
>Det finns en systemgränsen på 12 Autentiseringsagenter per klient.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Kan jag installera den första Autentiseringsagenten för direktautentisering på en server än den som kör Azure AD Connect?

Nej, det här scenariot är _inte_ stöds.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Varför behöver jag en endast molnbaserad globala administratörskonto för att aktivera direktautentisering

Vi rekommenderar att du aktiverar eller inaktiverar direktautentisering med ett globalt administratörskonto enbart i molnet. Lär dig mer om [att lägga till ett globalt administratörskonto molnbaserad](../active-directory-users-create-azure-portal.md). Göra det på så sätt säkerställer du att du inte blir utelåst från klientorganisationen.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hur kan jag inaktivera direktautentisering?

Kör Azure AD Connect-guiden och ändrar användarens inloggningsmetod från direktautentisering till en annan metod. Den här ändringen inaktiverar direktautentisering på klienten och avinstallerar agenten autentisering från servern. Du måste manuellt avinstallera den Autentiseringsagenter från de andra servrarna.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Vad händer när jag för att avinstallera en Autentiseringsagenten för direktautentisering?

Om du avinstallerar en Agent för autentisering av direkt från en server gör servern att sluta ta emot inloggningsförfrågningar. Om du vill undvika att skada funktionen för användare logga in på din klient, kontrollera att du har en annan Autentiseringsagenten körs innan du avinstallerar en Autentiseringsagenten för direktautentisering.

## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Läs mer om vilka scenarier som stöds och vilka som inte är.
- [Snabbstart](how-to-connect-pta-quick-start.md): komma igång på Azure AD-direktautentisering.
- [Migrera från AD FS till direktautentisering](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) -en detaljerad vägledning för att migrera från AD FS (eller andra tekniker för federation) till direktautentisering.
- [Smart kontoutelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smarta kontoutelåsning på din klient för att skydda användarkonton.
- [Teknisk djupdykning](how-to-connect-pta-how-it-works.md): Förstå hur funktionen direktautentisering fungerar.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direktautentisering.
- [Djupgående om säkerhet](how-to-connect-pta-security-deep-dive.md): få djupgående teknisk information om funktionen direktautentisering.
- [Azure AD sömlös SSO](how-to-connect-sso.md): Mer information om den här tilläggsfunktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktionbegäran.

