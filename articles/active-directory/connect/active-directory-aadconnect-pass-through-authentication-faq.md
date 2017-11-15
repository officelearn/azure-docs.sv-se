---
title: "Azure AD Connect: Direkt autentisering – vanliga frågor och svar | Microsoft Docs"
description: "Svar på vanliga frågor och svar om Azure Active Directory direkt-autentisering."
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
ms.openlocfilehash: 821d70ec7236e165d4f60ed6217c49d10de1cfc3
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory direkt-autentisering: Vanliga frågor och svar

I den här artikeln adressera vi vanliga frågor och svar om Azure Active Directory (AD Azure) direkt-autentisering. Kontrollera för nytt innehåll.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>Vilket av Azure AD-inloggning metoderna - direkt-autentisering, lösenord hash-synkronisering och Active Directory Federation Services (AD FS) - ska jag välja?

Det beror på din lokala miljö och organisatoriska krav. Granska den här artikeln för en [jämförelse av de olika Azure AD-inloggning metoderna](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>Är direkt autentisering en kostnadsfri funktion?

Direkt-autentisering är en kostnadsfri funktion och du behöver inte några betald utgåvor av Azure AD för att använda den.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>Är direkt-autentisering finns i [Microsoft Cloud Tyskland](http://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/)?

Nej, direkt autentisering är endast tillgänglig i hela världen instans av Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-access-azure-portalmd-work-with-pass-through-authentication"></a>Har [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) fungerar med direkt autentisering?

Ja, alla funktioner för villkorlig åtkomst, inklusive Azure Multi-Factor Authentication fungerar med direkt-autentisering.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Stöder direkt autentisering ”Alternativt ID” som användarnamn, i stället för ”userPrincipalName”?

Ja. Direkt-autentiseringen stöder `Alternate ID` som användarnamnet som konfigurerades i Azure AD Connect enligt [här](active-directory-aadconnect-get-started-custom.md). Inte alla Office 365-program stöder `Alternate ID`. Finns det specifika programmet dokumentationen för support-instruktionen.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Synkronisering av lösenords-Hash fungera som reserv direkt autentisering?

Nej, direkt autentisering _inte_ automatiskt redundans till hash-synkronisering av lösenord. Det fungerar endast som en reserv för [scenarier som inte stöder direkt autentiseringen idag](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Om du vill undvika användaren logga in fel, bör du konfigurera direkt autentisering för [hög tillgänglighet](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kan jag installera en [Azure AD Application Proxy](../active-directory-application-proxy-get-started.md) connector på samma server som en direkt autentiseringsagent?

Ja, den här konfigurationen stöds med rebranded versioner av autentiseringsagent direkt (versioner 1.5.193.0 eller senare).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Vilka versioner av Azure AD Connect och direkt autentiseringsagent behöver du?

Du behöver version 1.1.486.0 eller senare för Azure AD Connect och 1.5.58.0 eller senare för autentiseringsagent direkt för den här funktionen ska fungera. All programvara ska installeras på servrar med Windows Server 2012 R2 eller senare.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>Vad händer om min användarens lösenord har upphört att gälla och de försöker logga in med hjälp av direkt autentisering?

Om du har konfigurerat [tillbakaskrivning av lösenord](../active-directory-passwords-update-your-own-password.md) för en viss användare, och om användaren loggar in med hjälp av direkt-autentisering, de kan ändra eller återställa sina lösenord. Lösenord skrivs tillbaka till lokala Active Directory som förväntat.

Men om tillbakaskrivning av lösenord inte är konfigurerad för en viss användare eller om användaren inte har en giltig Azure AD-licens, användaren går inte att uppdatera sitt lösenord i molnet. De kan inte uppdatera sina lösenord, även om deras lösenord har upphört att gälla. Användaren i stället ser meddelandet: ”din organisation tillåter inte att uppdatera ditt lösenord på den här platsen. Uppdatera den enligt den metod som rekommenderas av din organisation, eller be administratören om du behöver hjälp ”. Användaren eller administratören har att återställa sina lösenord i din lokala Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hur direkt autentisering skyddar du mot brute force-attacker lösenord?

Läs [i den här artikeln](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) för mer information.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Vad direkt autentisering agenter kommunicerar via portarna 80 och 443?

- Autentisering agenter begär HTTPS via port 443 för alla åtgärder för funktionen.
- Autentisering agenter begär HTTP via port 80 för att hämta SSL listor över återkallade certifikat.

     >[!NOTE]
     >I de senaste uppdateringarna minskar vi antalet portar som krävs av funktionen. Om du har äldre versioner av Azure AD Connect eller autentisering agenten att dessa portar hålls också öppna: 5671, 8080, 9090, 9091, 9350, 9352 och 10100 10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Direkt-autentisering agenter kan kommunicera via en utgående webbproxyserver?

Ja. Om WPAD (Web Proxy Auto-Discovery) är aktiverad i din lokala miljö, försöker autentisering-agenter automatiskt hitta och använda en proxyserver i nätverket.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kan jag installera två eller flera direkt autentisering agenter på samma server?

Nej, kan du endast installera en Agent för direkt-autentisering på en enskild server. Om du vill konfigurera direkt autentisering för hög tillgänglighet, följer du instruktionerna i det här [artikel](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) i stället.

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>Jag har redan använder Active Directory Federation Services (AD FS) för Azure AD-inloggning. Hur jag växlar den direkt autentisering?

Om du konfigurerat AD FS som din inloggningsmetod med hjälp av Azure AD Connect-guiden, ändrar du metoden för användaren direkt-autentisering. Den här ändringen kan direkt autentisering för innehavaren och konverterar _alla_ externa domäner i hanterade domäner. Alla efterföljande inloggning förfrågningar i din klient hanteras av direkt-autentisering. För närvarande, går det inte stöds i Azure AD Connect att använda en kombination av AD FS och direkt-autentisering mellan domäner.

Om AD FS har konfigurerats som inloggningsmetod _utanför_ i Azure AD Connect-guiden Ändra metoden för användare till direkt-autentisering (från alternativet ”konfigurerar inte”). Den här ändringen gör direkt autentisering för innehavaren. Men alla externa domäner fortsätta att använda AD FS för inloggning. Använda PowerShell för att konvertera manuellt vissa eller alla dessa externa domäner till hanterade domäner. Efter det att alla inloggning-förfrågningar på hanterade domäner (_endast_) som hanteras av direkt-autentisering.

>[!IMPORTANT]
>Direkt-autentisering kan inte hantera inloggning för endast molnbaserad Azure AD-användare.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>Kan jag använda direkt autentisering i en miljö med flera skogar AD?

Ja. Miljöer med flera skogar stöds om det finns skogsförtroenden mellan AD-skogar och om routning är korrekt konfigurerad.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hur många direkt autentisering agenter jag behöver installera?

Installera flera direkt autentisering agenter garanterar [hög tillgänglighet](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). Men det inte omfattar deterministiska belastningsutjämning mellan agenter för autentisering.

Överväg belastning och genomsnittlig belastning inloggning begäranden som du förväntar dig att se på din klient. En enda autentiseringsagent kan hantera 300,000 400 000 autentiseringar per sekund på en standard 4 kärnor CPU, server med 16 GB RAM-minne som en prestandamått. Två eller tre autentisering agenter totalt är tillräckliga för hög tillgänglighet och kapacitet för de flesta kunder.

Vi rekommenderar att du installerar agenter autentisering nära domänkontrollanterna för att förbättra inloggningen svarstid.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Kan jag installera den första autentiseringsagent direkt på en server än den som kör Azure AD Connect?

Nej, det här scenariot är _inte_ stöds.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Hur många agenter för direkt-autentisering ska installera?

Vi rekommenderar att:

- Du kan installera två eller tre autentisering agenter totalt. Den här konfigurationen är tillräcklig för de flesta kunder.
- Du installerar agenter för autentisering på dina domänkontrollanter (eller som nära dem som möjligt) att förbättra inloggningen svarstid.
- Du kan kontrollera att servrarna (där autentisering agenter är installerade) har lagts till samma AD-skog som de användare vars lösenord behöver verifieras.

>[!NOTE]
>Det finns en systembegränsning 12 autentisering agenter per klient.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hur kan jag inaktivera direkt autentisering?

Kör Azure AD Connect-guiden och ändra metoden för användare från direkt autentisering till en annan metod. Den här ändringen inaktiverar direkt autentisering för innehavaren och avinstallerar agenten autentisering från servern. Du måste avinstallera autentisering agenter manuellt från andra servrar.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Vad händer när jag för att avinstallera en Agent för direkt-autentisering?

Avinstallera en Agent för autentisering av direkt från en server gör att sluta acceptera inloggningsförfrågningar. Se till att du har en annan autentisering agenten som körs innan du utför den här åtgärden för att undvika att bryta användare logga in på din klient.

## <a name="next-steps"></a>Nästa steg
- [**Aktuella begränsningar** ](active-directory-aadconnect-pass-through-authentication-current-limitations.md) – Lär dig vilka scenarier som stöds och vilka som inte är.
- [**Snabbstart** ](active-directory-aadconnect-pass-through-authentication-quick-start.md) – komma igång och körs direkt i Azure AD-autentisering.
- [**Smartkort kontoutelåsning** ](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) -konfigurerar Smart kontoutelåsning kapaciteten på din klient för att skydda användarkonton.
- [**Tekniska ingående** ](active-directory-aadconnect-pass-through-authentication-how-it-works.md) -förstå hur funktionen fungerar.
- [**Felsöka** ](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Lär dig att lösa vanliga problem med funktionen.
- [**Säkerhet ingående** ](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) -ytterligare djupt teknisk information om funktionen.
- [**Azure AD sömlös SSO** ](active-directory-aadconnect-sso.md) -mer information om den här funktionen.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för arkivering av nya funktioner som efterfrågas.
