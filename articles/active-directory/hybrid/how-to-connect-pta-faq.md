---
title: 'Azure AD Connect: Direktautentisering – Vanliga frågor | Microsoft-dokument'
description: Svar på vanliga frågor om Azure Active Directory Direktautentisering
services: active-directory
keywords: Azure AD Connect Pass-through-autentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, Enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59bf7ae5914f5cc886d95f25b36abccfdf09c4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331276"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Direktautentisering i Azure Active Directory: Vanliga frågor och svar

Den här artikeln tar upp vanliga frågor om Azure Active Directory (Azure AD) Direktautentisering. Fortsätt att kontrollera tillbaka för uppdaterat innehåll.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Vilken av metoderna för att logga in på Azure AD, Direktautentisering, lösenordsh hash-synkronisering och AD FS (Active Directory Federation Services), ska jag välja?

Granska [den här guiden](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) för en jämförelse av de olika Azure AD-inloggningsmetoderna och hur du väljer rätt inloggningsmetod för din organisation.

## <a name="is-pass-through-authentication-a-free-feature"></a>Är direktautentisering en kostnadsfri funktion?

Direktautentisering är en kostnadsfri funktion. Du behöver inga betalda utgåvor av Azure AD för att använda den.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloud-and-the-microsoft-azure-government-cloud"></a>Är direktautentisering tillgänglig i [Microsoft Azure Germany-molnet](https://www.microsoft.de/cloud-deutschland) och [Microsoft Azure Government-molnet?](https://azure.microsoft.com/features/gov/)

Nej. Direktautentisering är endast tillgängligt i den globala instansen av Azure AD.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Fungerar [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) med direktautentisering?

Ja. Alla funktioner för villkorlig åtkomst, inklusive Azure Multi-Factor Authentication, fungerar med direktautentisering.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Stöder direktautentisering "Alternativt ID" som användarnamn i stället för "userPrincipalName"?
Logga in med ett icke-UPN-värde, till exempel ett alternativt e-postmeddelande, testas för närvarande i privat förhandsversion för både direktautentisering (PTA) och lösenordshim sync (PHS).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Fungerar synkronisering av lösenord hash som en återgång till direktautentisering?

