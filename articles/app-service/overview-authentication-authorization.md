---
title: Autentisering och auktorisering – Azure App Service | Microsoft Docs
description: Konceptuell referens och översikt över autentisering / auktorisering funktion för Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/24/2018
ms.author: mahender,cephalin
ms.custom: seodec18
ms.openlocfilehash: b66b293e5d6e422c994e2b7cef13fd4100144cbf
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297012"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service

Azure App Service tillhandahåller inbyggd autentisering och autentisering stöder, så att du kan logga in användare och få åtkomst till data genom att skriva minimal eller ingen kod i din webbapp, RESTful-API och mobil serverdel, och även [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln beskrivs hur App Service hjälper dig att förenkla autentisering och auktorisering för din app. 

Säker autentisering och auktorisering kräver djup förståelse för säkerhet, inklusive federation, kryptering, [JSON web token (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) hantering, [beviljandetyper](https://oauth.net/2/grant-types/)och så vidare. App Service tillhandahåller dessa verktyg så att du kan lägga mer tid och energi på att tillhandahålla affärsvärde till kunden.

> [!NOTE]
> Du är inte krävs för att använda App Service för autentisering och auktorisering. Många webbramverk paketeras med säkerhetsfunktioner och du kan använda dem om du vill. Om du behöver mer flexibilitet än App Service tillhandahåller kan kan du också skriva egna verktyg.  
>

Information som är specifika för inbyggda mobilappar finns i [användarautentisering och auktorisering för mobila appar med Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Hur det fungerar

Autentisering och auktorisering modulen körs i samma sandlåda som din programkod. När den är aktiverad skickas varje inkommande HTTP-begäran via den innan du hanteras av din programkod.

![](media/app-service-authentication-overview/architecture.png)

Den här modulen hanterar flera saker för din app:

- Autentiserar användare med den angivna providern
- Verifierar, lagrar och uppdaterar token
- Hanterar autentiserad session
- Lägger in identitetsinformation i begärandehuvuden

Modulen körs avskilt från din programkod och konfigureras med hjälp av appinställningarna. Det krävs inga SDK: er, vissa språk eller ändringar i din programkod. 

### <a name="user-claims"></a>Användaranspråk

För alla språkramverken tillgängliggör App Service användarens anspråk i koden genom att placera dem i de begärda rubrikerna. För ASP.NET 4.6 appar fyller på App Service [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) med den autentiserade användarens anspråk, så att du kan följa Standardmönstret för .NET-kod, inklusive den `[Authorize]` attribut. På samma sätt för PHP-appar, App Service fyller den `_SERVER['REMOTE_USER']` variabeln.

För [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` inte är hydrerat för .NET-kod, men du kan fortfarande hitta användaranspråk i huvudena för begäran.

Mer information finns i [komma åt användaranspråk](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Tokenarkiv

App Service tillhandahåller en inbyggd token store, som är en lagringsplats i de token som är kopplade till användare av dina webbappar, API: er och inbyggda mobilappar. När du aktiverar autentisering med valfri provider som är den här token store omedelbart tillgängliga för din app. Om din programkod behöver komma åt data från dessa providers för användarens räkning, till exempel: 

- Publicera till den autentiserade användarens Facebook-tidslinje
- läsa användarens företagsdata från Azure Active Directory Graph API eller även Microsoft Graph

Vanligtvis måste du skriva kod för att samla in, lagra och uppdatera dessa token i ditt program. Med token store, som du precis [hämta token](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) när du behöver dem och [berätta för App Service för att uppdatera dem](app-service-authentication-how-to.md#refresh-identity-provider-tokens) när de blir ogiltig. 

Id-token, åtkomsttoken och uppdateringstoken cachelagras för den autentiserade sessionen och de är endast kan nås av den associerade användaren.  

Om du inte behöver arbeta med token i din app kan inaktivera du av tokenarkiv.

### <a name="logging-and-tracing"></a>Loggning och spårning

Om du [aktivera programloggning](troubleshoot-diagnostic-logs.md), autentisering och auktorisering spårningar visas direkt i loggfilerna. Om du ser ett autentiseringsfel som du inte förväntade dig kan hittar du smidigt all information genom att titta i din befintliga programloggar. Om du aktiverar [spårning av misslyckade begäranden](troubleshoot-diagnostic-logs.md), kan du se exakt vilken roll autentisering och auktorisering modulen kanske har spelat i en misslyckad begäran. Sök efter referenser till en modul med namnet i spårningsloggar, `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Identitetsprovidrar

App Service använder [federerad identitet](https://en.wikipedia.org/wiki/Federated_identity), där en tredjeparts identitetsprovider hanterar användaridentiteter och autentiseringsflödet åt dig. Det finns fem identitetsleverantörer som standard: 

| Leverantör | Logga in slutpunkt |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-konto](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

När du aktiverar autentisering och auktorisering med någon av dessa providers kan är logga in som slutpunkt tillgänglig för användarautentisering och för verifiering av autentiseringstoken från providern. Du kan ange dina användare med valfritt antal dessa inloggningsalternativ enkelt. Du kan också integrera någon annan identitetsprovider eller [dina egna anpassade identitetslösning][custom-auth].

## <a name="authentication-flow"></a>Autentiseringsflödet

Autentiseringsflödet är densamma för alla leverantörer, men skiljer sig beroende på om du vill logga in med leverantörens SDK:

- Utan provider-SDK: Programmet delegerar sammansluten inloggning till App Service. Detta är vanligtvis fallet med webbläsarbaserade appar som kan ge leverantörens inloggningssidan för användaren. Serverkoden hanterar inloggningsprocessen, det kallas även _server-riktade flow_ eller _server flow_. Det här fallet gäller webbläsarbaserade appar. Det gäller även för interna appar som loggar in användare med SDK för Mobile Apps-klient eftersom SDK: N öppnar en webbvy för att du loggar in användare med App Service-autentisering. 
- Med SDK-provider: Programmet loggar användarna i providern manuellt och skickar sedan autentiseringstoken till App Service för verifiering. Detta är vanligtvis fallet med webbläsare utan appar som det går inte att presentera leverantörens på inloggningssidan för användaren. Programkoden hanterar inloggningsprocessen, det kallas även _klienten dirigeras flödet_ eller _klientflödet_. Det här fallet gäller för REST API: er, [Azure Functions](../azure-functions/functions-overview.md), och JavaScript-webbläsarklienter, samt webbläsarbaserade appar som behöver mer flexibilitet i processen för inloggning. Det gäller även för inbyggda mobilappar som loggar in användare i med hjälp av leverantörens SDK.

> [!NOTE]
> Anrop från en betrodd browser-appen i App Service anropar en annan REST-API i App Service eller [Azure Functions](../azure-functions/functions-overview.md) kan autentiseras med hjälp av server-riktade flödet. Mer information finns i [anpassa autentisering och auktorisering i Apptjänst](app-service-authentication-how-to.md).
>

Tabellen nedan visar stegen för autentiseringsflödet.

| Steg | Utan provider-SDK | Med SDK-provider |
| - | - | - |
| 1. Registrera användare i | Omdirigerar klienten `/.auth/login/<provider>`. | Klientkoden loggar användaren in direkt med leverantörens SDK och får en autentiseringstoken. Information finns i leverantörens dokumentation. |
| 2. Efterautentisering | Providern omdirigerar klienten `/.auth/login/<provider>/callback`. | Klientkoden [inlägg token från providern](app-service-authentication-how-to.md#validate-tokens-from-providers) till `/.auth/login/<provider>` för verifiering. |
| 3. Upprätta autentiserad session | App Service lägger till autentiserade cookie svar. | App Service returnerar egna autentiseringstoken till klientkod. |
| 4. Hantera autentiserade innehåll | Klienten inkluderar autentiseringscookie i efterföljande förfrågningar (hanteras automatiskt av webbläsare). | Klientkoden presenterar autentiseringstoken i `X-ZUMO-AUTH` rubrik (hanteras automatiskt av Mobile Apps-klient SDK: er). |

För klientens webbläsare, App Service kan dirigera alla oautentiserade användare till `/.auth/login/<provider>`. Du kan också visa användare med en eller flera `/.auth/login/<provider>` länkar för att logga in på din app med sina leverantörer föredrar.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Beteende för auktorisering

I den [Azure-portalen](https://portal.azure.com), du kan konfigurera App Service-auktorisering med ett antal beteenden.

![](media/app-service-authentication-overview/authorization-flow.png)

Följande rubriker beskrivs alternativ.

### <a name="allow-all-requests-default"></a>Tillåt alla förfrågningar (standard)

Autentisering och auktorisering hanteras inte av App Service (inaktiverad). 

Välj det här alternativet om du inte behöver autentisering och auktorisering, eller om du vill skriva egen kod för autentisering och auktorisering.

### <a name="allow-only-authenticated-requests"></a>Tillåt endast autentiserade begäranden

Alternativet är **logga in med \<providern >**. App Service omdirigerar alla anonyma förfrågningar till `/.auth/login/<provider>` för providern som du väljer. Om anonym begäran kommer från en intern mobilapp, returnerade svaret är ett `HTTP 401 Unauthorized`.

Med det här alternativet behöver du inte skriva någon Autentiseringskod i din app. Mer detaljerad auktorisering, till exempel rollspecifika auktorisering, kan hanteras genom att kontrollera användarens anspråk (se [komma åt användaranspråk](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Tillåt alla begäranden, men Validera autentiserade begäranden

Alternativet är **Tillåt anonyma förfrågningar**. Det här alternativet aktiverar autentisering och auktorisering i App Service, men skjuter upp auktoriseringsbeslut programkoden. För autentiserade begäranden skickar också App Service med autentiseringsinformation i HTTP-huvuden. 

Det här alternativet ger bättre flexibilitet vid hantering av anonyma begäranden. Exempelvis kan du [presentera flera inloggning providers](app-service-authentication-how-to.md#use-multiple-sign-in-providers) till dina användare. Dock måste du skriva kod. 

## <a name="more-resources"></a>Fler resurser

[Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service för Linux](containers/tutorial-auth-aad.md)  
[Anpassa autentisering och auktorisering i App Service](app-service-authentication-how-to.md)

Provider-specifik instruktionsguider:

* [Så här konfigurerar du din app för att använda Azure Active Directory-inloggning][AAD]
* [Så här konfigurerar du din app för att använda Facebook-inloggning][Facebook]
* [Så här konfigurerar du din app för att använda Google-inloggning][Google]
* [Så här konfigurerar du din app om du vill använda Microsoft Account login][MSA]
* [Så här konfigurerar du din app för att använda Twitter-inloggning][Twitter]
* [Anvisningar: Använd anpassad autentisering för ditt program][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
