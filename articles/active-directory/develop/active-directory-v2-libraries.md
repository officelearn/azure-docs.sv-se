---
title: Autentiseringsbibliotek för Azure Active Directory v2.0 | Microsoft Docs
description: Kompatibel klientbibliotek och server mellanprogram bibliotek och relaterade biblioteket, källa och exempel länkar för Azure Active Directory v2.0-slutpunkten.
services: active-directory
documentationcenter: ''
author: SaeedAkhter-MSFT
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: saeeda
ms.custom: aaddev
ms.openlocfilehash: f961f8a6795df156549eece12c2c7e4cc26713ab
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0-autentiseringsbibliotek

Den [Azure Active Directory (AD Azure) v2.0-slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) stöder branschstandard OAuth 2.0 och OpenID Connect 1.0-protokollet. Microsoft Authentication Library (MSAL) är avsedd att fungera med Azure AD v2.0-slutpunkten.  Du kan också använda bibliotek med öppen källkod som stöder OAuth 2.0 och OpenID Connect 1.0.

Det rekommenderas att du använder bibliotek av protokollet domän experter som följer en Security Development Lifecycle (SDL)-metod som [det följt av Microsoft][Microsoft-SDL]. Om du väljer att hand kod stöd för protokoll, följer du en metod som Microsofts SDL och betala Stäng uppmärksam på säkerhet i standarderna för varje protokoll.

> [!NOTE]
> Letar du efter Azure AD v1.0 bibliotek (ADAL)? Checka ut den [ADAL-biblioteket guiden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).
>
>

## <a name="types-of-libraries"></a>Typer av bibliotek

Azure AD v2.0-slutpunkten fungerar med två typer av bibliotek:

* **Klientbibliotek**. Interna klienter och servrar kan du använda klientbibliotek för att få åtkomst-token för att anropa en resurs, till exempel Microsoft Graph.
* **Servern mellanprogram bibliotek**. Webbappar som använder server mellanprogram bibliotek för användarinloggning. Webb-API: er använda server mellanprogram bibliotek för att validera token som skickas av interna klienter eller av andra servrar.

## <a name="library-support"></a>Stöd

Eftersom du kan välja något standardkompatibel bibliotek när du använder v2.0-slutpunkten är det viktigt att du vet var för support. Kontakta ägaren biblioteket problem och funktioner som efterfrågas i biblioteket kod. Kontakta Microsoft-problem och funktioner som efterfrågas i protokollimplementering för tjänsten på klientsidan. [Filen en funktionsbegäran](https://feedback.azure.com/forums/169401-azure-active-directory) för ytterligare funktioner som du skulle vilja se i protokollet. [Skapa en supportbegäran](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request) om du upptäcker ett problem där Azure AD v2.0-slutpunkten inte är kompatibel med OAuth 2.0 eller OpenID Connect 1.0.

Bibliotek finns i två kategorier för support:

* **Stöds av Microsoft**. Microsoft tillhandahåller korrigeringar för dessa bibliotek och har utfört SDL vederbörlig möda åt på dessa bibliotek.
* **Kompatibel**. Microsoft har testat dessa bibliotek i grundläggande scenarier och bekräftat att den fungerar med v2.0-slutpunkten. Microsoft inte tillhandahåller åtgärdar för dessa bibliotek och inte har gjort en genomgång av dessa bibliotek. Problem och funktionsförfrågningar ska dirigeras till biblioteksprojekt öppen källkod.

En lista över bibliotek som fungerar med v2.0-slutpunkten finns i nästa avsnitt i den här artikeln.

## <a name="microsoft-supported-client-libraries"></a>Stöds av Microsoft klientbibliotek

> [!IMPORTANT]
> MSAL preview bibliotek är lämpliga för användning i en produktionsmiljö. Microsoft tillhandahåller samma nivå produktionsstöd för dessa bibliotek som de aktuella produktions-bibliotek (ADAL). Förvänta dig MSAL API, interna format och andra mekanismer för dessa bibliotek utan föregående meddelande, som du behöver ta tillsammans med felkorrigeringar eller funktionsförbättringar i förhandsversionen. Detta kan påverka ditt program. En ändring av cache-formatet kan exempelvis kräva användarna att logga in igen. Ändra ett API måste du kanske uppdatera din kod. När versionen för allmän tillgänglighet (GA) blir tillgänglig alla program som använder en förhandsversion av biblioteket måste uppdatera i sex månader eller de kan sluta fungera.

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient Windows Store UWP Xamarin-iOS och Android | MSAL .NET (förhandsgranskning) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Skrivbordsapp](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (förhandsgranskning) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Den enda sidan App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS macOS | MSAL (förhandsgranskning) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-App](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (förhandsgranskning) | [Den centrala databasen](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-App](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Stöds av Microsoft server mellanprogram bibliotek

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect mellanprogram |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/) |[MVC-App](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | OWIN OAuth ägar mellanprogram för AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/) |  | |
| .NET 4.x | JWT-hanterare för .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET OpenID Connect mellanprogram |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET-säkerhet (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC-app](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET OAuth ägar mellanprogram |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET-säkerhet (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | JWT-hanterare för .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webbprogram](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Kompatibel klientbibliotek

| Plattform | Biblioteksnamn | Testad version | Källkod | Exempel |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Exempel på inbyggda appen](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exempel på inbyggda appen](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| PHP | [PHP Leagues oauth2-klient](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-klient](https://github.com/thephpleague/oauth2-client/) | |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>Relaterat innehåll
Läs mer om Azure AD v2.0-slutpunkten på [översikt över Azure AD app model v2.0][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
