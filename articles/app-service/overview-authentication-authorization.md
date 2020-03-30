---
title: Autentisering och auktorisering
description: Ta reda på mer om den inbyggda autentiserings- och auktoriseringssupporten i Azure App Service och hur den kan skydda din app mot obehörig åtkomst.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 825d113bbe081ba6fb85da19ff6449824db92d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475399"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service

> [!NOTE]
> För närvarande stöds inte AAD V2 (inklusive MSAL) för Azure App Services och Azure Functions. Kom tillbaka för uppdateringar.
>

Azure App Service tillhandahåller inbyggt stöd för autentisering och auktorisering, så att du kan logga in användare och komma åt data genom att skriva minimal eller ingen kod i din webbapp, RESTful API och mobila serverdel, och även [Azure Functions](../azure-functions/functions-overview.md). I den här artikeln beskrivs hur App Service förenklar autentisering och auktorisering för din app.

Säker autentisering och auktorisering kräver djup förståelse för säkerhet, inklusive federation, kryptering, [JSON-webbtoken (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) hantering, [bidragstyper](https://oauth.net/2/grant-types/)och så vidare. App Service tillhandahåller dessa verktyg så att du kan lägga mer tid och energi på att ge affärsnytta till din kund.

> [!IMPORTANT]
> Du behöver inte använda App Service för AuthN/AuthO. Du kan använda de medföljande säkerhetsfunktionerna i webbramverket eller skriva egna verktyg. Tänk dock på att [Chrome 80 gör bryta ändringar i implementeringen av SameSite för cookies](https://www.chromestatus.com/feature/5088147346030592) (utgivningsdatum runt mars 2020) och anpassad fjärrautentisering eller andra scenarier som är beroende av flerwebbplatser cookie-post kan brytas när klientwebbwebbläsare i Chrome uppdateras. Lösningen är komplex eftersom den behöver ha stöd för olika SameSite-beteenden för olika webbläsare. 
>
> Den ASP.NET Core 2.1 och högre versioner som är värd apptjänsten är redan korrigerade för denna bryta förändring och hantera Chrome 80 och äldre webbläsare på lämpligt sätt. Dessutom distribueras samma korrigeringsfil för ASP.NET Framework 4.7.2 i App Service-instanserna under hela januari 2020. Mer information, inklusive hur du vet om din app har tagit emot korrigeringsfilen finns i [Azure App Service SameSite cookie update](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/).
>

Information som är specifik för inbyggda mobilappar finns i [Användarautentisering och auktorisering för mobilappar med Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Hur det fungerar

Autentiserings- och auktoriseringsmodulen körs i samma sandlåda som programkoden. När den är aktiverad passerar varje inkommande HTTP-begäran den innan den hanteras av programkoden.

![](media/app-service-authentication-overview/architecture.png)

Den här modulen hanterar flera saker för din app:

- Autentiserar användare med den angivna providern
- Validerar, lagrar och uppdaterar token
- Hanterar den autentiserade sessionen
- Ingjuter identitetsinformation i begäranden

Modulen körs separat från programkoden och konfigureras med hjälp av appinställningar. Inga SDK:er, specifika språk eller ändringar i programkoden krävs. 

### <a name="user-claims"></a>Användaranspråk

För alla språkramverk gör App Service användarens anspråk tillgängliga för din kod genom att injicera dem i begäranhetsrubrikerna. För ASP.NET 4.6-appar fyller App-tjänsten [i ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) med den autentiserade användarens anspråk, så att `[Authorize]` du kan följa standardkodmönstret för .NET, inklusive attributet. På samma sätt fyller App Service `_SERVER['REMOTE_USER']` variabeln för PHP-appar. För Java-appar är anspråken [tillgängliga från Tomcat servlet](containers/configure-language-java.md#authenticate-users-easy-auth).

För [Azure](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` Functions är inte hydratiserad för .NET-kod, men du kan fortfarande hitta användarens anspråk i begäranhetsrubrikerna.

Mer information finns i [Åtkomst till användaranspråk](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Tokenarkiv

AppTjänsten tillhandahåller en inbyggd tokenbutik, som är en databas med token som är associerade med användarna av dina webbappar, API:er eller inbyggda mobilappar. När du aktiverar autentisering med en provider är den här tokenbutiken omedelbart tillgänglig för din app. Om din programkod behöver komma åt data från dessa leverantörer för användarens räkning, till exempel: 

- publicera den autentiserade användarens Facebook-tidslinje
- läsa användarens företagsdata med Hjälp av Microsoft Graph API

Du måste vanligtvis skriva kod för att samla in, lagra och uppdatera dessa token i ditt program. Med token store hämtar du bara [token](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) när du behöver dem och [be App Service att uppdatera dem](app-service-authentication-how-to.md#refresh-identity-provider-tokens) när de blir ogiltiga. 

ID-token, åtkomsttoken och uppdatera token som cachelagrats för den autentiserade sessionen och de är endast tillgängliga för den associerade användaren.  

Om du inte behöver arbeta med token i appen kan du inaktivera tokenarkivet.

### <a name="logging-and-tracing"></a>Logga och spåra

Om du [aktiverar programloggning](troubleshoot-diagnostic-logs.md)visas autentiserings- och auktoriseringsspårningar direkt i loggfilerna. Om du ser ett autentiseringsfel som du inte förväntade dig kan du enkelt hitta alla detaljer genom att titta i dina befintliga programloggar. Om du aktiverar [spårning av misslyckade begäranden](troubleshoot-diagnostic-logs.md)kan du se exakt vilken roll autentiserings- och auktoriseringsmodulen kan ha spelat i en misslyckad begäran. Leta efter referenser till en modul med `EasyAuthModule_32/64`namnet . 

## <a name="identity-providers"></a>Identitetsprovidrar

App Service använder [federerad identitet](https://en.wikipedia.org/wiki/Federated_identity), där en tredjepartsidentitetsprovider hanterar användaridentiteter och autentiseringsflöde åt dig. Fem identitetsleverantörer är tillgängliga som standard: 

| Leverantör | Slutpunkt för inloggning |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-konto](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

När du aktiverar autentisering och auktorisering med en av dessa leverantörer är dess inloggningsslutpunkt tillgänglig för användarautentisering och för validering av autentiseringstoken från providern. Du kan enkelt ge användarna valfritt antal av dessa inloggningsalternativ. Du kan också integrera en annan identitetsprovider eller [din egen anpassade identitetslösning][custom-auth].

## <a name="authentication-flow"></a>Autentiseringsflöde

Autentiseringsflödet är detsamma för alla leverantörer, men skiljer sig beroende på om du vill logga in med leverantörens SDK:

- Utan provider SDK: Programmet delegerar federerade inloggning till App Service. Detta är vanligtvis fallet med webbläsarappar, som kan presentera leverantörens inloggningssida för användaren. Serverkoden hanterar inloggningsprocessen, så den kallas även _serverriktad flöde_ eller _serverflöde_. Det här fallet gäller webbläsarappar. Det gäller även för inbyggda appar som loggar in användare med mobile apps-klienten SDK eftersom SDK öppnar en webbvy för att logga in användare med App Service-autentisering. 
- Med provider SDK: Programmet signerar användare till providern manuellt och skickar sedan autentiseringstoken till App Service för validering. Detta är vanligtvis fallet med webbläsarlösa appar, som inte kan presentera leverantörens inloggningssida för användaren. Programkoden hanterar inloggningsprocessen, så den kallas även _klientstyrd flöde_ eller _klientflöde_. Det här fallet gäller REST-API:er, [Azure Functions](../azure-functions/functions-overview.md)och JavaScript-webbläsarklienter samt webbläsarappar som behöver mer flexibilitet i inloggningsprocessen. Det gäller även för inbyggda mobilappar som loggar in användare med hjälp av leverantörens SDK.

> [!NOTE]
> Samtal från en betrodd webbläsarapp i App Service anropar ett annat REST API i App Service eller [Azure Functions](../azure-functions/functions-overview.md) kan autentiseras med hjälp av det serverstyrda flödet. Mer information finns [i Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md).
>

Tabellen nedan visar stegen i autentiseringsflödet.

| Steg | Utan leverantör SDK | Med leverantören SDK |
| - | - | - |
| 1. Logga in användaren | Omdirigerar klienten till `/.auth/login/<provider>`. | Klientkoden signerar användaren direkt med leverantörens SDK och tar emot en autentiseringstoken. Mer information finns i leverantörens dokumentation. |
| 2. Efter autentisering | Provider omdirigerar `/.auth/login/<provider>/callback`klienten till . | Klientkod bokför token `/.auth/login/<provider>` från [provider](app-service-authentication-how-to.md#validate-tokens-from-providers) till för validering. |
| 3. Upprätta autentiserade session | App Service lägger till autentiserade cookies för att svara. | App Service returnerar sin egen autentiseringstoken till klientkod. |
| 4. Servera autentiserat innehåll | Klienten inkluderar autentiseringscookie i efterföljande begäranden (hanteras automatiskt av webbläsaren). | Klientkod visar autentiseringstoken i `X-ZUMO-AUTH` huvudet (hanteras automatiskt av Mobile Apps-klient-SDK:er). |

För klientwebbläsare kan App Service automatiskt dirigera alla oautentiserade användare till `/.auth/login/<provider>`. Du kan också presentera en `/.auth/login/<provider>` eller flera länkar för att logga in på appen med hjälp av valfri leverantör.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Auktoriseringsbeteende

I [Azure-portalen](https://portal.azure.com)kan du konfigurera App Service-auktorisering med ett antal beteenden när inkommande begäran inte autentiseras.

![](media/app-service-authentication-overview/authorization-flow.png)

Följande rubriker beskriver alternativen.

### <a name="allow-anonymous-requests-no-action"></a>Tillåt anonyma begäranden (ingen åtgärd)

Det här alternativet skjuter upp auktorisering av oautentiserade trafik till din programkod. För autentiserade begäranden skickar App Service även autentiseringsinformation i HTTP-huvudena. 

Det här alternativet ger större flexibilitet vid hantering av anonyma begäranden. Du kan till exempel [presentera flera inloggningsleverantörer](app-service-authentication-how-to.md#use-multiple-sign-in-providers) för användarna. Du måste dock skriva kod. 

### <a name="allow-only-authenticated-requests"></a>Tillåt endast autentiserade begäranden

Alternativet är **Logga \<in med leverantören>**. App Service omdirigerar alla `/.auth/login/<provider>` anonyma förfrågningar till för den leverantör du väljer. Om den anonyma begäran kommer från en inbyggd `HTTP 401 Unauthorized`mobilapp är det returnerade svaret ett .

Med det här alternativet behöver du inte skriva någon autentiseringskod i appen. Finare auktorisering, till exempel rollspecifik auktorisering, kan hanteras genom att användarens anspråk inspekteras (se [Åtkomstanvändaranspråk](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Att begränsa åtkomsten på det här sättet gäller alla samtal till din app, vilket kanske inte är önskvärt för appar som vill ha en allmänt tillgänglig startsida, som i många ensidiga program.

## <a name="more-resources"></a>Fler resurser

[Självstudiekurs: Autentisera och auktorisera användare från på ett i Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Självstudiekurs: Autentisera och auktorisera användare från på ett i Azure App Service för Linux](containers/tutorial-auth-aad.md)  
[Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md)

Providerspecifika how-to-guider:

* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning][AAD]
* [Så här konfigurerar du din app för att använda Facebook-inloggning][Facebook]
* [Så här konfigurerar du din app för att använda Google-inloggning][Google]
* [Så här konfigurerar du din app för att använda Microsoft-kontoinloggning][MSA]
* [Så här konfigurerar du din app för att använda Twitter-inloggning][Twitter]
* [Så här: Använd anpassad autentisering för ditt program][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
