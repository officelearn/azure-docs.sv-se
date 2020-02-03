---
title: Autentisering och auktorisering
description: Lär dig mer om det inbyggda stödet för autentisering och auktorisering i Azure App Service och hur det kan hjälpa till att skydda din app mot obehörig åtkomst.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: efef578f5c62bef4ae33b98b568fd6d5c1389c4a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715120"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service

> [!NOTE]
> För tillfället stöds inte AAD v2 (inklusive MSAL) för Azure App Services och Azure Functions. Kom tillbaka efter uppdateringar.
>

Azure App Service tillhandahåller stöd för inbyggd autentisering och auktorisering, så att du kan logga in användare och få åtkomst till data genom att skriva minimalt eller ingen kod i din webbapp, RESTful-API och mobil Server del och även [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln beskriver hur App Service underlättar autentisering och auktorisering för din app.

Säker autentisering och auktorisering kräver djupgående förståelse av säkerhet, inklusive Federation, kryptering, [JSON Web token (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) -hantering, [beviljande typer](https://oauth.net/2/grant-types/)och så vidare. App Service tillhandahåller de här verktygen så att du kan lägga mer tid och energi på att tillhandahålla affärs värde till din kund.

> [!IMPORTANT]
> Du behöver inte använda App Service för authn/authn. Du kan använda de sammanslagna säkerhetsfunktionerna i ditt webb ramverk alternativt, eller så kan du skriva egna verktyg. Tänk dock på att [Chrome 80 gör större ändringar i dess implementering av SameSite för cookies](https://www.chromestatus.com/feature/5088147346030592) (lanserings datum runt den 2020 mars) och anpassad fjärrautentisering eller andra scenarier som förlitar sig på att en cookie-post på flera platser kan brytas när klientens webbläsare uppdateras. Lösningen är komplicerad eftersom den behöver stöd för olika SameSite-beteenden för olika webbläsare. 
>
> ASP.NET Core 2,1 och senare versioner som är värd för App Service har redan korrigerats för den här avbrytande ändringen och hanterar Chrome 80 och äldre webbläsare på lämpligt sätt. Dessutom distribueras samma korrigering för ASP.NET Framework-4.7.2 på App Service instanserna i januari 2020. Mer information, inklusive hur du vet om din app har tagit emot korrigeringen, finns [Azure App Service SameSite cookie Update](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

Information som är specifik för interna mobilappar finns i [användarautentisering och auktorisering för mobila appar med Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Hur det fungerar

Modulen för autentisering och auktorisering körs i samma sandbox som din program kod. När den är aktive rad passerar varje inkommande HTTP-begäran genom den innan den hanteras av din program kod.

![](media/app-service-authentication-overview/architecture.png)

Den här modulen hanterar flera saker för din app:

- Autentiserar användare med den angivna providern
- Verifierar, lagrar och uppdaterar tokens
- Hanterar den autentiserade sessionen
- Infogar identitets information i begärandehuvuden

Modulen körs separat från din program kod och konfigureras med hjälp av app-inställningar. Inga SDK: er, specifika språk eller ändringar av din program kod krävs. 

### <a name="user-claims"></a>Användaranspråk

För alla språk ramverk App Service gör att användarens anspråk är tillgängliga för din kod genom att mata in dem i begärandehuvuden. För ASP.NET 4,6-appar fyller App Service [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) med den autentiserade användarens anspråk, så du kan följa standard kod mönstret för .net, inklusive attributet `[Authorize]`. På samma sätt fyller App Service i PHP-appar `_SERVER['REMOTE_USER']`-variabeln. För Java-appar är anspråken [tillgängliga från Tomcat-servlet](containers/configure-language-java.md#authenticate-users-easy-auth).

För [Azure Functions](../azure-functions/functions-overview.md)är `ClaimsPrincipal.Current` inte vattentorkat för .NET-kod, men du kan fortfarande hitta användar anspråk i begärandehuvuden.

Mer information finns i [användar anspråk för åtkomst](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Token Store

App Service tillhandahåller en inbyggd token-lagringsplats, som är en lagrings plats med token som är associerade med användare av dina webbappar, API: er eller interna mobilappar. När du aktiverar autentisering med vilken provider som helst är det här token-arkivet omedelbart tillgängligt för din app. Om din program kod behöver komma åt data från dessa providers för användarens räkning, t. ex.: 

- publicera på den autentiserade användarens Facebook-tidslinje
- Läs användarens företags data från Azure Active Directory Graph API eller till och med Microsoft Graph

Du måste vanligt vis skriva kod för att samla in, lagra och uppdatera dessa token i ditt program. Med tokenarkivet hämtar du bara [tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) när du behöver dem och [instruerar App Service att uppdatera dem](app-service-authentication-how-to.md#refresh-identity-provider-tokens) när de blir ogiltiga. 

ID-token, åtkomsttoken och uppdaterade tokens cachelagrade för den autentiserade sessionen och de är bara tillgängliga för den associerade användaren.  

Om du inte behöver arbeta med tokens i din app kan du inaktivera token Store.

### <a name="logging-and-tracing"></a>Loggning och spårning

Om du [aktiverar program loggning](troubleshoot-diagnostic-logs.md)ser du autentiserings-och verifierings spårningar direkt i dina loggfiler. Om du ser ett autentiseringsfel som du inte förväntade dig, kan du enkelt hitta all information genom att titta i dina befintliga program loggar. Om du aktiverar [spårning av misslyckade begär Anden](troubleshoot-diagnostic-logs.md)kan du se exakt vilken roll som modulen autentisering och auktorisering kan ha spelat i en misslyckad begäran. Leta efter referenser till en modul med namnet `EasyAuthModule_32/64`i spårnings loggarna. 

## <a name="identity-providers"></a>Identitetsprovidrar

App Service använder [federerade identiteter](https://en.wikipedia.org/wiki/Federated_identity), där en tredjeparts identitets leverantör hanterar användar identiteter och autentiseringspaket åt dig. Fem identitets leverantörer är tillgängliga som standard: 

| Providern | Inloggnings slut punkt |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-konto](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

När du aktiverar autentisering och auktorisering med någon av dessa leverantörer, är dess inloggnings slut punkt tillgänglig för användarautentisering och för verifiering av autentiseringstoken från providern. Du kan ge dina användare ett valfritt antal inloggnings alternativ enkelt. Du kan också integrera en annan identitets leverantör eller [din egen anpassade identitets lösning][custom-auth].

## <a name="authentication-flow"></a>autentiseringspaket

Autentiserings flödet är detsamma för alla providers, men varierar beroende på om du vill logga in med providerns SDK:

- Utan Provider SDK: appen delegerar federerad inloggning till App Service. Detta är vanligt vis fallet med webb läsar appar som kan presentera providerns inloggnings sida för användaren. Server koden hanterar inloggnings processen, så den kallas även _Server-dirigerat flöde_ eller _Server flöde_. Det här fallet gäller för webb läsar appar. Den gäller även för inbyggda appar som signerar användare i med hjälp av Mobile Apps klient-SDK eftersom SDK öppnar en webbvy för att logga in användare med App Service autentisering. 
- Med Provider SDK: appen loggar användare i providern manuellt och skickar sedan autentiseringstoken till App Service för verifiering. Detta är vanligt vis fallet med webbläsarbaserade appar, som inte kan visa leverantörens inloggnings sida för användaren. Program koden hanterar inloggnings processen, så den kallas även _klient-dirigerat flöde_ eller _klient flöde_. Det här fallet gäller för REST-API: er, [Azure Functions](../azure-functions/functions-overview.md)och Java Script Browser-klienter, samt webb läsar appar som behöver mer flexibilitet i inloggnings processen. Den gäller även för interna mobilappar som signerar användare med hjälp av providerns SDK.

> [!NOTE]
> Anrop från en betrodd webbapp i App Service anropar en annan REST API i App Service eller [Azure Functions](../azure-functions/functions-overview.md) kan autentiseras med hjälp av ett Server-dirigerat flöde. Mer information finns i [Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md).
>

I tabellen nedan visas stegen i Authentication Flow.

| Steg | Utan SDK för Provider | Med SDK för Provider |
| - | - | - |
| 1. Logga in användare | Omdirigerar klienten till `/.auth/login/<provider>`. | Klient koden signerar användaren direkt med providerns SDK och tar emot en autentiseringstoken. Mer information finns i leverantörens dokumentation. |
| 2. efter autentisering | Providern omdirigerar klienten till `/.auth/login/<provider>/callback`. | Klient koden [skickar token från providern](app-service-authentication-how-to.md#validate-tokens-from-providers) till `/.auth/login/<provider>` för verifiering. |
| 3. upprätta en autentiserad session | App Service lägger till autentiserad cookie i svaret. | App Service returnerar dess egen autentiseringstoken till klient koden. |
| 4. betjäna autentiserat innehåll | Klienten inkluderar autentiserings-cookie i efterföljande begär Anden (hanteras automatiskt av webbläsare). | Klient koden presenterar autentiseringstoken i `X-ZUMO-AUTH`s huvud (hanteras automatiskt av Mobile Apps klient-SDK: er). |

För klient webbläsare kan App Service automatiskt dirigera alla oautentiserade användare till `/.auth/login/<provider>`. Du kan också visa användare med en eller flera `/.auth/login/<provider>` Länkar för att logga in i din app med valfri leverantör.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Beteende för auktorisering

I [Azure Portal](https://portal.azure.com)kan du konfigurera App Service auktorisering med ett antal beteenden när en inkommande begäran inte autentiseras.

![](media/app-service-authentication-overview/authorization-flow.png)

Följande rubriker beskriver alternativen.

### <a name="allow-anonymous-requests-no-action"></a>Tillåt anonyma begär Anden (ingen åtgärd)

Med det här alternativet överlåts auktoriseringen av oautentiserad trafik till program koden. För autentiserade begär Anden skickar App Service även information om autentiseringen i HTTP-huvudena. 

Det här alternativet ger större flexibilitet vid hantering av anonyma begär Anden. Du kan till exempel [presentera flera inloggnings leverantörer](app-service-authentication-how-to.md#use-multiple-sign-in-providers) för dina användare. Du måste dock skriva kod. 

### <a name="allow-only-authenticated-requests"></a>Tillåt endast autentiserade begär Anden

Alternativet är att **Logga in med \<provider >** . App Service dirigerar om alla anonyma begär anden till `/.auth/login/<provider>` för den provider som du väljer. Om den anonyma begäran kommer från en ursprunglig mobilapp är det returnerade svaret en `HTTP 401 Unauthorized`.

Med det här alternativet behöver du inte skriva någon autentiseringsmetod i din app. Bättre auktorisering, till exempel rollbaserad auktorisering, kan hanteras genom att inspektera användarens anspråk (se [användar anspråk för åtkomst](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som vill ha en offentligt tillgänglig start sida, som i många program med en enda sida.

## <a name="more-resources"></a>Fler resurser

[Självstudie: autentisera och auktorisera användare från slut punkt till slut punkt i Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Självstudie: autentisera och auktorisera användare från slut punkt till slut punkt i Azure App Service för Linux](containers/tutorial-auth-aad.md)  
[Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md)

Providerspecifika instruktions guider:

* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning][AAD]
* [Så här konfigurerar du din app för att använda Facebook-inloggning][Facebook]
* [Så här konfigurerar du din app för att använda Google-inloggning][Google]
* [Så här konfigurerar du din app för att använda Microsoft-kontoinloggning][MSA]
* [Så här konfigurerar du din app för att använda Twitter-inloggning][Twitter]
* [Gör så här: använda Anpassad autentisering för ditt program][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
