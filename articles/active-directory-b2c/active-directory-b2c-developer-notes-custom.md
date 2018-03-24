---
title: 'Azure Active Directory B2C: Kommentarer för utvecklare om hur du använder anpassade principer | Microsoft Docs'
description: Information för utvecklare på Konfigurera och underhålla Azure AD B2C med anpassade principer
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/13/2017
ms.author: davidmu
ms.openlocfilehash: cf76f158d7e87a2f2f902551fdc751b295b6915b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Viktig information för en förhandsversion av Azure Active Directory B2C anpassad princip
Funktionsuppsättningen anpassad princip är nu tillgänglig för utvärdering under förhandsversion för alla Azure Active Directory B2C (Azure AD B2C) kunder. Den här funktionsuppsättningen är inriktad på avancerade identitet utvecklare som bygger mest komplexa identitetslösningar.  

Idag kräver den här funktionsuppsättningen konfigurera identitet upplevelse Framework direkt via redigering av XML-filen. Den här metoden i konfigurationen är kraftfulla och komplexa. Avancerade identitet utvecklare som använder identitet upplevelse Framework bör planera att investera lite tid att slutföra genomgångar och läsa referensdokument. 

## <a name="features-included-in-this-public-preview"></a>Funktioner som ingår i denna förhandsversion
Med de nya funktionerna som introducerades i public preview kan utvecklare göra följande:<br>

* Författare och överföra anpassad autentisering användaren transporter med hjälp av anpassade principer. 
   * Beskriv användaren resor stegvisa som utbyten mellan Anspråksproviders. 
   * Definiera villkorliga grenar i resor för användaren. 
* Integrera REST API-aktiverade tjänster i din användare resor för anpassad autentisering.  
* Lägg till federation med identitetsleverantörer som är kompatibla med OpenIDConnect standard. <br>
* Lägg till federation med identitetsleverantörer som följer SAML 2.0-protokollet. 

## <a name="terms-of-the-public-preview"></a>Villkoren i public preview

* Vi rekommenderar att du kan använda de nya funktionerna i utvärderingssyfte.<br>
* Nya funktioner är inte avsedd att användas i en produktionsmiljö.<br>
* Servicenivåavtal (SLA) gäller inte för de nya funktionerna. <br>
* Supportärenden kan registreras via vanlig supportkanaler. <br>
* Det finns inga framtida datum för allmän tillgänglighet.<br>
* Vår gottfinnande och av någon anledning, kan Microsoft flagga och avvisa eller begränsa scenarier och användaren resor som överskrider omfånget för Azure AD B2C produkten auktoriserad som fungerar som en kund identitets- och (CIAM) plattform.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Ansvaret för anpassad princip funktionsuppsättningen utvecklare
Manuell konfiguration ger lågnivå-åtkomst till den underliggande plattformen för Azure AD B2C och resulterar i att skapa ett unikt, helt anpassningsbar förtroende ramverk. Möjliga varianter av anpassade identitetsleverantörer, Betrodda relationer integration med externa tjänster och stegvisa arbetsflöden Placera större krav på avancerade utvecklare som använder dem.

Om du vill utnyttja alla fördelar som förhandsversion, föreslår vi att utvecklare förbrukar funktionsuppsättningen anpassad princip följer riktlinjerna nedan:
* Bekanta dig med den identitet upplevelse Framework och nyckel/hemligheter management configuration språk.
* Bli ägare till scenarier och anpassad integrering.
* Utföra metodisk scenariotestning.
* Följ programvaruutveckling och mellanlagring metodtips med minst en utvecklings- och testmiljö och en produktionsmiljö.
* Håll dig informerad om förändringar från leverantörer och tjänster som du integrerar med. Till exempel hålla koll på ändringar i hemligheter och planerade och oplanerade ändringar av tjänsten.
* Konfigurera active övervakning och övervaka svarstiderna hos produktionsmiljöer.
* Behåll kontakta e-postadresser aktuella i Azure-prenumeration och förblir kan svara på e-postmeddelanden Microsoft live-plats-teamet.
* Vidta lämpliga åtgärder när du gör detta genom att Microsoft live-plats-teamet. 

