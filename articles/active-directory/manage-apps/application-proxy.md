---
title: Fjärråtkomst till lokala appar – Azure AD-programproxy
description: Azure Active Directorys Application Proxy ger säker fjärråtkomst till lokala webb program. Efter en enkel inloggning till Azure AD kan användare komma åt både molnet och lokala program via en extern URL eller en intern applikations Portal. Till exempel kan programproxy tillhandahålla fjärråtkomst och enkel inloggning till fjärr skrivbord, SharePoint, teams, Tableau, Qlik och LOB-program (Line of Business).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274839"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Fjärråtkomst till lokala program via Azure Active Directory programmets proxy 

Azure Active Directorys Application Proxy ger säker fjärråtkomst till lokala webb program. Efter en enkel inloggning till Azure AD kan användare komma åt både molnet och lokala program via en extern URL eller en intern applikations Portal. Till exempel kan programproxy tillhandahålla fjärråtkomst och enkel inloggning till fjärr skrivbord, SharePoint, teams, Tableau, Qlik och LOB-program (Line of Business).

Azure AD Application Proxy är:

- **Enkelt att använda**. Användare kan komma åt dina lokala program på samma sätt som de kommer åt O365 och andra SaaS-appar som är integrerade med Azure AD. Du behöver inte ändra eller uppdatera dina program att arbeta med Application Proxy. 

- **Säker**. Lokala program kan använda Azures verifierings kontroller och säkerhets analys. Lokala program kan till exempel använda villkorlig åtkomst och tvåstegsverifiering. Application Proxy kräver inte att du öppnar inkommande anslutningar via brand väggen.
 
- **Kostnads effektivt**. Lokala lösningar kräver vanligt vis att du konfigurerar och underhåller demilitariserad-zoner (DMZs), Edge-servrar eller andra komplexa infrastrukturer. Programproxyn körs i molnet, vilket gör det enkelt att använda. Om du vill använda Application Proxy behöver du inte ändra nätverks infrastrukturen eller installera ytterligare enheter i din lokala miljö.

## <a name="what-is-application-proxy"></a>Vad är Application Proxy?
Application Proxy är en funktion i Azure AD som gör det möjligt för användare att komma åt lokala webb program från en fjärran sluten klient. Programproxyn innehåller både tjänsten Application Proxy som körs i molnet och Application Proxy Connector som körs på en lokal server. Azure AD, Application Proxy-tjänsten och Application Proxy-anslutningsprogrammet fungerar tillsammans för att på ett säkert sätt skicka användar inloggnings-token från Azure AD till webb programmet.

Application Proxy fungerar med:

* Webbprogram som använder [integrerad Windows-autentisering](application-proxy-configure-single-sign-on-with-kcd.md) för autentisering  
* Webbprogram som använder formulärbaserad eller [rubrikbaserad](application-proxy-configure-single-sign-on-with-ping-access.md) åtkomst  
* Web API: er som du vill exponera omfattande program på olika enheter  
* Program som ligger bakom en [fjärrskrivbordsgateway](application-proxy-integrate-with-remote-desktop-services.md)  
* Rich-klientprogram som är integrerade med Active Directory Authentication Library (ADAL)

Application Proxy stöder enkel inloggning. Mer information om metoder som stöds finns i [välja en metod för enkel inloggning](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Application Proxy rekommenderas för att ge fjärran vändare åtkomst till interna resurser. Programproxyn ersätter behovet av en VPN-eller omvänd proxy. Den är inte avsedd för interna användare i företags nätverket.  Dessa användare som inte nödvändigt vis använder Application Proxy kan orsaka oväntade och oönskade prestanda problem.

## <a name="how-application-proxy-works"></a>Så här fungerar tillämpningsproxy

Följande diagram visar hur Azure AD och Application Proxy arbetar tillsammans för att tillhandahålla enkel inloggning till lokala program.

![AzureAD Application Proxy-diagram](./media/application-proxy/azureappproxxy.png)

1. När användaren har åtkomst till programmet via en slutpunkt, omdirigeras användaren till inloggningssidan för Azure AD. 
2. Efter en lyckad inloggning skickar Azure AD en token till användarens klient enhet.
3. Klienten skickar token till Application Proxy-tjänsten som hämtar User Principal Name (UPN) och säkerhets huvud namn (SPN) från token. Application Proxy skickar sedan begäran till Application Proxy Connector.
4. Om du har konfigurerat enkel inloggning utför kopplingen ytterligare autentisering krävs för användarens räkning.
5. Kopplingen skickar en begäran till dina lokala program.  
6. Svaret skickas via tjänsten anslutnings-och programproxy till användaren.

| Komponent | Beskrivning |
| --------- | ----------- |
| Slutpunkt  | Slut punkten är en URL eller en [slut användar Portal](end-user-experiences.md). Användarna kan nå program medan utanför ditt nätverk genom att gå till en extern URL. Användare i nätverket kan komma åt programmet via en URL eller ett slutanvändarportal. När användare går till en av de här slutpunkterna, de autentiseras i Azure AD och sedan dirigeras via anslutningen så att dina lokala program.|
| Azure AD | Azure AD utför autentiseringen med hjälp av klient katalogen som lagras i molnet. |
| Application Proxy-tjänst | Tjänsten Application Proxy körs i molnet som en del av Azure AD. Den skickar inloggnings-token från användaren till Application Proxy Connector. Programproxyn vidarebefordrar eventuella tillgängliga huvuden på begäran och anger huvudena enligt dess protokoll till klientens IP-adress. Om den inkommande begäran till proxyn redan har den rubriken, läggs klientens IP-adress till i slutet av den kommaavgränsade listan som är värdet för rubriken.|
| Application Proxy Connector | Anslutningen är en Lightweight-agent som körs på en Windows-Server i nätverket. Anslutningen hanterar kommunikationen mellan Application Proxy-tjänsten i molnet och det lokala programmet. Anslutningen använder bara utgående anslutningar, så du behöver inte öppna några inkommande portar eller publicera något i DMZ. Anslutningarna är tillståndslösa och hämta information från molnet efter behov. Mer information om anslutningar kan, liksom hur de belastningsutjämna och autentisera, se [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory körs lokalt för att utföra autentisering för domän konton. När enkel inloggning har kon figurer ATS kommunicerar anslutningen med AD för att utföra ytterligare autentisering krävs.
| Lokalt program | Slutligen kan användaren komma åt ett lokalt program. 

## <a name="next-steps"></a>Nästa steg
Information om hur du börjar använda Application Proxy finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via programproxy](application-proxy-add-on-premises-application.md). 

De senaste nyheterna och uppdateringarna finns i [Application Proxy-bloggen](https://blogs.technet.com/b/applicationproxyblog/)


