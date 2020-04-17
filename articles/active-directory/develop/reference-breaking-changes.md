---
title: Referens för att bryta ändringar i Azure Active Directory
description: Lär dig mer om ändringar som gjorts i Azure AD-protokoll som kan påverka ditt program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a60b927f7239818b582ffcd85ddb4b7d69594482
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535969"
---
# <a name="whats-new-for-authentication"></a>Vad är nytt för autentisering?

>Få ett meddelande om uppdateringar av den här sidan. Lägg bara till [den här webbadressen](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) i rss-flödesläsaren.

Autentiseringssystemet ändrar och lägger till funktioner kontinuerligt för att förbättra efterlevnaden av säkerhet och standarder. Om du vill hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om följande information:

- De senaste funktionerna
- Kända problem
- Ändringar av protokoll
- Föråldrade funktioner

> [!TIP]
> Den här sidan uppdateras regelbundet, så besök ofta. Om inget annat anges införs dessa ändringar endast för nyregistrerade ansökningar.

## <a name="upcoming-changes"></a>Kommande förändringar

Ingen är planerad just nu.  Se nedan för de förändringar som är i eller kommer till produktion.

## <a name="march-2020"></a>Mars 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Användarlösenord begränsas till 256 tecken.

**Gäller från och med 13**mars 2020

**Slutpunkter påverkas:** Både v1.0 och v2.0

**Protokollet påverkas**: Alla användarflöden.

Användare med lösenord som är längre än 256 tecken som loggar in direkt på Azure AD (i motsats till ett federerat IDP som ADFS) kan inte logga in från och med den 13 mars 2020 och bli ombedda att återställa sitt lösenord i stället.  Administratörer kan få förfrågningar om att återställa användarnas lösenord.

Felet i inloggningsloggarna är AADSTS 50052: InvalidPasswordExceedsMaxLength

Meddelande:`The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Sanering:

Användaren kan inte logga in eftersom deras lösenord överskrider den tillåtna maximala längden. De bör kontakta sin administratör för att återställa lösenordet. Om SSPR är aktiverat för sin klientorganisation kan de återställa sitt lösenord genom att följa länken "Glömt ditt lösenord".



## <a name="february-2020"></a>Februari 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Tomma fragment läggs till i varje HTTP-omdirigering från inloggningsslutpunkten.

Gäller från och **med 8**februari 2020

**Slutpunkter påverkas:** Både v1.0 och v2.0

**Protokoll påverkas:** OAuth och OIDC flöden som använder response_type =query - detta täcker [auktoriseringskoden flödet](v2-oauth2-auth-code-flow.md) i vissa fall, och det [implicita flödet](v2-oauth2-implicit-grant-flow.md).

När ett autentiseringssvar skickas från login.microsoftonline.com till ett program via HTTP-omdirigering lägger tjänsten till ett tomt fragment i svars-URL:en.  Detta förhindrar en klass av omdirigeringsattacker genom att se till att webbläsaren utplånar alla befintliga fragment i autentiseringsbegäran.  Inga appar bör ha ett beroende av det här beteendet.


## <a name="august-2019"></a>Augusti 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>POST form semantik kommer att verkställas striktare - utrymmen och citat kommer att ignoreras

**Gäller från och med 2**september 2019

**Slutpunkter påverkas:** Både v1.0 och v2.0

**Protokollet påverkas:** Anywhere POST används[(klientautentiseringsuppgifter,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) [auktoriseringskod inlösen,](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow) [ROPC](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)och [uppdatera token inlösen)](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)

Från och med veckan den 9/2 valideras autentiseringsbegäranden som använder POST-metoden med strängare HTTP-standarder.  Blanksteg och dubbelcitat (") tas inte längre bort från formulärvärden för begäran. Dessa ändringar förväntas inte bryta några befintliga klienter och säkerställer att begäranden som skickas till Azure AD hanteras på ett tillförlitligt sätt varje gång. I framtiden (se ovan) planerar vi att dessutom avvisa dubblettparametrar och ignorera strukturlistan i begäranden.

Exempel:

Idag `?e=    "f"&g=h` är tolkas identiskt `?e=f&g=h` som `e`  ==  `f`- så .  Med denna förändring skulle det nu tolkas så att `e`  ==  `    "f"` - detta är osannolikt att vara ett giltigt argument, och begäran skulle nu misslyckas.


## <a name="july-2019"></a>Juli 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>App-token för program med en klientorganisation utfärdas endast om klientappen finns i resursklienten

**Gäller från och med 26**juli 2019

**Slutpunkter påverkas:** Både [v1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) och [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protokollet påverkas**: [Klientreferenser (endast apptoken)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

En säkerhetsändring gick live 26 juli som ändrar hur app-bara token (via klientautentiseringsbe beviljande) utfärdas. Tidigare kunde program hämta token för att anropa någon annan app, oavsett närvaro i klienten eller roller som godkänts för det programmet.  Detta har uppdaterats så att klientprogrammet måste finnas i resursklientationen för resurser (kallas ibland webb-API:er) som en enda klient (standardvärdet).  Observera att befintligt medgivande mellan klienten och API:et fortfarande inte krävs, `roles` och appar bör fortfarande göra sina egna auktoriseringskontroller för att säkerställa att ett anspråk finns och innehåller det förväntade värdet för API:et.

Felmeddelandet för det här scenariot lyder för närvarande:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Om du vill åtgärda problemet använder du administratörssamtyckesupplevelsen för att skapa klientprogramtjänstens huvudnamn i din klientorganisation eller skapa det manuellt.  Det här kravet säkerställer att klienten har gett programmet behörighet att verka inom klienten.

#### <a name="example-request"></a>Exempelbegäran

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`I det här exemplet är resursklienten (myndigheten) contoso.com, resursappen `gateway.contoso.com/api` är en app med en klientorganisation som kallas för Contoso-klienten och klientappen är `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`.  Om klientappen har ett tjänsthuvudnamn inom Contoso.com kan den här begäran fortsätta.  Om den inte gör det, men då begäran kommer att misslyckas med felet ovan.