## <a name="features-by-stage-and-known-issues"></a>Funktioner med steg och kända problem
Anpassad princip/identitet upplevelse Framework funktioner är under konstant och snabb utveckling.  Den här tabellen är ett index över funktioner/komponenttillgänglighet.

Ställa frågor i stackspill på [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Identitet Providers, token-protokoll
Gränssnitt med externa komponenter och program

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | till exempel Google + |
| IDP-OAUTH2 |  | x |  | till exempel Facebook  |
| IDP-OAUTH1 |  | x |  | till exempel Twitter |
| IDP-SAML |  | x |  | till exempel Salesforce ADFS |
| IDP-WSFED | x |  |  |  |
| Förlitande part OAUTH |  | x |  |  |
| Förlitande part OIDC |  | x |  |  |
| Förlitande part SAML | x |  |  |  |
| Förlitande part WSFED | x |  |  |  |
| REST-API med auth basic och certifikat. |  | x |  | till exempel Azure Functions |


### <a name="component-support"></a>Stöd för komponent


| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi Factor Authentication |  | x |  |  |
| Azure Active Directory som lokal katalog |  | x |  |  |
| Azure e-undersystem för 2FA |  | x |  |  |
| Stöd för flera språk|  | x |  |  |
| Lösenordskomplexitet | x |  |  |  |


### <a name="content-definition"></a>Definition av innehåll

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Felsidan, api.error |  | x |  |  |
|   IDP urvalssida, api.idpselections |  | x |  |  |
|   IDP val för registrering, api.idpselections.signup |  | x |  |  |
|   Har glömt lösenord, api.localaccountpasswordreset |  | x |  |  |
|   Lokalt konto inloggning, api.localaccountsignin |  | x |  |  |
|   Lokalt konto registrering api.localaccountsignup |  | x |  |  |
|   MFA-sidan, api.phonefactor |  | x |  |  |
|   Automatisk vars – till exempel sociala konto sig upp api.selfasserted |  | x |  |  |
|   Automatisk vars Profiluppdatering, api.selfasserted.profileupdate |  | x |  |  |
|   Enhetlig registreringen eller inloggningssidan, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>App-IEF integrering
| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Frågan sträng parametern id_token_hint | x |  |  |  |
| Frågan sträng parametern domain_hint |  | x |  | tillgängliga som anspråk, kan skickas till IDP |
| Frågan sträng parametern login_hint |  | x |  | tillgängliga som anspråk, kan skickas till IDP |
| Infoga JSON i UserJourney via client_assertion | x |  |  | att bli inaktuell |
| Infoga JSON i UserJourney som id_token_hint | x |  |  | Gå framåt metod för att skicka JSON |


### <a name="session-management"></a>Sessionshantering

| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------|-------------|---------|----|-------|
| Provider för SSO-Session |  | x |  |  |
| Externa inloggningen Sessionsprovider |  | x |  |  |
| SAML SSO Sessionsprovider |  | x |  |  |


### <a name="security"></a>Säkerhet
| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------------------|-------------|---------|----|-------|
| Principen generera nycklar, manuell, överför |  | x |  |  |
| Principen nycklar - RSA/Cert hemligheter |  | x |  |  |


### <a name="developer-interface"></a>Gränssnitt för utvecklare
| Funktion | Utveckling | Förhandsversion | Allmän tillgänglighet (GA) | Anteckningar |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal-IEF UX |  | x |  |  |
| Application Insights UserJourney Logs  |  | x |  |  |
| Application Insights-händelseloggarna |x|  |  |  |



## <a name="next-steps"></a>Nästa steg
[Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).
