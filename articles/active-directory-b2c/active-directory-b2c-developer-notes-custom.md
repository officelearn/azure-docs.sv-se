---
title: Kommentarer för utvecklare för anpassade principer – Azure Active Directory B2C | Microsoft Docs
description: Information för utvecklare på Konfigurera och underhålla Azure AD B2C med anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cf9c6f6a54c38f00e477e2a9d62e72ab5faccdef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60317980"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Kommentarer för utvecklare för anpassade principer i Azure Active Directory B2C

Anpassad princip för konfiguration i Azure Active Directory B2C är nu allmänt tillgänglig. Den här metoden för konfiguration är avsett för avancerade identity-utvecklare som skapar komplexa identitetslösningar. Anpassade principer gör kraften hos de Identitetsramverk som är tillgängliga i Azure AD B2C-klienter. Avancerade identitets-utvecklare som använder anpassade principer bör planera att investera tid slutförs genomgångarna och läsa referensdokument.

De flesta av de tillgängliga alternativen för anpassad princip är nu allmänt tillgängliga, finns men det underliggande funktioner, till exempel tekniska profiltyperna och innehållsdefinition API: er som finns i olika faser i programvarans livscykel. Många fler kommer. Tabellen nedan anger nivån för tillgänglighet på en mer detaljerad nivå.  

## <a name="features-that-are-generally-available"></a>Funktioner som är allmänt tillgängliga

- Författare och överföra anpassad autentisering användaren utbildning genom att använda anpassade principer.  
    - Beskriv användaren resor stegvisa som utbyten mellan Anspråksproviders.  
    - Definiera villkorlig branchning i utbildning för användaren.  
- Samverka med REST API-aktiverade tjänster i din utbildning för anpassad autentisering-användaren.  
- Federera med identitetsleverantörer som är kompatibla med OpenIDConnect-protokollet.  
- Federera med identitetsleverantörer som följer SAML 2.0-protokollet.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Ansvaret för anpassad princip funktionsuppsättningen utvecklare

Manuell konfiguration ger lägre nivå åtkomst till den underliggande plattformen av Azure AD B2C och resulterar i att skapa ett unikt förlitar sig på framework. Många möjliga varianter av anpassade identitetsprovidrar, förtroenderelationer integreringar med externa tjänster och stegvisa arbetsflöden kräver en metodisk strategi design och konfiguration. 

Utvecklare som använder funktionen för anpassade principuppsättningen måste uppfylla följande riktlinjer:

