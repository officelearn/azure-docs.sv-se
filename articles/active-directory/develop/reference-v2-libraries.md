---
title: Azure Active Directory v2.0-autentiseringsbibliotek | Microsoft Docs
description: Kompatibla klientbibliotek och server mellanprogram-bibliotek och relaterade biblioteket, källa och länkar till exempel, för Azure Active Directory v2.0-slutpunkten.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 208ef1267571002a90433f0b3febebbb0ea7089a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173291"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0-autentiseringsbibliotek

Den [v2.0-slutpunkten för Azure Active Directory (Azure AD)](active-directory-v2-compare.md) stöder de vanliga OAuth 2.0 och OpenID Connect 1.0-protokoll. Microsoft Authentication Library (MSAL) är utformad för att arbeta med Azure AD v2.0-slutpunkten. Det är också möjligt att använda bibliotek med öppen källkod som stöder OAuth 2.0 och OpenID Connect 1.0.

Vi rekommenderar att du använder bibliotek skrivna av protokollet domän experter som följer en Security Development Lifecycle (SDL)-metod som [det följt av Microsoft][Microsoft-SDL]. Om du väljer att hand-kod för protokoll, bör du följa en metod som Microsofts SDL-processen och betala Stäng uppmärksam på säkerhetsaspekter i standarderna för varje protokoll.

> [!NOTE]
> Letar du efter Azure AD v1.0 library (ADAL)? Kolla in den [ADAL-biblioteket för](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typer av bibliotek

Azure AD v2.0-slutpunkten fungerar med två typer av bibliotek:

* **Klientbibliotek**: Interna klienter och servrar kan du använda klientbibliotek för att få åtkomsttoken för att anropa en resurs, till exempel Microsoft Graph.
* **Server-bibliotek för mellanprogram**: Webbappar som använder server mellanprogram-bibliotek för användarinloggning. Web API: erna använder server mellanprogram klientbiblioteken för att validera token som skickas av interna klienter eller av andra servrar.

## <a name="library-support"></a>Klientbiblioteksstöd

Bibliotek levereras i två kategorier för support:

* **Microsoft-supported**: Microsoft tillhandahåller korrigeringar för dessa bibliotek och har gjort SDL sistone på dessa bibliotek.
* **Kompatibel**: Microsoft har testat dessa bibliotek i grundläggande scenarier och bekräftat att den fungerar med v2.0-slutpunkten. Microsoft inte tillhandahåller korrigeringar för dessa bibliotek och inte har gjort en genomgång av dessa bibliotek. Problem och funktionsförfrågningar ska dirigeras till biblioteksprojekt öppen källkod.

En lista över bibliotek som fungerar med v2.0-slutpunkten finns i nästa avsnitt i den här artikeln.

## <a name="microsoft-supported-client-libraries"></a>Stöds av Microsoft-klientbibliotek

Klientbibliotek för autentisering som används för att hämta en token för att anropa ett skyddat webb-API

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens | Konceptuell dokument | Översikt |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js (förhandsversion) | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Enkelsidig app](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)|
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular(Preview) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL .NET (förhandsversion) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Skrivbordsapp](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS / Objective C eller swift](media/sample-v2-code/logo_iOS.png) | MSAL obj_c (förhandsversion) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-app](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL (förhandsversion) | [ Centrallager](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-app](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Stöds av Microsoft server mellanprogram-bibliotek

Mellanprogram biblioteken används för att skydda webbprogram och webb-API: er. För webbapp eller webb-API som skrivits med ASP.NET eller ASP.NET Core, middleware-biblioteken används av ASP.NET / ASP.NET Core

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET-säkerhet |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[ASP.NET Security (GitHub)](https://github.com/aspnet/Security) |[MVC-app](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-referens](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel tillägg för .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-app](quickstart-v2-aspnet-webapp.md) |[Referens](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD-Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webbapp](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Kompatibla klientbibliotek

| Plattform | Biblioteksnamn | Testade version | Källkod | Exempel |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Skriv Java](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP Leagues oauth2-klient](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
![iOS](media/sample-v2-code/logo_iOS.png) |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Inbyggd app-exempel](active-directory-v2-devquickstarts-ios.md) |

Du kan använda v2.0-slutpunkten för alla bibliotek med standardkompatibel därför är det viktigt att veta var du hittar support.

* Kontakta ägaren biblioteket problem och nya funktionbegäran i biblioteket kod.
* Kontakta Microsoft om problem och nya funktionbegäran på tjänstsidan protokollimplementering.
* [Filen en funktionsbegäran](https://feedback.azure.com/forums/169401-azure-active-directory) för ytterligare funktioner som du skulle vilja se i protokollet.
* [Skapa en supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) om du upptäcker ett problem där Azure AD v2.0-slutpunkten inte är kompatibel med OAuth 2.0 eller OpenID Connect 1.0.

## <a name="related-content"></a>Relaterat innehåll

Läs mer om Azure AD v2.0-slutpunkten, den [översikt över Azure AD app model v2.0][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
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

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
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
