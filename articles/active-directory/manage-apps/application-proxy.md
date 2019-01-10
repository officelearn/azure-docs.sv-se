---
title: Fjärråtkomst till lokala appar – Azure Active Directory Application Proxy | Microsoft Docx
description: Azure Active Directory Application Proxy ger säker fjärråtkomst till lokala webbprogram. När du har en enkel inloggning till Azure AD, kan användare komma åt både i molnet och lokala program via en extern Webbadress eller en intern App-portal. Till exempel kan Application Proxy ge fjärråtkomst och enkel inloggning för fjärrskrivbord, SharePoint, Teams, Tableau, Qlik och verksamhetsspecifika (LOB) program.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 94330c9d53b69d86ff05fe2f0164cb7559e9d389
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159136"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Fjärråtkomst till lokala program via Azure Active Directory Application Proxy 

Azure Active Directory Application Proxy ger säker fjärråtkomst till lokala webbprogram. När du har en enkel inloggning till Azure AD, kan användare komma åt både i molnet och lokala program via en extern Webbadress eller en intern App-portal. Till exempel kan Application Proxy ge fjärråtkomst och enkel inloggning för fjärrskrivbord, SharePoint, Teams, Tableau, Qlik och verksamhetsspecifika (LOB) program.

Azure AD Application Proxy är:

- **Enkelt att använda**. Användare kan komma åt dina lokala program på samma sätt som de har åtkomst till O365 och andra SaaS-appar som integreras med Azure AD. Du behöver inte ändra eller uppdatera dina program att arbeta med Application Proxy. 

- **Skydda**. Lokala program kan använda auktoriseringskontroller och säkerhetsanalyser i Azure. Lokala program kan exempelvis använda villkorlig åtkomst och tvåstegsverifiering. Programproxy måste inte du öppna inkommande anslutningar via brandväggen.
 
- **Kostnadseffektiv**. Lokala lösningar kräver normalt att du konfigurerar och underhåller demilitariserad zoner (DMV), edge-servrar eller andra komplexa infrastrukturer. Programproxy körs i molnet, vilket gör det enkelt att använda. Om du vill använda Application Proxy, behöver du inte ändra nätverkets infrastruktur eller installera ytterligare installationer i din lokala miljö.

## <a name="what-is-application-proxy"></a>Vad är Application Proxy?
Programproxyn är en funktion i Azure AD som gör att användarna kan få åtkomst till lokala webbprogram från en fjärransluten klient. Programproxy omfattar både Application Proxy-tjänsten som körs i molnet och programproxy-kopplingen som körs på en lokal server. Azure AD Application Proxy-tjänsten och Application Proxy connector fungerar tillsammans för att på ett säkert sätt överföra inloggnings-användartoken från Azure AD till webbprogrammet.

Programproxy fungerar med:

* Webbprogram som använder [integrerad Windows-autentisering](application-proxy-configure-single-sign-on-with-kcd.md) för autentisering  
* Webbprogram som använder formulärbaserad eller [rubrikbaserad](application-proxy-configure-single-sign-on-with-ping-access.md) åtkomst  
* Web API: er som du vill exponera omfattande program på olika enheter  
* Program som ligger bakom en [fjärrskrivbordsgateway](application-proxy-integrate-with-remote-desktop-services.md)  
* Rich-klientprogram som är integrerade med Active Directory Authentication Library (ADAL)

Programproxyn har stöd för enkel inloggning. Mer information om metoder som stöds finns i [välja en metod för enkel inloggning](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

## <a name="how-application-proxy-works"></a>Hur fungerar Application Proxy

Följande diagram visar hur Azure AD och Application Proxy fungerar tillsammans för att tillhandahålla enkel inloggning till lokala program.

![AzureAD Application Proxy-diagram](./media/application-proxy/azureappproxxy.png)

1. När användaren har åtkomst till programmet via en slutpunkt, omdirigeras användaren till inloggningssidan för Azure AD. 
2. Efter en lyckad inloggning skickar Azure AD en token till användarens klientenheten.
3. Klienten skickar token till tjänsten Application Proxy, som hämtar användarens huvudnamn (UPN) och säkerhet huvudnamn (SPN) från token. Application Proxy skickar sedan begäran till anslutningsappen för programproxyn.
4. Om du har konfigurerat enkel inloggning utför kopplingen ytterligare autentisering krävs för användarens räkning.
5. Kopplingen skickar en begäran till dina lokala program.  
6. Svaret skickas via anslutningen och programproxytjänsten för användaren.

| Komponent | Beskrivning |
| --------- | ----------- |
| Slutpunkt  | Slutpunkten är en URL eller en [slutanvändarportal](end-user-experiences.md). Användarna kan nå program medan utanför ditt nätverk genom att gå till en extern URL. Användare i nätverket kan komma åt programmet via en URL eller ett slutanvändarportal. När användare går till en av de här slutpunkterna, de autentiseras i Azure AD och sedan dirigeras via anslutningen så att dina lokala program.|
| Azure AD | Azure AD autentiserar med hjälp av klientkatalogen lagras i molnet. |
| Tjänsten för Application Proxy | Den här Application Proxy-tjänsten körs i molnet som en del av Azure AD. Den skickar inloggnings-token från användaren till Application Proxy Connector. Programproxy vidarebefordrar tillgänglig rubriker på begäran och anger rubriker enligt dess protokoll till klientens IP-adress. Om den inkommande begäran till proxyn har redan den rubriken, klientens IP-adress har lagts till i slutet av en kommaavgränsad lista som är värdet för sidhuvudet.|
| Application Proxy Connector | Anslutningen är en förenklad agent som körs på en Windows-Server i ditt nätverk. Anslutningen hanterar kommunikationen mellan Application Proxy-tjänst i molnet och lokalt program. Anslutningsappen använder bara utgående anslutningar så att du inte behöver öppna några ingående portar eller lägga något i Perimeternätverket. Anslutningarna är tillståndslösa och hämta information från molnet efter behov. Mer information om anslutningar kan, liksom hur de belastningsutjämna och autentisera, se [alternativ för Azure AD-programproxyn kopplingar](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory körs lokalt för att utföra autentisering för domänkonton. När enkel inloggning har konfigurerats, kommunicerar anslutningen med AD för att utföra ytterligare autentisering krävs.
| Lokalt program | Slutligen kan användaren komma åt ett lokalt program. 

## <a name="next-steps"></a>Nästa steg
Om du vill börja använda programproxy, se [självstudien: Lägga till ett lokalt program för fjärråtkomst via programproxy](application-proxy-add-on-premises-application.md). 

De senaste nyheterna och uppdateringarna finns i den [bloggen om Application Proxy](https://blogs.technet.com/b/applicationproxyblog/)