- Bekanta dig med den anpassade principer och nyckel/hemligheter management configuration språk. Mer information finns i [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Bli ägare till scenarier och anpassade integreringar. Dokumentera ditt arbete och informera organisationen live-webbplatsen.  
- Utföra metodisk scenariotestning. 
- Följ programutveckling och bästa praxis med minst en utvecklings- och testmiljö och en produktionsmiljö. 
- Håll dig informerad om ny utveckling från identitetsleverantörer och tjänster som du integrerar med. Till exempel Håll koll på ändringar i hemligheter och planerade och oplanerade ändringar till tjänsten. 
- Konfigurera övervakning av aktiva och övervaka svarstiden för produktionsmiljöer. Mer information om hur du integrerar med Application Insights finns [Azure Active Directory B2C: Samla in loggar](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Håll kontakta e-postadresser i Azure-prenumeration och hålls nere till e-post med Microsoft team på aktiv webbplats. 
- Vidta lämpliga åtgärder när vi rekommenderar att göra det genom Microsofts team för live-webbplatser.

## <a name="terms-for-features-in-public-preview"></a>Villkor för funktioner i offentlig förhandsversion

- Vi rekommenderar att du kan använda funktionerna offentlig förhandsgranskning i utvärderingssyfte. 
- Serviceavtal (SLA) gäller inte för de offentliga förhandsfunktionerna.
- Supportärenden för offentlig förhandsversionsfunktioner kan lämnas via vanliga supportkanaler. 

## <a name="features-by-stage-and-known-issues"></a>Funktioner av steg och kända problem

Anpassad princip/identitet upplevelse Framework-funktioner är under konstant och snabb utveckling. I följande tabell är ett index över funktioner och komponenttillgänglighet.

### <a name="identity-providers-tokens-protocols"></a>Identitets-Providers, token, protokoll

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Till exempel Google +.  |
| IDP-OAUTH2 |  |  | X | Till exempel Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Till exempel Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Stöds inte |
| IDP-SAML |  |   | X | Till exempel Salesforce, AD FS. |
| IDP-WSFED | X |  |  |  |
| Förlitande part OAUTH1 |  |  |  | Stöds ej. |
| Förlitande part OAUTH2 |  |  | X |  |
| Förlitande part OIDC |  |  | X |  |
| Förlitande part SAML | X |  |  |  |
| Förlitande part WSFED | X |  |  |  |
| REST-API med basic- och certifikatbaserad autentisering |  |  | X | Till exempel Azure Logic Apps. |

### <a name="component-support"></a>Stöd för komponent

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi Factor Authentication |  |  | X |  |
| Azure Active Directory som lokal katalog |  |  | X |  |
| Azure e-undersystem för e-Postverifiering |  |  | X |  |
| Stöd för flera språk|  |  | X |  |
| Predikatet verifieringar |  |  | X | Till exempel lösenordskomplexitet. |
| Med hjälp av e-tredjepartsleverantörer | X |  |  |  |

### <a name="content-definition"></a>Content Definition

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | ----------- | ------- | -- | ----- |
| Felsidan, api.error |  |  | X |  |
| Sidan för val av IDP: N, api.idpselections |  |  | X |  |
| Val av IDP: N för registrering, api.idpselections.signup |  |  | X |  |
| Forgot Password, api.localaccountpasswordreset |  |  | X |  |
| Lokalt konto för inloggning, api.localaccountsignin |  |  | X |  |
| Registrering för, api.localaccountsignup för lokalt konto |  |  | X |  |
| MFA-sida, api.phonefactor |  |  | X |  |
| Registrering för, api.selfasserted för självkontrollerad socialt konto |  |  | X |  |
| Lokal verifieringsvillkor profil update api.selfasserted.profileupdate |  |  | X |  |
| Enhetlig registrering eller på inloggningssidan api.signuporsignin med parametern ”disableSignup” |  |  | X |  |
| JavaScript / sidan kontrakt |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-integrering

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | ----------- | ------- | -- | ----- |
| Fråga sträng parametern domain_hint |  |  | X | Tillgänglig som anspråk, kan skickas till IDP: N. |
| Fråga sträng parametern login_hint |  |  | X | Tillgänglig som anspråk, kan skickas till IDP: N. |
| Infoga JSON i UserJourney via client_assertion | X |  |  | Upphör att gälla. |
| Infoga JSON i UserJourney som id_token_hint |  | X |  | Gå framåt strategi för att skicka JSON. |
| Skicka TOKEN IDP till programmet |  | X |  | Till exempel från Facebook till appen. |

### <a name="session-management"></a>Sessionshantering

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | ----------- | ------- | -- | ----- |
| Provider för SSO-Session |  |  | X |  |
| Externa Inloggningsprovider för Session |  |  | X |  |
| Provider för SAML SSO-Session |  |  | X |  |
| Standardprovidern för SSO-Session |  |  | X |  |

### <a name="security"></a>Säkerhet

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | ----------- | ------- | -- | ----- |
| Princip för nycklar – generera, manuell, ladda upp |  |  | X |  |
| Princip för nycklar - RSA/Cert hemligheter |  |  | X |  |
| Principuppladdning |  |  | X |  |

### <a name="developer-interface"></a>Developer-gränssnitt

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | ----------- | ------- | -- | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| UserJourney loggar i Application Insights |  | X |  | Används för felsökning under utveckling.  |
| Application Insights-händelseloggarna (via orchestration-steg) |  | X |  | Används för att övervaka användarflöden i produktion. |

## <a name="next-steps"></a>Nästa steg
[Läs mer om anpassade principer och skillnader med användarflöden](active-directory-b2c-overview-custom.md).