Nej. Direktautentisering _reder inte_ automatiskt över till synkronisering av lösenordsh hash. Om du vill undvika inloggningsfel för användare bör du konfigurera direktautentisering för [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Vad händer när jag växlar från synkronisering av lösenordsh hash till direktautentisering?

När du använder Azure AD Connect för att växla inloggningsmetod från synkronisering av lösenord hash till direktautentisering blir direktautentisering den primära inloggningsmetoden för användare i hanterade domäner. Observera att alla användares lösenordstecken som tidigare synkroniserats med synkronisering av lösenord hash fortfarande lagras på Azure AD.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kan jag installera en [Azure AD Application Proxy-anslutning](../manage-apps/application-proxy.md) på samma server som en direktautentiseringsagent?

Ja. De omdöpta versionerna av direktautentiseringsagenten, version 1.5.193.0 eller senare, stöder den här konfigurationen.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Vilka versioner av Azure AD Connect och Pass-through Authentication Agent behöver du?

För att den här funktionen ska fungera behöver du version 1.1.750.0 eller senare för Azure AD Connect och 1.5.193.0 eller senare för direktautentiseringsagenten. Installera all programvara på servrar med Windows Server 2012 R2 eller senare.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Vad händer om användarens lösenord har gått ut och de försöker logga in med hjälp av direktautentisering?

Om du har konfigurerat [tillbakaskrivning](../authentication/concept-sspr-writeback.md) av lösenord för en viss användare, och om användaren loggar in med hjälp av direktautentisering, kan de ändra eller återställa sina lösenord. Lösenorden skrivs tillbaka till lokala Active Directory som förväntat.

Om du inte har konfigurerat tillbakaskrivning av lösenord för en viss användare eller om användaren inte har tilldelats en giltig Azure AD-licens kan användaren inte uppdatera sitt lösenord i molnet. De kan inte uppdatera sitt lösenord, även om lösenordet har upphört att gälla. Användaren ser i stället det här meddelandet: "Din organisation tillåter inte att du uppdaterar ditt lösenord på den här webbplatsen. Uppdatera den enligt den metod som rekommenderas av din organisation, eller fråga din administratör om du behöver hjälp." Användaren eller administratören måste återställa sitt lösenord i lokal Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hur skyddar pass-through-autentisering dig mot brute-force lösenordsattacker?

[Läs information om Smart Lockout](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Vad kommunicerar direktautentiseringsagenter över portarna 80 och 443?

- Autentiseringsagenterna gör HTTPS-begäranden över port 443 för alla funktionsåtgärder.
- Autentiseringsagenterna gör HTTP-begäranden via port 80 för att hämta listorna över återkalla TLS/SSL-certifikat (CRLs).

     >[!NOTE]
     >De senaste uppdateringarna minskade antalet portar som funktionen kräver. Om du har äldre versioner av Azure AD Connect eller autentiseringsagenten håller du även dessa portar öppna: 5671, 8080, 9090, 9091, 9350, 9352 och 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Kan direktautentiseringsagenterna kommunicera via en utgående webbproxyserver?

Ja. Om WPAD (Web Proxy Auto-Discovery) är aktiverat i din lokala miljö försöker autentiseringsagenter automatiskt hitta och använda en webbproxyserver i nätverket.

Om du inte har WPAD i din miljö kan du lägga till proxyinformation (som visas nedan) så att en direktautentiseringsagent kan kommunicera med Azure AD:
- Konfigurera proxyinformation i Internet Explorer innan du installerar direktautentiseringsagenten på servern. På så sätt kan du slutföra installationen av autentiseringsagenten, men den visas fortfarande som **inaktiv** på administratörsportalen.
- På servern navigerar du till "C:\Program Files\Microsoft Azure AD Connect Authentication Agent".
- Redigera konfigurationsfilen "AzureADConnectAuthenticationAgentService" och lägg till\:följande rader (ersätt "http //contosoproxy.com:8080" med din faktiska proxyadress):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kan jag installera två eller flera direktautentiseringsagenter på samma server?

Nej, du kan bara installera en direktautentiseringsagent på en enda server. Om du vill konfigurera direktautentisering för hög tillgänglighet [följer du instruktionerna här](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Måste jag förnya certifikat som används manuellt av direktautentiseringsagenter?

Kommunikationen mellan varje direktautentiseringsagent och Azure AD skyddas med hjälp av certifikatbaserad autentisering. Dessa [certifikat förnyas automatiskt med några månaders mellanrum av Azure AD](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Du behöver inte förnya dessa certifikat manuellt. Du kan rensa äldre utgångna certifikat efter behov.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Hur tar jag bort en direktautentiseringsagent?

Så länge en direktautentiseringsagent körs förblir den aktiv och hanterar kontinuerligt användaråtkomstbegäranden. Om du vill avinstallera en autentiseringsagent går du till **Kontrollpanelen -> Program -> program och funktioner** och avinstallerar både **Microsoft Azure AD Connect Authentication Agent** och Microsoft Azure AD Connect **Agent Updater-programmen.**

Om du kontrollerar bladet Direktautentisering i [Administrationscentret](https://aad.portal.azure.com) för Azure Active Directory efter att du har slutfört föregående steg visas autentiseringsagenten som visas som **inaktiv**. Detta _förväntas_. Autentiseringsagenten tas automatiskt bort från listan efter några dagar.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Jag använder redan AD FS för att logga in på Azure AD. Hur växlar jag den till direktautentisering?

Om du migrerar från AD FS (eller annan federationsteknik) till Direktautentisering rekommenderar vi starkt att du följer vår detaljerade distributionsguide som publiceras [här.](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Kan jag använda direktautentisering i en Active Directory-miljö med flera skogar?

Ja. Flerskogsmiljöer stöds om det finns skogsförtroenden mellan Active Directory-skogarna och om namnsuffixdirigeringen är korrekt konfigurerad.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Ger direktautentisering belastningsutjämning mellan flera autentiseringsagenter?

Nej, installation av flera direktautentiseringsagenter säkerställer endast [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Det ger inte deterministisk belastningsutjämning mellan autentiseringsagenterna. Alla autentiseringsagenter (slumpmässigt) kan bearbeta en viss begäran om användarloggning.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hur många direktautentiseringsagenter behöver jag installera?

Installera flera Direktautentiseringsagenter säkerställer [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Men det ger inte deterministisk belastningsutjämning mellan autentiseringsagenterna.

Tänk på den högsta och genomsnittliga belastningen av inloggningsbegäranden som du förväntar dig att se på din klientorganisation. Som ett riktmärke kan en enda autentiseringsagent hantera 300 till 400 autentiseringar per sekund på en vanlig 4-kärnig CPU, 16 GB RAM-server.

Om du vill uppskatta nätverkstrafiken använder du följande storleksvägledning:
- Varje begäran har en nyttolaststorlek på (0,5 K + 1K * num_of_agents) byte; dvs data från Azure AD till autentiseringsagenten. Här anger "num_of_agents" antalet autentiseringsagenter som är registrerade på din klientorganisation.
- Varje svar har en nyttolaststorlek på 1K-byte. dvs data från autentiseringsagenten till Azure AD.

För de flesta kunder är totalt två eller tre autentiseringsagenter tillräckliga för hög tillgänglighet och kapacitet. Du bör installera autentiseringsagenter nära domänkontrollanterna för att förbättra inloggningstiden.

>[!NOTE]
>Det finns en systemgräns på 40 autentiseringsagenter per klient.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Kan jag installera den första direktautentiseringsagenten på en annan server än den som kör Azure AD Connect?

Nej, det här scenariot stöds _inte._

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Varför behöver jag ett globalt administratörskonto endast för molnet för att aktivera direktautentisering?

Vi rekommenderar att du aktiverar eller inaktiverar direktautentisering med ett globalt administratörskonto för molnet. Läs mer om hur du [lägger till ett globalt administratörskonto för molnet](../active-directory-users-create-azure-portal.md). Att göra det på detta sätt säkerställer att du inte blir utelåst från din klient.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hur inaktiverar jag direktautentisering?

Kör Azure AD Connect-guiden och ändra inloggningsmetoden för användare från direktautentisering till en annan metod. Den här ändringen inaktiverar direktautentisering på klienten och avinstallerar autentiseringsagenten från servern. Du måste avinstallera autentiseringsagenterna manuellt från de andra servrarna.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Vad händer när jag avinstallerar en direktautentiseringsagent?

Om du avinstallerar en direktautentiseringsagent från en server gör det att servern slutar acceptera inloggningsbegäranden. För att undvika att bryta användarens inloggningsfunktion på din klientorganisation, se till att du har en annan autentiseringsagent som körs innan du avinstallerar en direktautentiseringsagent.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Jag har en äldre klient som ursprungligen konfigurerades med AD FS.  Vi har nyligen migrerat till PTA men nu ser vi inte våra UPN-ändringar synkroniseras med Azure AD.  Varför synkroniseras inte våra UPN-ändringar?

S: Under följande omständigheter kanske dina lokala UPN-ändringar inte synkroniseras om:

- Din Azure AD-klient skapades före den 15 juni 2015
- Du var först federerad med din Azure AD-klient med AD FS för autentisering
- Du bytte till att ha hanterade användare som använder PTA som autentisering

Detta beror på att standardbeteendet för klienter som skapats före den 15 juni 2015 var att blockera UPN-ändringar.  Om du behöver avblockera UPN-ändringar måste du köra följande PowerShell-cmdlt:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Klienter som skapats efter den 15 juni 2015 har standardbeteendet för att synkronisera UPN-ändringar.   



## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är det.
- [Snabbstart:](how-to-connect-pta-quick-start.md)Komma igång med Azure AD Pass-through-autentisering.
- [Migrera från AD FS till Direktautentisering](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – En detaljerad guide för att migrera från AD FS (eller annan federationsteknik) till direktautentisering.
- [Smart Utelåsning:](../authentication/howto-password-smart-lockout.md)Lär dig hur du konfigurerar smart utelåsningsfunktionen på din klient för att skydda användarkonton.
- [Teknisk djupdykning](how-to-connect-pta-how-it-works.md): Förstå hur funktionen Direktautentisering fungerar.
- [Felsöka:](tshoot-connect-pass-through-authentication.md)Lär dig hur du löser vanliga problem med direktautentiseringsfunktionen.
- [Djupdykning i säkerhet:](how-to-connect-pta-security-deep-dive.md)Få djup teknisk information om funktionen Direktautentisering.
- [Azure AD Seamless SSO](how-to-connect-sso.md): Läs mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory Forum för att fila nya funktionsbegäranden.