Om Contoso gateway-appen var ett program med flera innehavare skulle begäran dock fortsätta oavsett vilken klientapp som har ett tjänsthuvudnamn inom Contoso.com.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Omdirigera URI:er kan nu innehålla frågesträngparametrar

**Gäller från och med 22**juli 2019

**Slutpunkter påverkas:** Både v1.0 och v2.0

**Protokoll som påverkas**: Alla flöden

Per [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)kan Azure AD-program nu registrera och använda omdirigerings-(svar) URI:er med statiska frågeparametrar (till exempel `https://contoso.com/oauth2?idp=microsoft`) för OAuth 2.0-begäranden.  Dynamiska omdirigerings-URI:er är fortfarande förbjudna eftersom de utgör en säkerhetsrisk, `state` och detta kan inte användas för att behålla tillståndsinformation över en autentiseringsbegäran - för det använder parametern.

Den statiska frågeparametern är föremål för strängmatchning för omdirigerings-URI:er som alla andra delar av omdirigerings-URI- -om ingen sträng har registrerats som matchar den URI-avkodade redirect_uri, kommer begäran att avvisas.  Om URI finns i appregistreringen används hela strängen för att omdirigera användaren, inklusive den statiska frågeparametern.

Observera att vid denna tidpunkt (slutet av juli 2019) blockerar appregistrerings-UX i Azure-portalen fortfarande frågeparametrar.  Du kan dock redigera programmanifestet manuellt för att lägga till frågeparametrar och testa detta i appen.


## <a name="march-2019"></a>Mars 2019

### <a name="looping-clients-will-be-interrupted"></a>Looping-klienter avbryts

**Gäller från och med 25**mars 2019

**Slutpunkter påverkas:** Både v1.0 och v2.0

**Protokoll som påverkas**: Alla flöden

Klientprogram kan ibland missköter sig och utfärda hundratals av samma inloggningsbegäran under en kort tidsperiod.  Dessa begäranden kan eller inte lyckas, men de bidrar alla till dålig användarupplevelse och ökade arbetsbelastningar för IDP, vilket ökar svarstiden för alla användare och minskar tillgängligheten för IDP.  Dessa program fungerar utanför gränserna för normal användning, och bör uppdateras för att fungera korrekt.

