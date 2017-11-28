---
title: "Azure AD Connect: Direkt autentisering – vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor och svar om Azure Active Directory direkt-autentisering"
services: active-directory
keywords: "Azure AD Connect direkt-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Single Sign-on"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: d6a405f7245bf1b9635872efd0e29f8361d6a2f6
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory direkt-autentisering: Vanliga frågor och svar

Den här artikeln tar upp vanliga frågor och svar om Azure Active Directory (AD Azure) direkt-autentisering. Behåll Kontrollera tillbaka om uppdaterat innehåll.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Vilka metoder för att logga in på Azure AD, direkt-autentisering, lösenord hash-synkronisering och Active Directory Federation Services (AD FS), ska jag välja?

Det beror på din lokala miljö och organisatoriska krav. Granska de [Azure AD Connect användaren inloggningsalternativ](active-directory-aadconnect-user-signin.md) artikel för en jämförelse av olika Azure AD-inloggning metoder.

## <a name="is-pass-through-authentication-a-free-feature"></a>Är direkt autentisering en kostnadsfri funktion?

Direkt-autentisering är en funktion som ledigt. Du behöver inte några betald utgåvor av Azure AD för att använda den.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Är direkt-autentisering finns i den [Tyskland för Microsoft Azure-molnet](http://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/)?

Nej. Direkt-autentisering är endast tillgängligt i den globala instansen av Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Har [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) fungerar med direkt autentisering?

Ja. Alla funktioner för villkorlig åtkomst, inklusive Azure Multi-Factor Authentication fungerar med direkt-autentisering.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Stöder direkt autentisering ”Alternativt ID” som användarnamn, i stället för ”userPrincipalName”?

Ja. Direkt-autentiseringen stöder `Alternate ID` som användarnamnet som konfigurerades i Azure AD Connect. Mer information finns i [anpassad installation av Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Inte alla Office 365-program stöder `Alternate ID`. Finns det specifika programmet dokumentationen support-instruktion.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Synkronisering av lösenords-hash fungera som reserv direkt autentisering?

Nej. Direkt-autentisering _inte_ automatiskt redundans till hash-synkronisering av lösenord. Det fungerar endast som en reserv för [scenarier som inte stöder direkt autentiseringen idag](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Om du vill undvika användaren logga in fel, bör du konfigurera direkt autentisering för [hög tillgänglighet](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kan jag installera en [Azure AD Application Proxy](../active-directory-application-proxy-get-started.md) connector på samma server som en direkt autentiseringsagent?

Ja. Rebranded versioner av direkt autentiseringsagent, version 1.5.193.0 eller senare, stöder den här konfigurationen.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Vilka versioner av Azure AD Connect och direkt autentiseringsagent behöver du?

För den här funktionen ska fungera måste version 1.1.486.0 eller senare för Azure AD Connect och 1.5.58.0 eller senare för autentiseringsagent direkt. Installera alla program på servrar med Windows Server 2012 R2 eller senare.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Vad händer om min användarens lösenord har upphört att gälla och de försöker logga in med hjälp av direkt autentisering?

Om du har konfigurerat [tillbakaskrivning av lösenord](../active-directory-passwords-update-your-own-password.md) för en viss användare, och om användaren loggar in med hjälp av direkt-autentisering, de kan ändra eller återställa sina lösenord. Lösenord skrivs tillbaka till lokala Active Directory som förväntat.

Om du inte har konfigurerat tillbakaskrivning av lösenord för en specifik användare eller om användaren inte har en giltig Azure AD-licens, användaren går inte att uppdatera sitt lösenord i molnet. De kan inte uppdatera sina lösenord, även om deras lösenord har upphört att gälla. Användaren i stället ser meddelandet: ”din organisation tillåter inte att uppdatera ditt lösenord på den här platsen. Uppdatera enligt den metod som rekommenderas av din organisation eller be administratören om du behöver hjälp ”. Användaren eller administratören återställa sina lösenord i lokala Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hur direkt autentisering skyddar du mot lösenord brute force-attacker?

Läs [Azure Active Directory direkt-autentisering: Smart kontoutelåsning](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) för mer information.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Vad direkt autentisering agenter kommunicerar via portarna 80 och 443?

- Autentisering agenter begär HTTPS via port 443 för alla åtgärder för funktionen.
- Autentisering agenter begär HTTP via port 80 för att ladda ned de SSL listor över återkallade certifikat (CRL).

     >[!NOTE]
     >De senaste uppdateringarna minskar antalet portar som kräver funktionen. Om du har äldre versioner av Azure AD Connect eller autentisering agenten att dessa portar hålls öppna samt: 5671, 8080, 9090, 9091, 9350, 9352 och 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Direkt-autentisering agenter kan kommunicera via en utgående webbproxyserver?

Ja. Om Web Proxy Auto-Discovery (WPAD) är aktiverad i din lokala miljö, försöker autentisering-agenter automatiskt hitta och använda en proxyserver i nätverket.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kan jag installera två eller flera direkt autentisering agenter på samma server?

Nej, kan du endast installera en Agent för direkt-autentisering på en enskild server. Om du vill konfigurera direkt autentisering för hög tillgänglighet, följ instruktionerna i [Azure Active Directory direkt-autentisering: Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Jag använder redan AD FS för att logga in på Azure AD. Hur jag växlar den direkt autentisering?

Om du har konfigurerat AD FS som metod för att logga in via Azure AD Connect-guiden kan du ändra metod som användaren använder för att logga in direkt-autentisering. Den här ändringen kan direkt autentisering för innehavaren och konverterar _alla_ externa domäner till hanterade domäner. Direkt-autentisering hanterar alla efterföljande begäranden om att logga in på din klient. För närvarande, går det inte stöds i Azure AD Connect att använda en kombination av AD FS och direkt-autentisering mellan domäner.

Om AD FS har konfigurerats som metod för att logga in _utanför_ Azure AD Connect-guiden Ändra användare logga in metod direkt-autentisering. Du kan göra den här ändringen från den **konfigurerar inte** alternativet. Den här ändringen gör direkt autentisering för innehavaren, men alla externa domäner fortsätter att använda AD FS för inloggning. Använda PowerShell för att konvertera manuellt vissa eller alla dessa externa domäner till hanterade domäner. När du har ändrat, *endast* direkt autentisering hanterar alla förfrågningar för att logga in på de hanterade domänerna.

>[!IMPORTANT]
>Direkt-autentisering kan inte hantera inloggning för endast molnbaserad Azure AD-användare.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Kan jag använda direkt autentisering i en Active Directory-miljö med flera skogar?

Ja. Miljöer med flera skogar stöds om det finns skogsförtroenden mellan Active Directory-skogar och om routning är korrekt konfigurerad.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hur många direkt autentisering agenter jag behöver installera?

Installera flera direkt autentisering agenter garanterar [hög tillgänglighet](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Men den ger inte deterministisk belastningsutjämning mellan agenter för autentisering.

Överväg belastning och genomsnittlig belastning inloggning begäranden som du förväntar dig att se på din klient. Som en benchmark kan en enda autentiseringsagent hantera 300,000 400 000 autentiseringar per sekund på en standard 4 kärnor CPU, 16 GB RAM-server. Två eller tre autentisering agenter totalt är tillräckliga för hög tillgänglighet och kapacitet för de flesta kunder.

Du bör installera autentisering agenter nära domänkontrollanterna för att förbättra inloggningen svarstid.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Kan jag installera den första autentiseringsagent direkt på en server än den som kör Azure AD Connect?

Nej, det här scenariot är _inte_ stöds.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Hur många agenter för direkt-autentisering ska installera?

Vi rekommenderar att:

- Du kan installera två eller tre autentisering agenter totalt. Den här konfigurationen är tillräcklig för de flesta kunder.
- Du installerar agenter för autentisering på dina domänkontrollanter (eller som nära dem som möjligt) att förbättra inloggningen svarstid.
- Du se till att du lägger till de servrar där du installerade agenterna autentisering i samma Active Directory-skog som de användare vars lösenord du måste validera.

>[!NOTE]
>Det finns en systembegränsning 12 autentisering agenter per klient.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hur kan jag inaktivera direkt autentisering?

Kör Azure AD Connect-guiden och ändra metoden för användare från direkt autentisering till en annan metod. Den här ändringen inaktiverar direkt autentisering för innehavaren och avinstallerar agenten autentisering från servern. Du måste avinstallera autentisering agenter manuellt från de andra servrarna.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Vad händer när jag för att avinstallera en Agent för direkt-autentisering?

Om du avinstallerar en Agent för autentisering av direkt från en server gör servern att sluta acceptera inloggningsförfrågningar. Se till att du har en annan autentiseringsagent körs innan du avinstallerar en Agent för direkt-autentisering för att undvika att funktionen för användare logga in på din klient.

## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](active-directory-aadconnect-pass-through-authentication-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är.
- [Snabbstart](active-directory-aadconnect-pass-through-authentication-quick-start.md): komma igång direkt i Azure AD för autentisering.
- [Smartkort kontoutelåsning](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): Lär dig hur du konfigurerar Smart kontoutelåsning kapaciteten på din klient skydda användarkonton.
- [Tekniska ingående](active-directory-aadconnect-pass-through-authentication-how-it-works.md): Förstå hur funktionen direkt autentisering fungerar.
- [Felsöka](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): Lär dig att lösa vanliga problem med funktionen direkt-autentisering.
- [Säkerhet ingående](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): avancerad teknisk information om funktionen direkt-autentisering.
- [Azure AD sömlös SSO](active-directory-aadconnect-sso.md): Lär dig mer om den här funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): använda Azure Active Directory-forumet till filen nya funktioner som efterfrågas.

