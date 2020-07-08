---
title: Välj rätt Federations protokoll för program med flera klienter
description: Vägledning för oberoende program varu leverantörer vid integrering med Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7dc40d0e4736b4fef8012b08737cbd07bc36bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763304"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Välj rätt Federations protokoll för ditt program med flera klienter

När du utvecklar SaaS-program (program vara som en tjänst) måste du välja det Federations protokoll som bäst uppfyller dina kunders behov. Det här beslutet baseras på din utvecklings plattform och du vill integrera med data som är tillgängliga i dina kunders Office 365-och Azure AD-eko system.

Se den fullständiga listan över [protokoll som är tillgängliga för SSO-integration](what-is-single-sign-on.md) med Azure Active Directory.
I följande tabell jämförs 
* Öppen autentisering 2,0 (OAuth 2,0)
* Öppna ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| Kapacitet| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| Webbaserad enkel inloggning| √| √ |
| Webbaserad enkel utloggning| √| √ |
| Mobil-baserad enkel inloggning| √| √* |
| Mobil-baserad enkel utloggning| √| √* |
| Principer för villkorlig åtkomst för mobila program| √| X |
| Sömlös MFA-upplevelse för mobila program| √| X |
| Åtkomst Microsoft Graph| √| X |

* Möjligt, men Microsoft tillhandahåller inte exempel eller rikt linjer.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2,0 och öppen ID Connect

OAuth 2,0 är ett [bransch standard](https://oauth.net/2/) protokoll för auktorisering. OIDC (OpenID Connect) är ett [standard](https://openid.net/connect/) lager för identitets verifiering som byggts ovanpå OAuth 2,0-protokollet.

### <a name="benefits"></a>Fördelar

Microsoft rekommenderar att du använder OIDC/OAuth 2,0 eftersom de har autentisering och auktorisering inbyggd i protokollen. Med SAML måste du också implementera auktorisering.

Auktoriseringen i dessa protokoll gör att ditt program kan komma åt och integrera med omfattande användar-och organisations data via Microsoft Graph API.

Med hjälp av OAuth 2,0 och OIDC fören klar kunderna användarnas upplevelse när de använder SSO för ditt program. Du kan enkelt definiera de behörighets uppsättningar som krävs, som sedan visas automatiskt för administratören eller slutanvändaren som godkänner.

Med dessa protokoll kan dina kunder dessutom använda villkorlig åtkomst och MFA-principer för att kontrol lera åtkomsten till programmen. Microsoft tillhandahåller bibliotek och [kod exempel över flera teknik plattformar](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) för att hjälpa dig att utveckla.  

### <a name="implementation"></a>Implementering

Du registrerar ditt program med Microsoft Identity, som är en OAuth 2,0-Provider. Du kan sedan registrera ditt OAuth 2,0-baserade program med andra identitets leverantörer som du vill integrera med. 

Information om hur du registrerar ditt program och implementerar dessa protokoll för enkel inloggning till webbappar finns i [bevilja åtkomst till webb program med hjälp av OpenID Connect och Azure Active Directory](../develop/sample-v2-code.md).  Information om hur du implementerar dessa protokoll för enkel inloggning i mobila appar finns i följande avsnitt: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Universell Windows-plattform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 och WSFed

Security Assertion Markup Language (SAML) används vanligt vis för webb program. Se [hur Azure använder SAML-protokollet](../develop/active-directory-saml-protocol-reference.md) för en översikt. 

Web Services Federation (WSFed) är en [bransch standard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) som används vanligt vis för webb program som utvecklas med hjälp av .NET-plattformen.

### <a name="benefits"></a>Fördelar

SAML 2,0 är en vuxen standard och de flesta teknik plattformarna stöder bibliotek med öppen källkod för SAML 2,0. Du kan ge dina kunder ett administrations gränssnitt för att konfigurera SAML SSO. De kan konfigurera SAML SSO för Microsoft Azure AD och andra identitets leverantörer som stöder SAML 2

### <a name="trade-offs"></a>Kompromisser

När du använder SAML 2,0-eller WSFed-protokoll för mobila program, kommer vissa principer för villkorlig åtkomst inklusive Multi-Factor Authentication (MFA) att ha en försämrad upplevelse. Om du vill komma åt Microsoft Graph måste du dessutom implementera auktorisering via OAuth 2,0 för att generera nödvändiga tokens. 

### <a name="implementation"></a>Implementering

Microsoft tillhandahåller inga bibliotek för SAML-implementering eller rekommenderar vissa bibliotek. Det finns många tillgängliga bibliotek med öppen källkod.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO och använder Microsoft Graph REST API 

Microsoft Graph är data infrastrukturen i alla Microsoft 365, inklusive Office 365, Windows 10 och Enterprise Mobility och Security samt ytterligare produkter som Dynamics 365. Detta inkluderar kärn scheman för entiteter, till exempel användare, grupper, kalender, e-post, filer och mycket mer, som driver användar produktivitet. Microsoft Graph erbjuder tre gränssnitt för utvecklare ett REST-baserat API, Microsoft Graph data Connect och anslutningar som gör att utvecklare kan lägga till sina egna data i Microsoft Graph.  

Genom att använda något av protokollen ovan för SSO kan du få programmets åtkomst till de omfattande data som är tillgängliga via Microsoft Graph REST API. Detta gör det möjligt för dina kunder att få mer värde från sina investeringar i Microsoft 365. Ditt program kan t. ex. anropa Microsoft Graph-API: n för att integrera med dina kunders Office 365-instans-och Surface-användares Microsoft Office-och SharePoint-objekt i ditt program. 

Om du använder Open ID Connect för att autentisera är din utvecklings upplevelse sömlös eftersom du kommer att använda OAuth2, grunden för Open ID Connect, för att hämta token kan användas för att anropa Microsoft Graph API: er. Om programmet använder SAML eller WSFed måste du lägga till ytterligare kod i programmet för att hämta dessa OAuth2 för att hämta de token som krävs för att anropa Microsoft Graph API: er. 

## <a name="next-steps"></a>Nästa steg

[Aktivera enkel inloggning för ditt program för flera innehavare](isv-sso-content.md)

[Skapa dokumentation för ditt program med flera klienter](isv-create-sso-documentation.md)
