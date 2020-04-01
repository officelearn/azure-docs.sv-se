---
title: Utvecklaranteckningar för anpassade principer
titleSuffix: Azure AD B2C
description: Anteckningar för utvecklare om att konfigurera och underhålla Azure AD B2C med anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408704"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Utvecklaranteckningar för anpassade principer i Azure Active Directory B2C

Anpassad principkonfiguration i Azure Active Directory B2C är nu allmänt tillgänglig. Den här konfigurationsmetoden är inriktad på avancerade identitetsutvecklare som bygger komplexa identitetslösningar. Anpassade principer gör kraften i Identity Experience Framework tillgänglig i Azure AD B2C-klienter.
Avancerade identitetsutvecklare som använder anpassade principer bör planera att investera lite tid med att slutföra genomgångar och läsa referensdokument.

De flesta av de anpassade principalternativen är nu allmänt tillgängliga, men det finns underliggande funktioner, till exempel tekniska profiltyper och API:er för innehållsdefinition som befinner sig i olika skeden av programvarans livscykel. Många fler kommer. Tabellen nedan anger tillgänglighetsnivån på en mer detaljerad nivå.

## <a name="features-that-are-generally-available"></a>Funktioner som är allmänt tillgängliga

- Skapa och ladda upp anpassade autentiseringsanvändarresor med hjälp av anpassade principer.
    - Beskriv användarresor steg för steg som utbyten mellan skadeleverantörer.
    - Definiera villkorlig förgrening i användarresor.
- Samverka med REST API-aktiverade tjänster i dina anpassade autentiseringsanvändarresor.
- Federera med identitetsleverantörer som är kompatibla med OpenIDConnect-protokollet.
- Federera med identitetsleverantörer som följer SAML 2.0-protokollet.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Ansvar för anpassade principfunktionsuppsättningsutvecklare

Manuell principkonfiguration ger åtkomst på lägre nivå till den underliggande plattformen för Azure AD B2C och resulterar i skapandet av ett unikt, förtroenderamverk. De många möjliga permutationerna av anpassade identitetsleverantörer, förtroenderelationer, integrationer med externa tjänster och steg-för-steg-arbetsflöden kräver en metodisk metod för design och konfiguration.

Utvecklare som konsumerar den anpassade principfunktionen bör följa följande riktlinjer:

- Bekanta dig med konfigurationsspråket för anpassade principer och hantering av nyckel/hemligheter. Mer information finns i [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Bli ägare till scenarier och anpassade integreringar. Dokumentera ditt arbete och informera din live site organisation.
- Utför metodisk scenariotestning.
- Följ programvaruutveckling och mellanlagringstips med minst en utvecklings- och testmiljö och en produktionsmiljö.
- Håll dig informerad om ny utveckling från de identitetsleverantörer och tjänster du integrerar med. Håll till exempel reda på ändringar i hemligheter och schemalagda och oplanerade ändringar av tjänsten.
- Konfigurera aktiv övervakning och övervaka produktionsmiljöernas svarstider. Mer information om hur du integrerar med Application Insights finns i [Azure Active Directory B2C: Samla in loggar](analytics-with-application-insights.md).
- Håll e-postadresser för kontaktkontakter aktuella i Azure-prenumerationen och håll dig lyhörd för Microsofts e-postmeddelanden för live-site-teamet.
- Vidta åtgärder i tid när du uppmanas att göra det av Microsofts live-site-team.

## <a name="terms-for-features-in-public-preview"></a>Villkor för funktioner i offentlig förhandsversion

- Vi rekommenderar att du använder de offentliga förhandsversionsfunktionerna endast i utvärderingssyfte.
- Servicenivåavtal (SLA) gäller inte för de offentliga förhandsversionsfunktionerna.
- Supportförfrågningar för offentliga förhandsversionsfunktioner kan arkiveras via vanliga supportkanaler.

## <a name="features-by-stage-and-known-issues"></a>Funktioner efter steg och kända problem

Anpassade funktioner för policy/Identity Experience Framework är under ständig och snabb utveckling. Följande tabell är ett index över funktioner och komponenttillgänglighet.


### <a name="protocols-and-authorization-flows"></a>Protokoll och auktoriseringsflöden

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2-auktoriseringskod](authorization-code-flow.md) |  |  | X |  |
| OAuth2-auktoriseringskod med PKCE |  |  | X | Endast mobila applikationer  |
| [OAuth2 implicit flöde](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2-resursägare lösenord autentiseringsuppgifter](ropc-custom.md) |  | X |  |  |
| [OIDC Anslut](openid-connect.md) |  |  | X |  |
| [SAML2 (AV )](connect-with-saml-service-providers.md)  |  |X  |  | POST- och omdirigeringsbindningar. |
| OAuth1 |  |  |  | Stöds inte. |
| WSFED (WSFED) | X |  |  |  |

### <a name="identify-providers-federation"></a>Identifiera providers federation 

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Till exempel Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Till exempel Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Till exempel Twitter. |
| [SAML2 (AV )](saml-technical-profile.md) |  |   | X | Till exempel Salesforce, ADFS. |
| WSFED (WSFED)| X |  |  |  |


### <a name="rest-api-integration"></a>INTEGRERING AV REST API

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API med grundläggande auth](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST API med klientcertifikat auth](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API med OAuth2 bärare auth](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Stöd för komponent

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autentisering av telefonfaktor](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA-autentisering](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Engångslösenord](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) som lokal katalog |  |  | X |  |
| Undersystem för Azure-e-post för e-postverifiering |  |  | X |  |
| [E-postleverantörer från tredje part](custom-email.md) |  |X  |  |  |
| [Stöd för flera språk](localization.md)|  |  | X |  |
| [Predikatvalider](predicates.md) |  |  | X | Till exempel lösenordskomplexitet. |
| [Visa kontroller](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Sidlayoutversioner

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [Support för JavaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-integrering

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parameter för frågesträng`domain_hint` |  |  | X | Tillgänglig som anspråk, kan skickas till IDP. |
| Parameter för frågesträng`login_hint` |  |  | X | Tillgänglig som anspråk, kan skickas till IDP. |
| Sätt in JSON i användarens färd via`client_assertion` | X |  |  | Kommer att vara föråldrad. |
| Infoga JSON i användarfärden som`id_token_hint` |  | X |  | Gå framåt strategi för att passera JSON. |
| [Skicka identitetsprovidertoken till programmet](idp-pass-through-custom.md) |  | X |  | Till exempel från Facebook till app. |

### <a name="session-management"></a>Hantering av sessionssessioner

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Standardleverantör för SSO-session](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Extern inloggningssessionsleverantör](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO-sessionsleverantör](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Säkerhet

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| Principnycklar- Generera, Manuell, Ladda upp |  |  | X |  |
| Policynycklar- RSA/Cert, Hemligheter |  |  | X |  |


### <a name="developer-interface"></a>Gränssnitt för utvecklare

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Ladda upp principen |  |  | X |  |
| [Programstatistik användare färdloggar](troubleshoot-with-application-insights.md) |  | X |  | Används för felsökning under utveckling.  |
| [Händelseloggar för Application Insights](application-insights-technical-profile.md) |  | X |  | Används för att övervaka användarflöden i produktionen. |


## <a name="next-steps"></a>Nästa steg

- Kontrollera de [Microsoft Graph-åtgärder som är tillgängliga för Azure AD B2C](microsoft-graph-operations.md)
- Läs mer om [anpassade principer och skillnaderna med användarflöden](custom-policy-overview.md).
