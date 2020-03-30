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
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189403"
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

### <a name="identity-providers-tokens-protocols"></a>Identitetsleverantörer, tokens, protokoll

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Till exempel Google+.  |
| IDP-OAUTH2 |  |  | X | Till exempel Facebook.  |
| IDP-OAUTH1 (Twitter) |  | X |  | Till exempel Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Stöds inte |
| IDP-SAML |  |   | X | Till exempel Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Förlitar sig part OAUTH1 |  |  |  | Stöds inte. |
| Förlitande part OAUTH2 |  |  | X |  |
| Förlitande part OIDC |  |  | X |  |
| Förlitande part SAML |  |X  |  |  |
| Förlitande part WSFED | X |  |  |  |
| REST API med grundläggande och certifikatautentisering |  |  | X | Till exempel Azure Logic Apps. |

### <a name="component-support"></a>Stöd för komponenter

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi Factor-autentisering |  |  | X |  |
| Azure Active Directory som lokal katalog |  |  | X |  |
| Undersystem för Azure-e-post för e-postverifiering |  |  | X |  |
| Stöd för flera språk|  |  | X |  |
| Predikat valideringar |  |  | X | Till exempel lösenordskomplexitet. |
| Använda e-postleverantörer från tredje part |  |X  |  |  |

### <a name="content-definition"></a>Definition av innehåll

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Felsida, api.error |  |  | X |  |
| Urvalssida för IDP, api.idpselections |  |  | X |  |
| IDP-val för registrering, api.idpselections.signup |  |  | X |  |
| Glömt lösenord, api.localaccountpasswordreset |  |  | X |  |
| Inloggning för lokalt konto, api.localaccountsignin |  |  | X |  |
| Registrering av lokalt konto, api.localaccountsignup |  |  | X |  |
| MFA sida, api.phonefactor |  |  | X |  |
| Självförsäkrad social konto registrering, api.selfasserted |  |  | X |  |
| Självpåförd profiluppdatering, api.selfasserted.profileupdate |  |  | X |  |
| Enhetlig registrerings- eller inloggningssida, api.signuporsignin, med parametern "disableSignup" |  |  | X |  |
| JavaScript / Sidlayout |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF-integrering

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parameter för frågesträng domain_hint |  |  | X | Tillgänglig som anspråk, kan skickas till IDP. |
| Parameter för frågesträng login_hint |  |  | X | Tillgänglig som anspråk, kan skickas till IDP. |
| Sätt in JSON i UserJourney via client_assertion | X |  |  | Kommer att vara föråldrad. |
| Infoga JSON i UserJourney som id_token_hint |  | X |  | Gå framåt strategi för att passera JSON. |
| Skicka IDP TOKEN till programmet |  | X |  | Till exempel från Facebook till app. |

### <a name="session-management"></a>Hantering av sessionssessioner

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| SSO-sessionsleverantör |  |  | X |  |
| Leverantör av extern inloggningssession |  |  | X |  |
| SAML SSO-sessionsleverantör |  |  | X |  |
| Standardleverantör för SSO-sessions |  |  | X |  |

### <a name="security"></a>Säkerhet

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------- | :-----------: | :-------: | :--: | ----- |
| Principnycklar- Generera, Manuell, Ladda upp |  |  | X |  |
| Policynycklar- RSA/Cert, Hemligheter |  |  | X |  |
| Ladda upp principen |  |  | X |  |

### <a name="developer-interface"></a>Gränssnitt för utvecklare

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Programinsikter UserJourney Loggar |  | X |  | Används för felsökning under utveckling.  |
| Programinsikter Händelseloggar (via orchestration steg) |  | X |  | Används för att övervaka användarflöden i produktionen. |

## <a name="next-steps"></a>Nästa steg

Läs mer om [anpassade principer och skillnaderna med användarflöden](custom-policy-overview.md).
