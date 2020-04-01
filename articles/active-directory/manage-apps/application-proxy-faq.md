---
title: Vanliga frågor och svar på Azure AD-programproxy | Microsoft-dokument
description: Lär dig svar på vanliga frågor och svar om hur du använder Azure AD Application Proxy för att publicera interna, lokala program till fjärranvändare.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 31587777ba22dd8b4cebf81f0ff98979bb30fade
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410720"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Vanliga frågor och svar om Vanliga frågor och svar om Active Directory-programproxy (Azure AD)

Den här sidan besvarar vanliga frågor om Azure Active Directory (Azure AD) Application Proxy.

## <a name="enabling-azure-ad-application-proxy"></a>Aktivera Azure AD-programproxy

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Vilken licens krävs för att använda Azure AD Application Proxy?

Om du vill använda Azure AD Application Proxy måste du ha en Azure AD Premium P1- eller P2-licens. Mer information om licensiering finns i [Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Varför är knappen "Aktivera programproxy nedtonad?

Kontrollera att du har minst en Azure AD Premium P1- eller P2-licens och en Azure AD Application Proxy Connector installerad. När du har installerat den första anslutningen aktiveras Azure AD Application Proxy-tjänsten automatiskt.

## <a name="connector-configuration"></a>Anslutningskonfiguration

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Kan Application Proxy Connector-tjänster köras i en annan användarkontext än standard?

Nej, det här scenariot stöds inte. Standardinställningarna är:

- Proxyanslutning för Microsoft AAD-program - WAPCSvc - Nätverkstjänst
- Microsoft AAD Application Proxy Connector Updater - WAPCUpdaterSvc - NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Min backend-program finns på flera webbservrar och kräver användarsession uthållighet (klibbighet). Hur uppnår jag sessionsens uthållighet? 

Rekommendationer finns i [Hög tillgänglighet och belastningsutjämning av programproxyanslutningar och program](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Kan jag placera en proxyenhet framåt mellan anslutningsservern/anslutningsservrarna och server för backend-program?

Nej, det här scenariot stöds inte. Endast anslutnings- och uppdateringstjänster kan konfigureras för att använda en vidarebefordran proxy för utgående trafik till Azure. Se [Arbeta med befintliga lokala proxyservrar](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Stöds TLS-avslutning (TLS/HTTPS-inspektion eller acceleration) på trafik från anslutningsservrarna till Azure?

Application Proxy Connector utför certifikatbaserad autentisering till Azure. TLS-avslutning (TLS/HTTPS-inspektion eller acceleration) bryter den här autentiseringsmetoden och stöds inte. Trafik från anslutningen till Azure måste kringgå alla enheter som utför TLS-avslutning.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Ska jag skapa ett dedikerat konto för att registrera kopplingen med Azure AD Application Proxy?

Det finns ingen anledning till det. Alla globala administratörs- eller programadministratörskonto fungerar. Autentiseringsuppgifterna som anges under installationen används inte efter registreringsprocessen. I stället utfärdas ett certifikat till kopplingen, som används för autentisering från den punkten och på.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Hur kan jag övervaka prestanda för Azure AD Application Proxy-anslutningen?

Det finns prestandaövervakarräknare som installeras tillsammans med kopplingen. Så här visar du dem:  

1. Välj **Start**, skriv "Perfmon" och tryck på RETUR.
2. Välj **Prestandaövervakare** och klicka på den gröna **+** ikonen.
3. Lägg till de Proxy connector-räknare för **Microsoft AAD-program** som du vill övervaka.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Måste Azure AD Application Proxy-anslutningen finnas i samma undernät som resursen?

Kopplingen behöver inte finnas i samma undernät. Den behöver dock namnmatchning (DNS, hosts-filen) till resursen och nödvändig nätverksanslutning (routning till resursen, portar öppna på resursen osv.). Rekommendationer finns i [Nätverkstopologiöverväganden när du använder Azure Active Directory Application Proxy](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Tillämpningskonfiguration

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Hur lång är standard- och "lång" backend-timeout? Kan tidsgränsen förlängas?

Standardlängden är 85 sekunder. Inställningen "lång" är 180 sekunder. Tidsgränsen kan inte förlängas.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Hur ändrar jag målsidan som mitt program läser in?

På sidan Programregistreringar kan du ändra startsidans URL till önskad extern WEBBADRESS för målsidan. Den angivna sidan läses in när programmet startas från Mina program eller Office 365 Portal. Konfigurationssteg finns i [Ange en anpassad startsida för publicerade appar med hjälp av Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Kan endast IIS-baserade program publiceras? Hur är det med webbprogram som körs på webbservrar som inte kommer från Windows? Måste anslutningen installeras på en server med IIS installerat?

Nej, det finns inget IIS-krav för program som publiceras. Du kan publicera webbprogram som körs på andra servrar än Windows Server. Du kanske inte kan använda förautentisering med en server som inte är Windows Server, beroende på om webbservern stöder Förhandla (Kerberos-autentisering). IIS krävs inte på servern där anslutningen är installerad.

## <a name="integrated-windows-authentication"></a>Integrerad Windows-autentisering

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>När ska jag använda metoden PrincipalsAllowedToDelegateToAccount när du ställer in Kerberos Constrained Delegation (KCD)?

Principen PrincipalsAllowedToDelegateToAccount används när anslutningsservrar finns i en annan domän än webbprogramtjänstkontot. Det kräver användning av resursbaserad begränsad delegering.
Om anslutningsservrarna och webbprogramtjänstkontot finns i samma domän kan du använda Active Directory - användare och datorer för att konfigurera delegeringsinställningarna för var och en av anslutningsmaskinkontona, så att de kan delegera till målsstunden.

Om anslutningsservrarna och webbprogramtjänstkontot finns i olika domäner används resursbaserad delegering. Delegeringsbehörigheterna konfigureras på målwebbservern och webbprogramtjänstkontot. Denna metod för begränsad delegering är relativt ny. Metoden introducerades i Windows Server 2012, som stöder delegering mellan domäner genom att låta resursägaren (webbtjänsten) styra vilka dator- och tjänstkonton som kan delegera till den. Det finns inget användargränssnitt som kan hjälpa till med den här konfigurationen, så du måste använda PowerShell.
Mer information finns i faktabladet [Förstå Kerberos Constrained Delegering med Programproxy](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Direktautentisering

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Kan jag använda principer för villkorlig åtkomst för program som publiceras med direktautentisering?

Principer för villkorlig åtkomst tillämpas endast för förbekdömda användare i Azure AD. Direktautentisering utlöser inte Azure AD-autentisering, så principer för villkorlig åtkomst kan inte tillämpas. Med direktautentisering måste MFA-principer implementeras på den lokala servern, om möjligt, eller genom att aktivera förautentisering med Azure AD Application Proxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Kan jag publicera ett webbprogram med autentiseringskrav för klientcertifikat?

Nej, det här scenariot stöds inte eftersom Programproxy avslutar TLS-trafik.  

## <a name="remote-desktop-gateway-publishing"></a>Publicering av fjärrskrivbordsgateway

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Hur publicerar jag fjärrskrivbordsgateway via Azure AD Application Proxy?

Se [Publicera fjärrskrivbord med Azure AD Application Proxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Kan jag använda Kerberos Constrained Delegation (Single Sign-On - Windows Integrated Authentication) i publiceringsscenariot för fjärrskrivbordsgateway?

Nej, det här scenariot stöds inte.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Mina användare använder inte Internet Explorer 11 och scenariot för förhandsautentisering fungerar inte för dem. Är detta väntat?

Ja, det är väntat. Scenariot för förautentisering kräver en ActiveX-kontroll som inte stöds i webbläsare från tredje part.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Stöds HTML5 (Remote Desktop Web Client)?

Nej, det här scenariot stöds för närvarande inte. Följ vårt Feedbackforum för [UserVoice](https://aka.ms/aadapuservoice) för uppdateringar om den här funktionen.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>När jag har konfigurerat scenariot före autentisering insåg jag att användaren måste autentisera två gånger: först i Azure AD-inloggningsformuläret och sedan i formuläret RDWeb-inloggning. Är detta väntat? Hur kan jag reducera detta till en inloggning?

Ja, det är väntat. Om användarens dator är Azure AD-ansluten loggar användaren automatiskt in på Azure AD. Användaren behöver bara ange sina autentiseringsuppgifter i formuläret RDWeb-inloggning.

## <a name="sharepoint-publishing"></a>SharePoint-publicering

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Hur publicerar jag SharePoint via Azure AD Application Proxy?

Se [Aktivera fjärråtkomst till SharePoint med Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Kan jag använda SharePoint-mobilappen (iOS/ Android) för att komma åt en publicerad SharePoint-server?

[SharePoint-mobilappen](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) stöder inte Azure Active Directory-förautentisering för närvarande.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Ad FS-publicering (Active Directory Federation Services) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Kan jag använda Azure AD Application Proxy som AD FS-proxy (som Proxy för webbprogram)?

Nej. Azure AD Application Proxy är utformad för att fungera med Azure AD och uppfyller inte kraven för att fungera som en AD FS-proxy.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Har WebSocket stöd arbete för andra program än QlikSense?

För närvarande är WebSocket-protokollstöd fortfarande i offentlig förhandsversion och det kanske inte fungerar för andra program. Vissa kunder har haft blandad framgång med WebSocket-protokollet med andra program. Om du testar sådana scenarier, skulle vi gärna höra dina resultat. Vänligen skicka oss aadapfeedback@microsoft.comdin feedback på .

Funktioner (Eventlogs, PowerShell och Fjärrskrivbordstjänster) i WAC (Windows Admin Center) eller HTML5 (Remote Desktop Web Client) fungerar inte via Azure AD Application Proxy för närvarande.

## <a name="link-translation"></a>Länköversättning

### <a name="does-using-link-translation-affect-performance"></a>Påverkar användning av Länköversättning prestanda?

Ja. Länköversättning påverkar prestanda. Application Proxy-tjänsten söker igenom programmet efter hårdkodade länkar och ersätter dem med deras respektive, publicerade externa webbadresser innan de presenteras för användaren. 

För bästa prestanda rekommenderar vi att du använder identiska interna och externa webbadresser genom att konfigurera [anpassade domäner](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Om det inte går att använda anpassade domäner kan du förbättra länköversättningsprestanda genom att använda tillägget Säker inloggning i Mina appar eller Microsoft Edge-webbläsare på mobilen. Se [Omdirigera hårdkodade länkar för appar som publiceras med Azure AD Application Proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Jokertecken

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Hur använder jag jokertecken för att publicera två program med samma anpassade domännamn men med olika protokoll, ett för HTTP och ett för HTTPS?

Det här scenariot stöds inte direkt. Alternativen för det här scenariot är:

1. Publicera både HTTP- och HTTPS-URL:erna som separata program med ett jokertecken, men ge var och en av dem en annan anpassad domän. Den här konfigurationen fungerar eftersom de har olika externa WEBBADRESSER.

2. Publicera HTTPS-URL:en via ett jokerteckenprogram. Publicera HTTP-programmen separat med hjälp av dessa PowerShell-programproxy-kortslutningar:
   - [Hantering av programproxyprogram](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Hantering av programproxyanslutningar](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
