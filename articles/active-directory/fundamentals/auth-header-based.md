---
title: Huvud-baserad autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå huvud-baserad autentisering med Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb54410fb314376b68d7297a3452f0990762343d
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577166"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Huvud-baserad autentisering med Azure Active Directory

Äldre program använder ofta huvud-baserad autentisering. I det här scenariot autentiseras en användare (eller meddelande upphovs man) till en mellanliggande identitets lösning. Den mellanliggande lösningen autentiserar användaren och sprider nödvändiga Hypertext Transfer Protocol (HTTP)-huvuden till mål webb tjänsten. Azure Active Directory (AD) stöder det här mönstret via sin Application Proxy-tjänst och integreringar med andra lösningar för nätverks styrenhet. 

I vår lösning ger programproxy fjärråtkomst till programmet, autentiserar användaren och skickar huvuden som krävs av programmet. 

## <a name="use-when"></a>Använd när

Fjärran vändare behöver enkel inloggning (SSO) i till lokala program som kräver en huvud-baserad autentisering.

![Arkitektur-baserad autentisering med avbildning](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare** : åtkomst till äldre program som hanteras av Application Proxy.

* **Webbläsare** : komponenten som användaren interagerar med för att få åtkomst till den externa URL: en för programmet.

* **Azure AD** : autentiserar användaren. 

* **Application Proxy Service** : fungerar som omvänd proxy för att skicka begäran från användaren till det lokala programmet. Den finns i Azure AD och kan även tillämpa principer för villkorlig åtkomst.

* **Application Proxy Connector** : installeras lokalt på Windows-servrar för att tillhandahålla anslutning till programmen. Endast utgående anslutningar används. Returnerar svaret till Azure AD.

* **Äldre program** : program som tar emot användar förfrågningar från programproxyn. Det äldre programmet tar emot de HTTP-huvuden som krävs för att konfigurera en session och returnera ett svar. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Implementera huvud-baserad autentisering med Azure AD

* [Lägg till ett lokalt program för fjärråtkomst via Application Proxy i Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)  

* [Huvudbaserad autentisering för enkel inloggning med Programproxy och PingAccess](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-ping-access) 

* [Skydda äldre appar med appars leveranskontroller och nätverk](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
