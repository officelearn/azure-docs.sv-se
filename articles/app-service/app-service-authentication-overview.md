---
title: Autentisering och auktorisering i Azure App Service | Microsoft Docs
description: Konceptuell referens- och översikt över autentisering / auktorisering funktion för Azure App Service
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: c180dcf5d769245f3fa2485ccee2cbc18ecf5f67
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763500"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service

Azure Apptjänst innehåller inbyggda autentisering och auktorisering stöder, så att du kan logga in användare och få åtkomst till data genom att skriva minimal eller någon kod i ditt webbprogram, API och mobila serverdel och även [Azure Functions](../azure-functions/functions-overview.md). Den här artikeln beskriver hur Apptjänst hjälper till att förenkla autentisering och auktorisering för din app. 

Säker autentisering och auktorisering kräver djupgående förståelse för säkerhet, inklusive federation, kryptering, [JSON web token (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) hantering, [bevilja typer](https://oauth.net/2/grant-types/)och så vidare. App Service tillhandahåller dessa verktyg så att du kan spendera mer tid och energi på att tillhandahålla business-värde till kunden.

> [!NOTE]
> Du inte behöver använda Apptjänst för autentisering och auktorisering. Ramverk för många webbserver är paketerade med säkerhetsfunktioner och du kan använda dem om du vill. Du kan också skriva egna verktyg om du behöver mer flexibilitet än App Service tillhandahåller.  
>

Information som är specifika för inbyggda mobila appar finns i [användarautentisering och auktorisering för mobila appar med Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Hur det fungerar

Autentisering och auktorisering modulen körs i samma sandbox som din programkod. När den är aktiverad passerar varje inkommande HTTP-begäran innan hanteras av din programkod.

![](media/app-service-authentication-overview/architecture.png)

Den här modulen hanterar flera saker för din app:

- Autentiserar användare med den angivna providern
- Verifierar, lagrar och uppdaterar token
- Hanterar autentiserad session
- Lägger in identitetsinformation i huvuden för begäran

Modulen körs separat från din programkod och konfigureras med hjälp av app-inställningar. Det krävs inga SDK: er, specifika språk eller ändringar av programkoden. 

### <a name="user-claims"></a>Användaranspråk

För alla språkramverken tillgängliggör Apptjänst användarens anspråk till din kod genom att placera dem i huvuden för begäran. För ASP.NET 4.6 appar Apptjänst fylls [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) med den autentiserade användarens anspråk, så att du kan följa standardmönster för .NET-kod, inklusive den `[Authorize]` attribut. På samma sätt för PHP-appar Apptjänst fylls den `_SERVER['REMOTE_USER']` variabeln.

För [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` inte ur för .NET-kod, men du fortfarande kan hitta användaranspråk i huvuden för begäran.

Mer information finns i [åtkomst till användaranspråk](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Tokenarkiv

Apptjänst innehåller en inbyggd tokenarkiv som är en lagringsplats för token som är kopplade till användare av ditt webbprogram, API: er eller inbyggda mobila appar. När du aktiverar autentisering med valfri provider som är den här token store omedelbart tillgängliga för din app. Om din programkod som behöver åtkomst till data från dessa providers för användarens räkning som: 

- efter att den autentiserade användaren Facebook tidslinjen
- läsa användarens företagets data från Azure Active Directory Graph API och även Microsoft Graph

Id-token, åtkomst-token och uppdateringstoken cachelagras för autentiserad session och de är endast kan nås av den associerade användaren.  

Vanligtvis måste du skriva kod för att samla in, lagra och uppdatera dessa token i ditt program. Med arkivet token som du precis [hämta token](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) när du behöver och [berätta Apptjänst att uppdatera dem](app-service-authentication-how-to.md#refresh-access-tokens) när de blir ogiltig. 

Om du inte behöver arbeta med token i din app kan du inaktivera av tokenarkiv.

### <a name="logging-and-tracing"></a>Loggning och spårning

Om du [aktivera programloggning](web-sites-enable-diagnostic-log.md), autentisering och auktorisering spårningar visas direkt i loggfilerna. Om du ser ett autentiseringsfel som du inte förväntar dig hittar du lätt allt genom att titta i din befintliga programloggar. Om du aktiverar [spårning av misslyckade begäranden](web-sites-enable-diagnostic-log.md), du kan se exakt vilken roll autentisering och auktorisering modulen kanske har spelat i en misslyckad begäran. Sök efter referenser till en modul med namnet i spårningsloggar, `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Identitetsprovidrar

Apptjänst använder [federerad identitet](https://en.wikipedia.org/wiki/Federated_identity), där en tredjeparts identitetsprovider hanterar användaridentiteter och autentiseringsflödet för dig. Det finns fem identitetsleverantörer som standard: 

| Leverantör | Logga in slutpunkt |
| - | - |
| [Azure Active Directory](../active-directory/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-konto](../active-directory/develop/active-directory-appmodel-v2-overview.md) | `/.auth/login/microsoft` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/docs/basics/authentication) | `/.auth/login/twitter` |

När du aktiverar autentisering och auktorisering med något av dessa providers är inloggning slutpunkten tillgänglig för autentisering och för verifiering av autentiseringstoken från leverantören. Du kan ge användarna med valfritt antal dessa inloggningsalternativ enkel. Du kan också integrera en annan identitetsleverantör eller [egna anpassade identitetslösning][custom-auth].

## <a name="authentication-flow"></a>Autentiseringsflöde

Autentiseringsflödet är samma för alla leverantörer, men skiljer sig åt beroende på om du vill logga in med leverantörens SDK:

- Utan providern SDK: programmet delegerar federerad inloggning till App Service. Detta är vanligtvis fallet med webbläsarbaserade appar som kan medföra leverantörens inloggningssidan för användaren. Serverkoden hanterar processen inloggning så kallas även _server-riktade flödet_ eller _server flödet_. Det här fallet gäller för web apps. Det gäller även för interna appar som loggar in användare med Mobile Apps klient-SDK eftersom SDK öppnar en webbvy för att du loggar in användare med App Service-autentisering. 
- Med SDK-provider: programmet loggar användaren manuellt och skickar sedan autentiseringstoken till App Service för verifiering. Detta är vanligtvis fallet med webbläsare mindre appar som inte finns leverantörens inloggningssidan för användaren. Programkoden hanterar processen inloggning så kallas även _klienten dirigeras flödet_ eller _flödet_. Det här fallet gäller för REST-API: er, [Azure Functions](../azure-functions/functions-overview.md), och JavaScript-webbläsarklienter, samt webbprogram som behöver mer flexibilitet i processen för inloggning. Det gäller även för inbyggda mobila appar som loggar in användare med leverantörens SDK.

> [!NOTE]
> Anrop från en betrodd browser-appen i App Service anropar en annan REST-API i App Service eller [Azure Functions](../azure-functions/functions-overview.md) kan autentiseras med hjälp av server-riktade flödet. Mer information finns i [autentisera användare med Azure App Service]().
>

Tabellen nedan visar stegen för autentiseringsflödet.

| Steg | Utan providern SDK | Med SDK-provider |
| - | - | - |
| 1. Logga användare i | Dirigerar klienten att `/.auth/login/<provider>`. | Klientkod signerar användaren direkt med leverantörens SDK och tar emot en token för autentisering. Information finns i leverantörens dokumentation. |
| 2. Efterautentisering | Providern dirigerar klienten att `/.auth/login/<provider>/callback`. | Klientkod anslår token från providern till `/.auth/login/<provider>` för verifiering. |
| 3. Upprätta en autentiserad session | Apptjänst lägger till autentiserade cookie svar. | Apptjänst returnerar egna autentiseringstoken till klientkod. |
| 4. Hantera autentiserade innehåll | Klienten inkluderar autentiseringscookien i efterföljande förfrågningar (hanteras automatiskt av webbläsare). | Klientkod är autentiseringstoken i `X-ZUMO-AUTH` huvud (hanteras automatiskt av Mobile Apps klienten SDK: er). |

För klientens webbläsare Apptjänst automatiskt dirigera alla icke autentiserade användare till `/.auth/login/<provider>`. Du kan också visa användare med en eller flera `/.auth/login/<provider>` länkar för att logga in i appen med hjälp av providerns val.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Auktorisering beteende

I den [Azure-portalen](https://portal.azure.com), kan du konfigurera App Service-auktorisering med ett antal beteenden.

![](media/app-service-authentication-overview/authorization-flow.png)

Följande rubriker beskrivs alternativen.

### <a name="allow-all-requests-default"></a>Tillåt alla förfrågningar (standard)

Autentisering och auktorisering hanteras inte av App Service (inaktiverad). 

Välj det här alternativet om du inte behöver autentisering och auktorisering, eller om du vill skriva egen kod för autentisering och auktorisering.

### <a name="allow-only-authenticated-requests"></a>Tillåt endast autentiserade begäranden

Alternativet är **logga in med \<providern >**. Apptjänst dirigerar alla anonyma begäranden till `/.auth/login/<provider>` för providern som du väljer. Om anonym begäran kommer från en intern mobilapp, returnerade svaret är ett `HTTP 401 Unauthorized`.

Med det här alternativet behöver du inte skriva någon Autentiseringskod i appen. Ökad auktorisering, till exempel rollspecifika auktorisering, kan hanteras genom att kontrollera användarens anspråk (se [åtkomst till användaranspråk](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Tillåt alla begäranden, men Validera autentiserade begäranden

Alternativet är **Tillåt anonyma begäranden**. Det här alternativet aktiverar autentisering och auktorisering i Apptjänst, men skiljer sig auktoriseringsbeslut av programkoden. För autentiserade begäranden skickar också Apptjänst längs autentiseringsinformation i HTTP-rubriker. 

Det här alternativet ger bättre flexibilitet vid anonyma begäranden. Till exempel gör det möjligt att [presentera flera inloggningsalternativ](app-service-authentication-how-to.md#configure-multiple-sign-in-options) till användarna. Du måste dock skriva kod. 

## <a name="more-resources"></a>Fler resurser

[Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Självstudier: Autentisera och auktorisera användare slutpunkt till slutpunkt i Azure App Service för Linux](containers/tutorial-auth-aad.md)  
[Anpassa autentisering och auktorisering i Apptjänst](app-service-authentication-how-to.md)

Provider-specifik guider:

* [Så här konfigurerar du appen för att använda Azure Active Directory-inloggning][AAD]
* [Så här konfigurerar du appen för att använda Facebook-inloggning][Facebook]
* [Så här konfigurerar du appen för att använda Google-inloggning][Google]
* [Så här konfigurerar du appen för att använda Account inloggning][MSA]
* [Så här konfigurerar du appen för att använda Twitter-inloggning][Twitter]
* [Så här: Använd anpassad autentisering för ditt program][custom-auth]

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
