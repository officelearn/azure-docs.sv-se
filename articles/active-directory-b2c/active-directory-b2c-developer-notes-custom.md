---
title: Kommentarer för utvecklare om hur du använder anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Information för utvecklare på Konfigurera och underhålla Azure AD B2C med anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0a5255974c7399f9307d8b06a58f4f977be89829
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55172892"
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Viktig information för Azure Active Directory B2C förhandsversion för anpassad princip
Anpassad princip funktionsuppsättningen är nu tillgänglig för utvärdering under förhandsversion för alla Azure Active Directory B2C (Azure AD B2C) kunder. Den här funktionsuppsättningen är avsett för avancerade identity-utvecklare som skapar de mest komplexa identity-lösningarna.  

Idag kräver den här funktionsuppsättningen utvecklare att konfigurera Identitetsramverk direkt via XML-fil redigering. Den här metoden för konfiguration är kraftfulla och komplexa. Avancerade identitets-utvecklare som använder den Identitetsramverk bör planera att investera tid slutförs genomgångarna och läsa referensdokument. 

## <a name="features-included-in-this-public-preview"></a>Funktioner som ingår i den här förhandsversionen
Med de nya funktionerna som introducerades i offentlig förhandsversion, kan utvecklare göra följande:<br>

* Skapa och överför anpassad autentisering användaren resor med hjälp av anpassade principer. 
   * Beskriv användaren resor stegvisa som utbyten mellan Anspråksproviders. 
   * Definiera villkorlig branchning i utbildning för användaren. * Integrera REST API-aktiverade tjänster i din utbildning för anpassad autentisering-användaren.  
* Lägg till federation med identitetsleverantörer som är kompatibla med OpenIDConnect standard. <br>
* Lägg till federation med identitetsleverantörer som följer SAML 2.0-protokollet. 

## <a name="terms-of-the-public-preview"></a>Villkoren i offentlig förhandsversion

* Vi rekommenderar att du kan använda de nya funktionerna i utvärderingssyfte.<br>
* De nya funktionerna är inte avsedda att användas i en produktionsmiljö.<br>
* Servicenivåavtal (SLA) gäller inte för de nya funktionerna. <br>
* Supportärenden kan lämnas via vanliga supportkanaler. <br>
* Det finns inget framtida datum för allmän tillgänglighet.<br>
* Enligt vår bedömning, och om du av någon anledning, kan Microsoft flagga och avvisa eller begränsa scenarier och användaren utbildning som överskrider omfånget för auktoriserad för Azure AD B2C-produkt som fungerar som en kund identitets- och hantering av Kundidentitet plattform.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Ansvaret för anpassad princip funktionsuppsättningen utvecklare
Manuell konfiguration ger lägre nivå åtkomst till den underliggande Azure AD B2C-plattformen och resulterar i att skapa ett unikt helt anpassningsbara förtroende-ramverk. Möjliga varianter av anpassade identitetsprovidrar, förtroenderelationer integreringar med externa tjänster och stegvisa arbetsflöden Placera större krav på avancerade utvecklare som använder dem.

Om du vill utnyttja helt offentlig förhandsversion, föreslår vi att utvecklare som använder funktionen för anpassade principuppsättningen följa följande riktlinjer:
* Bekanta dig med Identitetsramverk och nyckel/hemligheter management configuration språk.
* Bli ägare till scenarier och anpassade integreringar.
* Utföra metodisk scenariotestning.
* Följ programutveckling och bästa praxis med minst en utvecklings- och testmiljö och en produktionsmiljö.
* Håll dig informerad om ny utveckling från identitetsleverantörer och tjänster som du integrerar med. Till exempel Håll koll på ändringar i hemligheter och planerade och oplanerade ändringar till tjänsten.
* Konfigurera övervakning av aktiva och övervaka svarstiden för produktionsmiljöer.
* Håll kontakta e-postadresser i Azure-prenumeration och hålls nere till e-post med Microsoft team på aktiv webbplats.
* Vidta lämpliga åtgärder när vi rekommenderar att göra det genom Microsofts team för live-webbplatser. 

## <a name="features-by-stage-and-known-issues"></a>Funktioner av steg och kända problem
Den anpassade principen/Identitetsramverk funktioner är under konstant och snabb utveckling.  Den här tabellen är ett index över funktioner/komponenttillgänglighet.

Ställ dina frågor i Stack Overflow på [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Identitets-Providers, token, protokoll
Gränssnitt med externa komponenter och program

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | till exempel Google + |
| IDP-OAUTH2 |  | x |  | till exempel Facebook  |
| IDP-OAUTH1 |  | x |  | till exempel Twitter |
| IDP-SAML |  | x |  | till exempel Salesforce, AD FS |
| IDP-WSFED | x |  |  |  |
| Förlitande part OAUTH |  | x |  |  |
| Förlitande part OIDC |  | x |  |  |
| Förlitande part SAML | x |  |  |  |
| Förlitande part WSFED | x |  |  |  |
| REST-API med autentisering för basic och certifikat. |  | x |  | till exempel Azure Functions |


### <a name="component-support"></a>Stöd för komponent


| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi Factor Authentication |  | x |  |  |
| Azure Active Directory som lokal katalog |  | x |  |  |
| Azure e-undersystem för 2FA |  | x |  |  |
| Stöd för flera språk|  | x |  |  |
| Lösenordskomplexitet | x |  |  |  |


### <a name="content-definition"></a>Content Definition

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Felsidan, api.error |  | x |  |  |
|   Sidan för val av IDP: N, api.idpselections |  | x |  |  |
|   Val av IDP: N för registrering, api.idpselections.signup |  | x |  |  |
|   Forgot Password, api.localaccountpasswordreset |  | x |  |  |
|   Lokalt konto för inloggning, api.localaccountsignin |  | x |  |  |
|   Registrering för, api.localaccountsignup för lokalt konto |  | x |  |  |
|   MFA-sida, api.phonefactor |  | x |  |  |
|   Lokal verifieringsvillkor – till exempel registrering för, api.selfasserted för socialt konto |  | x |  |  |
|   Lokal verifieringsvillkor profil update api.selfasserted.profileupdate |  | x |  |  |
|   Enhetlig registrering eller på inloggningssidan, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>App-IEF-integrering
| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Fråga sträng parametern id_token_hint | x |  |  |  |
| Fråga sträng parametern domain_hint |  | x |  | tillgängliga anspråk, kan skickas till IDP |
| Fråga sträng parametern login_hint |  | x |  | tillgängliga anspråk, kan skickas till IDP |
| Infoga JSON i UserJourney via client_assertion | x |  |  | upphör att gälla |
| Infoga JSON i UserJourney som id_token_hint | x |  |  | Gå framåt metod för att skicka JSON |


### <a name="session-management"></a>Sessionshantering

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------|-------------|---------|----|-------|
| Provider för SSO-Session |  | x |  |  |
| Externa Inloggningsprovider för Session |  | x |  |  |
| Provider för SAML SSO-Session |  | x |  |  |


### <a name="security"></a>Säkerhet
| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------------------|-------------|---------|----|-------|
| Princip för nycklar – generera, manuell, ladda upp |  | x |  |  |
| Princip för nycklar - RSA/Cert hemligheter |  | x |  |  |


### <a name="developer-interface"></a>Developer-gränssnitt
| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal-IEF UX |  | x |  |  |
| UserJourney loggar i Application Insights  |  | x |  |  |
| Application Insights-händelseloggar |x|  |  |  |



## <a name="next-steps"></a>Nästa steg
[Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).
