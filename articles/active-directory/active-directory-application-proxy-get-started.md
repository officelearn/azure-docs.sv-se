---
title: "Hur du ger säker fjärråtkomst till lokala appar"
description: "Beskriver hur du använder Azure AD Application Proxy för att tillhandahålla säker fjärråtkomst till lokala appar."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 24def898f282c4e122ae53932ae86047e815595c
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Hur du ger säker fjärråtkomst till lokala program

Anställda vill idag att vara produktiva var som helst, när som helst och från valfri enhet. De vill arbeta med sina egna enheter, oavsett om de är bärbara datorer, surfplattor eller telefoner. Och de förväntar sig för att kunna komma åt sina program, både SaaS-appar i molnet och företagets appar lokalt. Att ge åtkomst till lokala program har traditionellt berörda virtuella privata nätverk (VPN) eller demilitariserad zoner (DMZs). Inte bara dessa lösningar komplexa och svåra att säkra, men de finns kostsamt att konfigurera och hantera.

Det finns ett bättre sätt!

En modern personal i mobile-första molnet första world behöver en lösning för moderna fjärråtkomst. Azure AD Application Proxy är en funktion i Azure Active Directory som erbjuder fjärråtkomst som en tjänst. Det innebär att det är lätt att distribuera, använda och hantera.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Vad är Azure Active Directory Application Proxy?
Azure AD Application Proxy ger enkel inloggning (SSO) och säker fjärråtkomst för webbprogram finns lokalt. Vissa appar som du vill publicera omfattar SharePoint-webbplatser, Outlook Web Access eller några andra LOB webbprogram som du har. Dessa lokala webbprogram är integrerade med Azure AD, samma identitet och kontroll-plattform som används av O365. Slutanvändarna kan komma åt lokala program på samma sätt som de har åtkomst till O365 och andra SaaS-appar som är integrerade med Azure AD. Du behöver inte ändra nätverkets infrastruktur eller kräver VPN att tillhandahålla den här lösningen för dina användare.

## <a name="why-is-application-proxy-a-better-solution"></a>Varför är Application Proxy en bättre lösning?
Azure AD Application Proxy är en lösning för enkel, säker och kostnadseffektiv fjärråtkomst till lokala program.

Azure AD Application Proxy är:

* **Enkel**
   * Du behöver inte ändra eller uppdatera dina program att arbeta med Application Proxy. 
   * Användarna får en konsekvent autentiseringsupplevelse. De kan använda den MyApps-portalen för att få enkel inloggning till både SaaS-appar i molnet och dina appar lokalt. 
* **Skydda**
   * När du publicerar dina appar med hjälp av Azure AD Application Proxy kan dra du nytta av kontroller som omfattande tillstånd och säkerhet analytics i Azure. Du får skalbar molnlagring säkerhet och säkerheten i Azure-funktioner som villkorlig åtkomst och tvåstegsverifiering verifiering.
   * Du behöver öppna alla inkommande anslutningar via brandväggen för att ge användarna fjärråtkomst. 
* **Kostnadseffektiv**
   * Programproxy fungerar i molnet, så att du kan spara tid och pengar. Lokala lösningar kräver normalt att ställa in och underhålla DMZs, edge servrar eller andra komplexa infrastrukturer.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Vilka typer av program som fungerar med Application Proxy?
Du kan komma åt olika typer av interna program med Azure AD Application Proxy:

* Webbprogram som använder [integrerad Windows-autentisering](active-directory-application-proxy-sso-using-kcd.md) för autentisering  
* Webbprogram som använder formulärbaserad eller [huvud-baserade](application-proxy-ping-access.md) åtkomst  
* Web API: er som du vill exponera omfattande program på olika enheter  
* Program som finns bakom en [servern för fjärrskrivbordsgateway](application-proxy-publish-remote-desktop.md)  
* Rich-klientprogram som är integrerade med Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Hur fungerar Application Proxy?
Det finns två komponenter som måste du konfigurera om du vill göra Application Proxy fungerar: en koppling och den externa slutpunkten. 

Kopplingen har en förenklad agent som placeras på en Windows-Server i nätverket. Kopplingen underlättar trafikflöde från Application Proxy-tjänst i molnet till ditt program lokalt. Den används endast utgående anslutningar, så du behöver öppna ingående portar eller placera något i Perimeternätverket. Kopplingarna är tillståndslösa och hämta information från molnet vid behov. Mer information om kopplingar, t.ex. hur de belastningsutjämning och autentisera, se [förstå Azure AD Application Proxy kopplingar](application-proxy-understand-connectors.md). 

Den externa slutpunkten är hur användarna kan nå dina program medan utanför nätverket. De kan antingen gå direkt till en extern Webbadress som du bestämmer eller de kan komma åt programmet via MyApps-portalen. Om användare besöker någon av dessa slutpunkter, de autentiseras i Azure AD och sedan dirigeras via anslutningen till lokala program.

 ![Diagram över AzureAD Application Proxy](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. Användaren har åtkomst till programmet via tjänsten Application Proxy och omdirigeras till inloggningssidan för Azure AD för autentisering.
2. Efter en lyckad inloggning, är en token genererats och skickats till klientenheten.
3. Klienten skickar token till tjänsten Application Proxy, som hämtar användarens huvudnamn (UPN) och säkerhet huvudnamn (SPN) från token och dirigerar begäran till Application Proxy connector.
4. Om du har konfigurerat enkel inloggning utför kopplingen ytterligare autentisering krävs för användarens räkning.
5. Kopplingen skickar en begäran till programmet lokalt.  
6. Svaret skickas via Application Proxy-tjänsten och anslutningen till användaren.

### <a name="single-sign-on"></a>Enkel inloggning
Azure AD Application Proxy ger enkel inloggning (SSO) för program som använder integrerad autentisering IWA (Windows) eller anspråksmedvetna program. Om programmet använder IWA, personifierar Application Proxy användaren använder Kerberos-begränsad delegering för att tillhandahålla enkel inloggning. Om du har ett anspråksmedvetet program som har förtroende för Azure Active Directory fungerar enkel inloggning eftersom användaren redan har autentiserats av Azure AD.

Mer information om Kerberos finns [alla du vill veta om Kerberos-begränsad delegering (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Hantera appar
När appen har publicerats med Application Proxy kan hantera du den precis som andra företagsapp i Azure-portalen. Du kan använder Azure Active Directory-säkerhetsfunktioner som villkorlig åtkomst och tvåstegsverifiering verifiering, styra användarbehörigheter och anpassa din app. 

## <a name="get-started"></a>Kom igång

Innan du konfigurerar Application Proxy, kontrollera att du har en stöds [Azure Active Directory edition](https://azure.microsoft.com/pricing/details/active-directory/) och Azure AD-katalog som du är en global administratör.

Kom igång med Application Proxy i två steg:

1. [Aktivera Application Proxy och konfigurera anslutningen](active-directory-application-proxy-enable.md).    
2. [Publicera program](active-directory-application-proxy-publish.md) – Använd guiden snabbt och enkelt för att hämta dina lokala appar publicerade och är åtkomlig via fjärranslutning.

## <a name="whats-next"></a>Nästa steg
När du har publicerat din första app finns mycket mer du kan göra med Application Proxy:

* [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
* [Publicera program med ditt domännamn](active-directory-application-proxy-custom-domains.md)
* [Lär dig mer om Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-working-with-proxy-servers.md) 
* [Ange en anpassad startsida](application-proxy-office365-app-launcher.md)

Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)

