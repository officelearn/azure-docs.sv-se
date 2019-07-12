---
title: Välj rätt federation-protokollet för ditt program för flera innehavare
description: Vägledning för oberoende programleverantörer för integrering med Azure Active Directory
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795202"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Välj rätt federation-protokollet för ditt program för flera innehavare

När du utvecklar din programvara som en tjänst (SaaS), måste du välja federation-protokollet som bäst uppfyller dina och dina kunders behov. Det här beslutet baseras på din utvecklingsplattform och din önskan att integrera med data som är tillgängliga i kundernas Office 365 och Azure AD-ekosystemet.

Se en fullständig lista över [protokoll som är tillgängliga för enkel inloggning integrationer](what-is-single-sign-on.md) med Azure Active Directory.
Följande tabell jämförs 
* Öppen autentisering 2.0 (OAuth 2.0)
* Öppna ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Web Services Federation (WSFed)

| Funktion| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Webbaserad enkel inloggning| √| √ |
| Webbaserad enkel utloggning| √| √ |
| Mobile-baserad enkel inloggning| √| √* |
| Mobile-baserad enkel utloggning| √| √* |
| Principer för villkorlig åtkomst för mobila program| √| X |
| Smidigt MFA för mobila program| √| X |
| Åtkomst till Microsoft Graph| √| X |

\* Möjligt, men Microsoft tillhandahåller inte exempel eller vägledning.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 och öppna ID Connect

OAuth 2.0 är en [branschstandard](https://oauth.net/2/) protokoll för auktorisering. OIDC (OpenID Connect) är en [branschstandard](https://openid.net/connect/) Identitetslagret autentisering som bygger på Oath 2.0-protokollet.

### <a name="benefits"></a>Fördelar

Microsoft rekommenderar att du använder OIDC/OAuth 2.0 som de har autentisering och auktorisering som är inbyggd i protokollen. Med SAML, måste du även implementera auktorisering.

I dessa protokoll auktoriseringen kan dina program kan komma åt och integrera med rika och organisationens data via Microsoft Graph API.

Med hjälp av OAuth 2.0 och OIDC förenklar kundernas slutanvändarens upplevelse när enkel inloggning för ditt program. Du kan enkelt definiera behörighet uppsättningar behövs, som sedan automatiskt representeras till administratören eller slutanvändaren medgivandedialogen.

Dessutom kan använder dessa protokoll kunderna använda villkorlig åtkomst och MFA-principer för att styra åtkomst till program. Microsoft tillhandahåller bibliotek och [kodexempel på flera plattformar för teknik](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) att underlätta utveckling.  

### <a name="implementation"></a>Implementering

Du kan registrera ditt program med Microsoft Identity, vilket är en OAuth 2.0-provider. Du kan sedan också registrera ditt OAuth 2.0-baserade program med någon annan identitetsleverantören som du vill integrera med. 

Information om hur du registrerar ditt program och implementera dessa protokoll för enkel inloggning till web apps finns i [bevilja åtkomst till webbprogram med hjälp av OpenID Connect och Azure Active Directory](../develop/sample-v2-code.md).  Mer information om hur du implementera dessa protokoll för enkel inloggning i mobila appar finns i följande: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Universal Windows Platform](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 och WSFed

Security Assertion Markup Language (SAML) används vanligtvis för webbprogram. Se [hur Azure använder SAML-protokoll](../develop/active-directory-saml-protocol-reference.md) en översikt. 

Web Services Federation (WSFed) är en [branschstandard](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) används för webbprogram som har utvecklats med hjälp av .net-plattformen.

### <a name="benefits"></a>Fördelar

SAML 2.0 är en mogen standard och de flesta teknik plattformar stöd för öppen källkod-bibliotek för SAML 2.0. Du kan ge kunderna ett administrationsgränssnitt om du vill konfigurera SAML SSO. De kan konfigurera SAML SSO för Microsoft Azure AD och andra identitetsprovider som har stöd för SAML-2

### <a name="trade-offs"></a>Med de

När du använder SAML 2.0 eller WSFed protokoll för mobila program, har vissa principer för villkorlig åtkomst, inklusive Multi-Factor Authentication (MFA) en försämrad upplevelse. Om du vill komma åt Microsoft Graph behöver du dessutom att implementera auktorisering via OAuth 2.0 för att generera nödvändigt token. 

### <a name="implementation"></a>Implementering

Microsoft varken tillhandahåller bibliotek för SAML-implementering eller rekommenderar specifika bibliotek. Det finns många bibliotek med öppen källkod som är tillgängliga.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>Enkel inloggning och använder Microsoft Graph Rest API 

Microsoft Graph är infrastrukturresursen data för alla Microsoft 365, inklusive Office 365, Windows 10 och Enterprise Mobility och Security och ytterligare produkter, till exempel Dynamics 365. Detta inkluderar core scheman för entiteter som användare, grupper, kalender, e-post, filer och mycket mer, att öka produktiviteten för användaren. Microsoft Graph erbjuder tre gränssnitt för utvecklare ett REST-baserad API-, Microsoft Graph-data anslutnings- och kopplingar som gör att utvecklare att lägga till sina egna data i Microsoft Graph.  

Med hjälp av något av de ovanstående protokoll för enkel inloggning ger ditt programs åtkomst till omfattande data som är tillgängliga via Microsoft Graph REST-API. Detta gör det möjligt för kunderna att få ut mer av sina investeringar i Microsoft 365. Programmet kan till exempel anropa Microsoft Graph API för att integrera med dina kunders Office 365-instansen och surface användarnas Microsoft Office och SharePoint-objekt i ditt program. 

Om du använder öppna ID Connect för autentisering, din utvecklingsupplevelse är sömlös eftersom du använder OAuth2, kan grunden för att öppna ID Connect, att hämta token användas för att anropa Microsoft Graph API: er. Om ditt program använder SAML eller WSFed, måste du lägga till extra kod i ditt program att hämta dessa OAuth2 att förvärva de token som krävs för att anropa Microsoft Graph API: er. 

## <a name="next-steps"></a>Nästa steg

[Aktivera enkel inloggning för dina program med flera innehavare](isv-sso-content.md)

[Skapa dokumentation för ditt program med flera innehavare](isv-create-sso-documentation.md)
