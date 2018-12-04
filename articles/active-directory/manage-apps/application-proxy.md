---
title: Att tillhandahålla säker fjärråtkomst till lokala appar
description: Beskriver hur du använder Azure AD Application Proxy för att tillhandahålla säker fjärråtkomst till lokala appar.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 212628c0ec97524e91ab8eaeb766c3e405023aaf
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846172"
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Att tillhandahålla säker fjärråtkomst till lokala program

I dag vill anställda vara produktiva var som helst, när som helst och från valfri enhet. De vill arbeta med sina egna enheter, oavsett om de är bärbara datorer, surfplattor eller telefoner. Och de förväntar sig för att kunna komma åt sina program, både SaaS-appar i molnet och företagets appar lokalt. Att ge åtkomst till lokala program har traditionellt inneburit virtuella privata nätverk (VPN) eller demilitariserade zoner (DMZ). De här lösningarna är inte bara komplexa och svåra att skydda, utan de är även dyra att konfigurera och hantera.

Det finns ett bättre sätt!

En modern arbetsstyrka i mobilstyrt molnvärlden behöver en lösning för moderna fjärråtkomst. Azure AD Application Proxy är en funktion i Azure Active Directory som erbjuder fjärråtkomst som en tjänst. Det innebär att det är enkelt att distribuera, använda och hantera.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Vad är Azure Active Directory Application Proxy?
Azure AD Application Proxy ger enkel inloggning (SSO) och säker fjärråtkomst för webbprogram finns lokalt. Vissa appar som du vill publicera är SharePoint-webbplatser, Outlook Web Access eller några andra affärsappar som du har. Dessa lokala webbprogram är integrerade med Azure AD, samma identitets- och kontrollplattform som används av O365. Slutanvändarna kan komma åt dina lokala program på samma sätt som de har åtkomst till O365 och andra SaaS-appar som integreras med Azure AD. Du behöver inte ändra nätverkets infrastruktur eller kräva VPN att tillhandahålla den här lösningen för dina användare.

## <a name="why-is-application-proxy-a-better-solution"></a>Varför är programproxy en bättre lösning?
Azure AD Application Proxy ger en enkel, säker och kostnadseffektiv fjärråtkomst till lokala program.

Azure AD Application Proxy är:

* **Enkel**
   * Du behöver inte ändra eller uppdatera dina program att arbeta med Application Proxy. 
   * Dina användare får en konsekvent autentiseringsupplevelse. De kan använda MyApps-portalen för att få enkel inloggning till både SaaS-appar i molnet och dina appar lokalt. 
* **Skydda**
   * När du publicerar dina appar med Azure AD Application Proxy kan dra du nytta av omfattande auktoriseringskontroller och säkerhetsanalyser i Azure. Du får molnskala security och Azure säkerhetsfunktioner, t.ex. villkorlig åtkomst och tvåstegsverifiering verifiering.
   * Du behöver öppna några inkommande anslutningar via brandväggen för att ge dina användare fjärråtkomst. 
* **Kostnadseffektiv**
   * Programproxy fungerar i molnet, så att du kan spara tid och pengar. Lokala lösningar kräver normalt att du konfigurerar och underhåller DMZ-miljöer, edge-servrar eller andra komplexa infrastrukturer.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Vilken typ av programmen fungerar med Application Proxy?
Du kan använda Azure AD-programproxy för att komma åt olika typer av interna program:

* Webbprogram som använder [integrerad Windows-autentisering](application-proxy-configure-single-sign-on-with-kcd.md) för autentisering  
* Webbprogram som använder formulärbaserad eller [rubrikbaserad](application-proxy-configure-single-sign-on-with-ping-access.md) åtkomst  
* Web API: er som du vill exponera omfattande program på olika enheter  
* Program som ligger bakom en [fjärrskrivbordsgateway](application-proxy-integrate-with-remote-desktop-services.md)  
* Rich-klientprogram som är integrerade med Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Hur fungerar Application Proxy?
Det finns två komponenter som du behöver konfigurera om du vill göra Application Proxy fungerar: en koppling och en extern slutpunkt. 

Anslutningen är en förenklad agent som finns på en Windows-Server i ditt nätverk. Anslutningen underlättar trafikflödet från Application Proxy-tjänst i molnet till ditt program lokalt. Den använder endast utgående anslutningar så att du inte behöver öppna några ingående portar eller lägga något i Perimeternätverket. Anslutningarna är tillståndslösa och hämta information från molnet efter behov. Mer information om anslutningar kan, liksom hur de belastningsutjämna och autentisera, se [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md). 

Den externa slutpunkten är hur användarna kan nå dina program medan utanför ditt nätverk. De Gå antingen direkt till en extern URL som du anger, eller de kan komma åt programmet via MyApps-portalen. När användare går till en av de här slutpunkterna, de autentiseras i Azure AD och sedan dirigeras via anslutningen så att dina lokala program.

 ![AzureAD Application Proxy-diagram](./media/application-proxy/azureappproxxy.png)

1. Användaren har åtkomst till programmet via tjänsten Application Proxy och omdirigeras till inloggningssidan för Azure AD för att autentisera.
2. Efter en lyckad inloggning, genereras en token och skickas till klientenheten.
3. Klienten skickar token till tjänsten Application Proxy, som hämtar användarens huvudnamn (UPN) och säkerhet huvudnamn (SPN) från token och sedan dirigerar begäran till anslutningsappen för programproxyn.
4. Om du har konfigurerat enkel inloggning utför kopplingen ytterligare autentisering krävs för användarens räkning.
5. Kopplingen skickar en begäran till dina lokala program.  
6. Svaret skickas via Application Proxy-tjänsten och anslutningen till användaren.

### <a name="single-sign-on"></a>Enkel inloggning
Azure AD-programproxyn tillhandahåller enkel inloggning (SSO) till program som använder integrerad Windows autentisering (IWA) eller anspråksmedvetna program. Om programmet använder IWA, personifierar Application Proxy användaren med Kerberos-begränsad delegering för att tillhandahålla enkel inloggning. Om du har ett anspråksmedvetet program som litar på Azure Active Directory fungerar enkel inloggning eftersom användaren redan har autentiserats av Azure AD.

Mer information om Kerberos finns i [alla du vill veta om Kerberos-begränsad delegering (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Hantera appar
När din app har publicerats med Application Proxy kan hantera du den som andra enterprise-appar i Azure-portalen. Du kan använda Azure Active Directory-säkerhetsfunktioner, t.ex. villkorlig åtkomst och tvåstegsverifiering verifiering, styra användarbehörigheter och anpassa din app. 

## <a name="get-started"></a>Kom igång

Innan du konfigurerar Application Proxy, kontrollera att du har en stödd [Azure Active Directory edition](https://azure.microsoft.com/pricing/details/active-directory/) och Azure AD-katalog som du är en global administratör.

Kom igång med Application Proxy i två steg:

1. [Aktivera Application Proxy och konfigurera anslutningen](application-proxy-enable.md).    
2. [Publicera program](application-proxy-publish-azure-portal.md) – Använd guiden snabbt och enkelt för att få dina lokala appar publicerade och kan nås via en fjärranslutning.

## <a name="whats-next"></a>Nästa steg
När du publicerar din första app, är det mycket mer du kan göra med Application Proxy:

* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Publicera program med ditt domännamn](application-proxy-configure-custom-domain.md)
* [Lär dig mer om Azure AD Application Proxy-kopplingar](application-proxy-connectors.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md) 
* [Ange en anpassad startsida](application-proxy-configure-custom-home-page.md)

Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](https://blogs.technet.com/b/applicationproxyblog/)

