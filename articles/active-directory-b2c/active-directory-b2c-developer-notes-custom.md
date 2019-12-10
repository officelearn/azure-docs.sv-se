---
title: Developer-kommentarer för anpassade principer
titleSuffix: Azure AD B2C
description: Anteckningar för utvecklare om hur man konfigurerar och underhåller Azure AD B2C med anpassade principer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edbd31434715c380badf15118b0779885aed700f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949763"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Developer-kommentarer för anpassade principer i Azure Active Directory B2C

Anpassad princip konfiguration i Azure Active Directory B2C är nu allmänt tillgänglig. Den här konfigurations metoden riktar sig till avancerade identitets utvecklare som skapar komplexa identitets lösningar. Anpassade principer gör kraften i det tillgängliga ramverket för identitets miljö i Azure AD B2C klienter.
Avancerade identitets utvecklare som använder anpassade principer bör planera att investera lite tid genom att gå igenom och läsa referens dokument.

De flesta anpassade princip alternativ är nu allmänt tillgängliga, men det finns underliggande funktioner, till exempel tekniska profil typer och API: er för innehålls definition som finns i olika faser i program varu livs cykeln. Många fler kommer. Tabellen nedan anger nivån på tillgänglighet på en mer detaljerad nivå.

## <a name="features-that-are-generally-available"></a>Funktioner som är allmänt tillgängliga

- Skapa och ladda upp användar transporter för anpassad autentisering med hjälp av anpassade principer.
    - Beskriv användar resan steg för steg som utbyten mellan anspråks leverantörer.
    - Definiera villkorlig grenning i användar resor.
- Samverka med REST API-aktiverade tjänster i dina anpassade användar resor för autentisering.
- Federera med identitets leverantörer som är kompatibla med OpenIDConnect-protokollet.
- Federera med identitets leverantörer som följer SAML 2,0-protokollet.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Ansvars områden för anpassad princip funktion – Ställ in utvecklare

Den manuella princip konfigurationen ger lägre åtkomst till den underliggande plattformen av Azure AD B2C och resulterar i att ett unikt förtroende ramverk skapas. Många möjliga permutationer av anpassade identitets leverantörer, förtroende relationer, integreringar med externa tjänster och steg för steg-arbets flöden kräver en metodisk metod för design och konfiguration.

Utvecklare som använder den anpassade princip inställningen bör följa följande rikt linjer:

- Bekanta dig med konfigurations språket för anpassade principer och hantering av nyckel/hemligheter. Mer information finns i [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Bli ägare till scenarier och anpassade integreringar. Dokumentera ditt arbete och informera din Live site-organisation.
- Utför metodiska scenario testning.
- Följ program utvecklings-och mellanlagringsplatsen med bästa praxis med minst en utvecklings-och test miljö och en produktions miljö.
- Håll dig informerad om ny utveckling från identitets leverantörer och tjänster som du integrerar med. Du kan till exempel hålla reda på ändringar i hemligheter och schemalagda och oplanerade ändringar i tjänsten.
- Konfigurera aktiv övervakning och övervaka svars tiderna för produktions miljöer. Mer information om hur du integrerar med Application Insights finns i [Azure Active Directory B2C: samla in loggar](active-directory-b2c-custom-guide-eventlogger-appins.md).
- Behåll kontaktens e-postadresser som är aktuella i Azure-prenumerationen och håll dig uppdaterad med e-postmeddelandena från Microsoft Live-site.
- Ta åtgärds tid när du uppmanas att göra det av Microsoft Live-site-teamet.

## <a name="terms-for-features-in-public-preview"></a>Villkor för funktioner i offentlig för hands version

- Vi rekommenderar att du bara använder de offentliga för hands funktionerna i utvärderings syfte.
- Service avtal (service avtal) gäller inte för de offentliga för hands versions funktionerna.
- Support förfrågningar för offentliga för hands versions funktioner kan arkiveras via vanliga Support kanaler.

## <a name="features-by-stage-and-known-issues"></a>Funktioner efter steg och kända problem

Funktioner för anpassad princip/identitets upplevelse Framework är under konstant och snabb utveckling. Följande tabell är ett index över funktioner och tillgänglighet för komponenter.

### <a name="identity-providers-tokens-protocols"></a>Identitets leverantörer, token, protokoll

| Funktion | Utveckling | Förhandsversion | Allmänt tillgänglig | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Till exempel Google +.  |
| IDP-OAUTH2 |  |  | X | Till exempel Facebook.  |
| IDP – OAUTH1 (Twitter) |  | X |  | Till exempel Twitter. |
| IDP – OAUTH1 (t. ex. Twitter) |  |  |  | Stöds inte |
| IDP-SAML |  |   | X | Till exempel Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| OAUTH1 för förlitande part |  |  |  | Stöds ej. |
| OAUTH2 för förlitande part |  |  | X |  |
| OIDC för förlitande part |  |  | X |  |
| SAML för förlitande part | X |  |  |  |
| WSFED för förlitande part | X |  |  |  |
| REST API med Basic-och certifikatautentisering |  |  | X | Till exempel Azure Logic Apps. |

### <a name="component-support"></a>Komponent stöd

| Funktion | Utveckling | Förhandsversion | Allmänt tillgänglig | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Azure Active Directory som lokal katalog |  |  | X |  |
| Azure E-mail Subsystem för e-postverifiering |  |  | X |  |
| Stöd för flera språk|  |  | X |  |
| Predikat-validering |  |  | X | Till exempel komplexitet av lösen ord. |
| Använda e-postleverantörer från tredje part | X |  |  |  |

### <a name="content-definition"></a>Innehålls definition

| Funktion | Utveckling | Förhandsversion | Allmänt tillgänglig | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Felsida, API. fel |  |  | X |  |
| IDP-urvals sida, API. idpselections |  |  | X |  |
| IDP-val för registrering, API. idpselections. signup |  |  | X |  |
| Glömt lösen ord, API. localaccountpasswordreset |  |  | X |  |
| Inloggning för lokalt konto, API. localaccountsignin |  |  | X |  |
| Registrera lokalt konto, API. localaccountsignup |  |  | X |  |
| MFA-sida, API. phonefactor |  |  | X |  |
| Självkontrollerad registrering av sociala konton, API. selfasserted |  |  | X |  |
| Självkontrollerad profil uppdatering, API. selfasserted. profileupdate |  |  | X |  |
| Unified signup eller inloggnings sida, API. signuporsignin med parametern "disableSignup" |  |  | X |  |
| Java Script/sidlayout |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-integrering

| Funktion | Utveckling | Förhandsversion | Allmänt tillgänglig | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parameter för frågesträng domain_hint |  |  | X | Tillgängligt som anspråk kan skickas till IDP. |
| Parameter för frågesträng login_hint |  |  | X | Tillgängligt som anspråk kan skickas till IDP. |
| Infoga JSON i UserJourney via client_assertion | X |  |  | Är föråldrad. |
| Infoga JSON i UserJourney som id_token_hint |  | X |  | Go-Forward-metoden för att skicka JSON. |
| Skicka IDP-TOKEn till programmet |  | X |  | Till exempel från Facebook till app. |

### <a name="session-management"></a>Sessionshantering

| Funktion | Utveckling | Förhandsversion | Allmänt tillgänglig | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO-sessionsbiljett |  |  | X |  |
| Provider för extern inloggningssession |  |  | X |  |
| SAML SSO-sessionsbiljett |  |  | X |  |
| Standardprovider för SSO-session |  |  | X |  |

### <a name="security"></a>Säkerhet

| Funktion | Utveckling | Förhandsversion | Allmänt tillgänglig | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| Princip nycklar – generera, manuell, uppladdning |  |  | X |  |
| Princip nycklar – RSA/cert, hemligheter |  |  | X |  |
| Princip uppladdning |  |  | X |  |

### <a name="developer-interface"></a>Gränssnittet för utvecklare

| Funktion | Utveckling | Förhandsversion | Allmänt tillgänglig | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal – IEF UX |  |  | X |  |
| Application Insights UserJourney-loggar |  | X |  | Används för fel sökning under utveckling.  |
| Application Insights händelse loggar (via Orchestration-steg) |  | X |  | Används för att övervaka användar flöden i produktionen. |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [anpassade principer och skillnaderna med användar flöden](active-directory-b2c-overview-custom.md).