Klienter som utfärdar dubblettbegäranden `invalid_grant` flera `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`gånger skickas ett fel: .

De flesta klienter behöver inte ändra beteende för att undvika det här felet.  Endast felkonfigurerade klienter (de utan tokencache eller de som redan uppvisar snabbloopar) påverkas av det här felet.  Klienter spåras per instans lokalt (via cookie) på följande faktorer:

* Användartips, om någon

* Scope eller resurs som begärs

* Klientorganisations-ID

* Omdirigerings-URI

* Svarstyp och -läge

Appar som gör flera begäranden (15+) på kort tid `invalid_grant` (5 minuter) får ett felmeddelande som förklarar att de loopar.  De token som begärs har tillräckligt långlivade livstider (minst 10 minuter, 60 minuter som standard), så upprepade begäranden under den här tidsperioden är onödiga.

Alla appar `invalid_grant` bör hantera genom att visa en interaktiv prompt, i stället för att tyst begära en token.  För att undvika det här felet bör klienter se till att de cacheställer de token de får på rätt sätt.


## <a name="october-2018"></a>Oktober 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Auktoriseringskoder kan inte längre återanvändas

**Gäller från**och med 15 november 2018

**Slutpunkter påverkas:** Både v1.0 och v2.0

**Protokoll som påverkas**: [Kodflöde](v2-oauth2-auth-code-flow.md)

Från och med den 15 november 2018 slutar Azure AD att acceptera tidigare använda autentiseringskoder för appar. Den här säkerhetsändringen hjälper till att anpassa Azure AD till OAuth-specifikationen och kommer att tillämpas på både v1- och v2-slutpunkterna.

Om appen återanvänder auktoriseringskoder för att hämta token för flera resurser rekommenderar vi att du använder koden för att hämta en uppdateringstoken och sedan använda den uppdateringstoken för att hämta ytterligare token för andra resurser. Auktoriseringskoder kan bara användas en gång, men uppdateringstoken kan användas flera gånger över flera resurser. Alla nya appar som försöker återanvända en autentiseringskod under OAuth-kodflödet får ett invalid_grant fel.

Mer information om uppdateringstoken finns i [Uppdatera åtkomsttoken](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  Om du använder ADAL eller MSAL hanteras detta åt dig av biblioteket - ersätt den andra instansen av "AcquireTokenByAuthorizationCodeAsync" med "AcquireTokenSilentAsync".

## <a name="may-2018"></a>Maj 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID-token kan inte användas för OBO-flödet

**Datum**: 1 maj 2018

**Slutpunkter påverkas:** Både v1.0 och v2.0

**Protokoll som påverkas**: Implicit flöde och [förflödet](v2-oauth2-on-behalf-of-flow.md)

Efter den 1 maj 2018 kan id_tokens inte användas som påstående i ett OBO-flöde för nya applikationer. Åtkomsttoken bör användas i stället för att skydda API:er, även mellan en klient och mellannivå i samma program. Appar som registrerats före den 1 maj 2018 fortsätter att fungera och kan utbyta id_tokens för en åtkomsttoken. Detta mönster anses dock inte vara en bästa praxis.

Om du vill gå runt den här ändringen kan du göra följande:

1. Skapa ett webb-API för ditt program, med ett eller flera scope. Denna uttryckliga ingång kommer att tillåta finare kornig kontroll och säkerhet.
1. I appens manifest, i [Azure-portalen](https://portal.azure.com) eller [appregistreringsportalen,](https://apps.dev.microsoft.com)se till att appen tillåts utfärda åtkomsttoken via det implicita flödet. Detta styrs `oauth2AllowImplicitFlow` genom nyckeln.
1. När klientprogrammet begär en id_token `response_type=id_token`via begär du även`response_type=token`en åtkomsttoken ( ) för webb-API:et som skapats ovan. När du använder v2.0-slutpunkten bör `scope` parametern alltså se ut ungefär som `api://GUID/SCOPE`. På v1.0-slutpunkten `resource` ska parametern vara appen URI för webb-API:et.
1. Skicka den här åtkomsttoken till mellannivån i stället för id_token.
