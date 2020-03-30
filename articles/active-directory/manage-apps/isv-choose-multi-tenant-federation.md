---
title: Välj rätt federationsprotokoll för program för flera innehavare
description: Vägledning för oberoende programvaruleverantörer om integrering med Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443380"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Välj rätt federationsprotokoll för ditt program för flera innehavare

När du utvecklar din programvara som ett SaaS-program (Service) måste du välja det federationsprotokoll som bäst uppfyller dina och dina kunders behov. Det här beslutet baseras på din utvecklingsplattform och din önskan att integrera med data som är tillgängliga i kundernas Office 365- och Azure AD-ekosystem.

Se den fullständiga listan över [protokoll som är tillgängliga för SSO-integreringar](what-is-single-sign-on.md) med Azure Active Directory.
I följande tabell jämförs 
* Öppna autentisering 2.0 (OAuth 2.0)
* Öppna ID Connect (OIDC)
* Markeringsspråk för säkerhetspåstående (SAML)
* Web Services Federation (WSFed)

| Funktion| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Webbaserad enkel inloggning| √| √ |
| Webbaserad Enkel ut logga ut| √| √ |
| Mobilbaserad enkel inloggning| √| √* |
| Mobilbaserad Enkel utspelning| √| √* |
| Principer för villkorlig åtkomst för mobila program| √| X |
| Sömlös MFA-upplevelse för mobila applikationer| √| X |
| Få tillgång till Microsoft Graph| √| X |

*Möjligt, men Microsoft ger inte exempel eller vägledning.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 och Open ID Connect

OAuth 2.0 är ett [branschstandardprotokoll](https://oauth.net/2/) för auktorisering. OIDC (OpenID Connect) är ett [branschstandardidentitetsautentiseringslag](https://openid.net/connect/) som bygger ovanpå OAuth 2.0-protokollet.

### <a name="benefits"></a>Fördelar

Microsoft rekommenderar att du använder OIDC/OAuth 2.0 eftersom de har autentisering och auktorisering inbyggt i protokollen. Med SAML måste du dessutom implementera auktorisering.

Auktoriseringen i dessa protokoll gör det möjligt för ditt program att komma åt och integreras med omfattande användar- och organisationsdata via Microsoft Graph API.

Genom att använda OAuth 2.0 och OIDC förenklas dina kunders slutanvändarupplevelse när du använder SSO för ditt program. Du kan enkelt definiera de behörighetsgrupper som behövs, som sedan automatiskt representeras för administratören eller slutanvändarens medgivande.

Med hjälp av dessa protokoll kan dessutom dina kunder använda principer för villkorlig åtkomst och MFA för att styra åtkomsten till programmen. Microsoft tillhandahåller bibliotek och [kodexempel på flera teknikplattformar](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) för att underlätta din utveckling.  

### <a name="implementation"></a>Implementering

Du registrerar ditt program hos Microsoft Identity, som är en OAuth 2.0-leverantör. Du kan sedan också registrera ditt OAuth 2.0-baserade program hos någon annan identitetsleverantör som du vill integrera med. 

Information om hur du registrerar ditt program och implementerar dessa protokoll för SSO till webbappar finns i [Auktorisera åtkomst till webbprogram med OpenID Connect och Azure Active Directory](../develop/sample-v2-code.md).  Information om hur du implementerar dessa protokoll för SSO i mobilappar finns i följande avsnitt: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Universal Windows Platform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 och WSFed

Säkerhetspåslagsspråk (SAML) används vanligtvis för webbprogram. Se [Hur Azure använder SAML-protokollet](../develop/active-directory-saml-protocol-reference.md) för en översikt. 

Web Services Federation (WSFed) är en [branschstandard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) som vanligtvis används för webbapplikationer som utvecklas med hjälp av .Net-plattformen.

### <a name="benefits"></a>Fördelar

SAML 2.0 är en mogen standard och de flesta teknikplattformar stöder bibliotek med öppen källkod för SAML 2.0. Du kan ge dina kunder ett administrationsgränssnitt för att konfigurera SAML SSO. De kan konfigurera SAML SSO för Microsoft Azure AD och alla andra identitetsleverantörer som stöder SAML 2

### <a name="trade-offs"></a>Kompromisser

När du använder SAML 2.0- eller WSFed-protokoll för mobila program har vissa principer för villkorlig åtkomst, inklusive MFA (Multifaktor Authentication) en försämrad upplevelse. Dessutom, om du vill komma åt Microsoft Graph, måste du implementera auktorisering via OAuth 2.0 för att generera nödvändiga token. 

### <a name="implementation"></a>Implementering

Microsoft tillhandahåller inte bibliotek för SAML-implementering eller rekommenderar specifika bibliotek. Det finns många bibliotek med öppen källkod tillgängliga.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO och använda Microsoft Graph Rest API 

Microsoft Graph är datastrukturen i hela Microsoft 365, inklusive Office 365, Windows 10 och Enterprise Mobility and Security och ytterligare produkter som Dynamics 365. Detta inkluderar de grundläggande schemana för entiteterna som Användare, Grupper, Kalender, E-post, Filer med mera som driver användarnas produktivitet. Microsoft Graph erbjuder tre gränssnitt för utvecklare av ett REST-baserat API, Microsoft Graph-dataanslutning och kontakter som gör det möjligt för utvecklare att lägga till egna data i Microsoft Graph.  

Om du använder något av ovanstående protokoll för SSO kan programmets åtkomst till de omfattande data som är tillgängliga via Microsoft Graph REST API. Detta gör det möjligt för dina kunder att få mer värde från sin investering i Microsoft 365. Programmet kan till exempel anropa Microsoft Graph API för att integrera med kundernas Office 365-instans och surface-användares Microsoft Office- och SharePoint-objekt i ditt program. 

Om du använder Open ID Connect för att autentisera är utvecklingsupplevelsen sömlös eftersom du använder OAuth2, grunden för Open ID Connect, för att hämta token kan användas för att anropa Microsoft Graph API:er. Om ditt program använder SAML eller WSFed måste du lägga till ytterligare kod i ditt program för att hämta dessa OAuth2 för att hämta de token som krävs för att anropa Microsoft Graph API:er. 

## <a name="next-steps"></a>Efterföljande moment

[Aktivera enkel inloggning för ditt program för flera innehavare](isv-sso-content.md)

[Skapa dokumentation för ditt program med flera innehavare](isv-create-sso-documentation.md)
