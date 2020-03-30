---
title: Fjärråtkomst till lokala appar – Azure AD Application Proxy
description: Azure Active Directorys programproxy ger säker fjärråtkomst till lokala webbprogram. Efter en enda inloggning till Azure AD kan användare komma åt både moln- och lokala program via en extern URL eller en intern programportal. Programproxy kan till exempel ge fjärråtkomst och enkel inloggning till LOB-program för fjärrskrivbord, SharePoint, Teams, Tableau, Qlik och branschspecifika program (LOB).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274839"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Fjärråtkomst till lokala program via Azure Active Directorys programproxy 

Azure Active Directorys programproxy ger säker fjärråtkomst till lokala webbprogram. Efter en enda inloggning till Azure AD kan användare komma åt både moln- och lokala program via en extern URL eller en intern programportal. Programproxy kan till exempel ge fjärråtkomst och enkel inloggning till LOB-program för fjärrskrivbord, SharePoint, Teams, Tableau, Qlik och branschspecifika program (LOB).

Azure AD-programproxy är:

- **Enkel att använda**. Användare kan komma åt dina lokala program på samma sätt som de får åtkomst till O365 och andra SaaS-appar som är integrerade med Azure AD. Du behöver inte ändra eller uppdatera dina program för att fungera med programproxy. 

- **Säker**. Lokala program kan använda Azures auktoriseringskontroller och säkerhetsanalys. Lokala program kan till exempel använda villkorlig åtkomst och tvåstegsverifiering. Application Proxy kräver inte att du öppnar inkommande anslutningar via brandväggen.
 
- **Kostnadseffektivt**. Lokala lösningar kräver vanligtvis att du ställer in och underhåller demilitariserade zoner (DMZs), kantservrar eller andra komplexa infrastrukturer. Programproxy körs i molnet, vilket gör det enkelt att använda. Om du vill använda Programproxy behöver du inte ändra nätverksinfrastrukturen eller installera ytterligare enheter i den lokala miljön.

## <a name="what-is-application-proxy"></a>Vad är Application Proxy?
Programproxy är en funktion i Azure AD som gör det möjligt för användare att komma åt lokala webbprogram från en fjärrklient. Programproxy innehåller både application proxy-tjänsten som körs i molnet och Application Proxy-anslutningen som körs på en lokal server. Azure AD, application proxy-tjänsten och application proxy-anslutningsappen arbetar tillsammans för att på ett säkert sätt skicka användarens inloggningstoken från Azure AD till webbprogrammet.

Application Proxy fungerar med:

* Webbprogram som använder [integrerad Windows-autentisering](application-proxy-configure-single-sign-on-with-kcd.md) för autentisering  
* Webbprogram som använder formulärbaserad eller [rubrikbaserad](application-proxy-configure-single-sign-on-with-ping-access.md) åtkomst  
* Webb-API:er som du vill exponera för avancerade program på olika enheter  
* Program som finns bakom en [gateway för fjärrskrivbord](application-proxy-integrate-with-remote-desktop-services.md)  
* Omfattande klientappar som är integrerade med Active Directory Authentication Library (ADAL)

Application Proxy stöder enkel inloggning. Mer information om metoder som stöds finns i [Välja en enda inloggningsmetod](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Programproxy rekommenderas för att ge fjärranvändare åtkomst till interna resurser. Application Proxy ersätter behovet av en VPN eller omvänd proxy. Den är inte avsedd för interna användare i företagsnätverket.  Dessa användare som i onödan använder Application Proxy kan introducera oväntade och oönskade prestandaproblem.

## <a name="how-application-proxy-works"></a>Så här fungerar Programproxy

Följande diagram visar hur Azure AD och Application Proxy fungerar tillsammans för att tillhandahålla enkel inloggning till lokala program.

![Proxydiagram för AzureAD-program](./media/application-proxy/azureappproxxy.png)

1. När användaren har åtkomst till programmet via en slutpunkt dirigeras användaren till inloggningssidan för Azure AD. 
2. Efter en lyckad inloggning skickar Azure AD en token till användarens klientenhet.
3. Klienten skickar token till application proxy-tjänsten, som hämtar användarens huvudnamn (UPN) och säkerhetsobjektet (SPN) från token. Programproxy skickar sedan begäran till Application Proxy-anslutningen.
4. Om du har konfigurerat enkel inloggning utför kopplingen all ytterligare autentisering som krävs för användarens räkning.
5. Anslutningsappen skickar begäran till det lokala programmet.  
6. Svaret skickas via tjänsten anslutnings- och programproxy till användaren.

| Komponent | Beskrivning |
| --------- | ----------- |
| Slutpunkt  | Slutpunkten är en URL eller en [slutanvändarportal](end-user-experiences.md). Användare kan nå program utanför nätverket genom att komma åt en extern URL. Användare i nätverket kan komma åt programmet via en URL eller en slutanvändarportal. När användare går till en av dessa slutpunkter autentiseras de i Azure AD och dirigeras sedan via kopplingen till det lokala programmet.|
| Azure AD | Azure AD utför autentiseringen med hjälp av klientkatalogen som lagras i molnet. |
| Tjänsten Programproxy | Den här programproxytjänsten körs i molnet som en del av Azure AD. Den skickar inloggningstoken från användaren till Application Proxy Connector. Application Proxy vidarebefordrar alla tillgängliga rubriker på begäran och ställer in rubrikerna enligt protokollet, till klientens IP-adress. Om den inkommande begäran till proxyn redan har det huvudet läggs klient-IP-adressen till i slutet av den kommaavgränsade listan som är värdet för huvudet.|
| Proxyanslutning för program | Anslutningen är en lättviktsagent som körs på en Windows Server i nätverket. Anslutningsappen hanterar kommunikationen mellan programproxytjänsten i molnet och det lokala programmet. Anslutningen använder endast utgående anslutningar, så du behöver inte öppna några inkommande portar eller placera något i DMZ. Kopplingarna är tillståndslösa och hämtar information från molnet efter behov. Mer information om kopplingar, till exempel hur de belastningssaldo och autentisera finns i [Förstå Azure AD Application Proxy-kopplingar](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory körs lokalt för att utföra autentisering för domänkonton. När enkel inloggning är konfigurerad kommunicerar kopplingen med AD för att utföra eventuell ytterligare autentisering som krävs.
| Lokalt program | Slutligen kan användaren komma åt ett lokalt program. 

## <a name="next-steps"></a>Nästa steg
Mer om du vill börja använda Programproxy finns i [Självstudiekurs: Lägg till ett lokalt program för fjärråtkomst via Programproxy](application-proxy-add-on-premises-application.md). 

De senaste nyheterna och uppdateringarna finns i [bloggen Application Proxy](https://blogs.technet.com/b/applicationproxyblog/)


