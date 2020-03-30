---
title: Autentiseringsbibliotek för Microsoft-identitetsplattform | Microsoft-dokument
description: Kompatibla klientbibliotek och serverbibliotek för mellanprogram, tillsammans med relaterade biblioteks-, käll- och exempellänkar, för slutpunkten för Microsofts identitetsplattform.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9d2df175fa9d1ed33eb17ae85e01a4fd7a24e728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611937"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Autentiseringsbibliotek för Microsoft-identitetsplattform

[Slutpunkten för Microsoft-identitetsplattform](active-directory-v2-compare.md) stöder protokollen OAuth 2.0 och OpenID Connect 1.0 i branschstandard. Microsoft Authentication Library (MSAL) är utformat för att fungera med slutpunkten för Microsoft-identitetsplattformen. Du kan också använda bibliotek med öppen källkod som stöder OAuth 2.0 och OpenID Connect 1.0.

Vi rekommenderar att du använder bibliotek skrivna av protokolldomänexperter som följer en SDL-metod (Security Development Lifecycle). Sådana metoder inkluderar [den som Microsoft följer][Microsoft-SDL]. Om du handkod för protokollen bör du följa en metod som Microsoft SDL. Var uppmärksam på säkerhetsaspekterna i standardspecifikationerna för varje protokoll.

> [!NOTE]
> Letar du efter Azure Active Directory Authentication Library (ADAL)? Kolla in [ADAL biblioteksguide](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typer av bibliotek

Slutpunkten för Microsoft identity-plattformen fungerar med två typer av bibliotek:

* **Klientbibliotek**: Inbyggda klienter och servrar använder klientbibliotek för att hämta åtkomsttoken för att anropa en resurs som Microsoft Graph.
* **Server middleware bibliotek:** Webbappar använder server middleware bibliotek för användare inloggning. Webb-API:er använder serverbibliotek för att validera token som skickas av inbyggda klienter eller av andra servrar.

## <a name="library-support"></a>Biblioteksstöd

Biblioteken finns i två stödkategorier:

* **Microsoft-stödda:** Microsoft tillhandahåller korrigeringar för dessa bibliotek och har gjort SDL due diligence på dessa bibliotek.
* **Kompatibel**: Microsoft har testat dessa bibliotek i grundläggande scenarier och har bekräftat att de fungerar med slutpunkten för Microsoft-identitetsplattformen. Microsoft tillhandahåller inte korrigeringar för dessa bibliotek och har inte gjort en granskning av dessa bibliotek. Problem och funktionsförfrågningar bör riktas till bibliotekets projekt med öppen källkod.

En lista över bibliotek som fungerar med slutpunkten för Microsoft-identitetsplattform finns i följande avsnitt.

## <a name="microsoft-supported-client-libraries"></a>Microsoft-stödda klientbibliotek

Använd klientautentiseringsbibliotek för att hämta en token för att anropa ett skyddat webb-API.

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens | Konceptuellt dokument | Översikt |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM (NPM)](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Ensidesapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referens](https://azuread.github.io/microsoft-authentication-library-for-js/docs/msal/) | [Konceptuella dokument](msal-overview.md)| [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Vinkel JS | [NPM (NPM)](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL-vinkel (förhandsgranskning) | [NPM (NPM)](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Skrivbordsapp](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Konceptuella dokument](msal-overview.md) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Prover](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [LäsDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Prover](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Referens](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS och macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-app](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [macOS-app](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Referens](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Konceptuella dokument](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Central databas](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-appen](quickstart-v2-android.md) | [JavaDocs (JavaDocs)](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Konceptuella dokument](msal-overview.md) |[Översikt](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft-stödda server middleware-bibliotek

Använd middleware-bibliotek för att skydda webbprogram och webb-API:er. Webbappar eller webb-API:er som är skrivna med ASP.NET eller ASP.NET Core använder middleware-biblioteken.

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET säkerhet |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC-app](quickstart-v2-aspnet-webapp.md) |[api-referens för ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel-tillägg för .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-app](quickstart-v2-aspnet-webapp.md) |[Referens](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD-pass |[NPM (NPM)](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webbapp](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Microsoft-stödda bibliotek efter OPERATIVSYSTEM /språk

I termen operativsystem mot språk som stöds är mappningen följande:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Kärna, MSAL.Net (.NET FW, Kärna, UWP)| ASP.NET Kärna, MSAL.Net (.NET Kärna) | ASP.NET Kärna, MSAL.Net (MacOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL för iOS och macOS](msal-overview.md) | [MSAL för iOS och macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j (msal4j) | msal4j (msal4j) | msal4j (msal4j) | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport.node | Passport.node | Passport.node |

Se även [Scenarier med plattformar och språk som stöds](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Kompatibla klientbibliotek

| Plattform | Biblioteksnamn | Testad version | Källkod | Exempel |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hej js](https://adodson.com/hello.js/) | Version 1.13.5 |[Hej js](https://github.com/MrSwitch/hello.js) |[Spa](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Skriv Java](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava (ScribeJava)](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect-bibliotek](https://github.com/GluuFederation/oxAuth) | [Version 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect-bibliotek](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Begäran om OAuthlib](https://github.com/requests/requests-oauthlib) | [Version 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Begäran om OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-klient](https://github.com/panva/node-openid-client) | [Version 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-klient](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-klient](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-klient](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[Omniauth (olika år)](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[Omniauth (olika år)](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reagera på inbyggd appauth](https://github.com/FormidableLabs/react-native-app-auth) | [Version 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagera på inbyggd appauth](https://github.com/FormidableLabs/react-native-app-auth) | |

För alla standardkompatibla bibliotek kan du använda slutpunkten för Microsoft identity platform. Det är viktigt att veta vart du ska gå för support:

* Kontakta bibliotekets ägare om du vill ha ärenden och nya funktionsförfrågningar i bibliotekskoden.
* Kontakta Microsoft om du vill ha problem och nya funktionsbegäranden i protokollimplementeringen på tjänstsidan.
* [Lämna in en funktionsbegäran om](https://feedback.azure.com/forums/169401-azure-active-directory) ytterligare funktioner som du vill se i protokollet.
* [Skapa en supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) om du hittar ett problem där slutpunkten för Microsoft-identitetsplattformen inte är kompatibel med OAuth 2.0 eller OpenID Connect 1.0.

## <a name="related-content"></a>Relaterat innehåll

Mer information om slutpunkten för Microsofts identitetsplattform finns i [översikten över Microsofts identitetsplattform][AAD-App-Model-V2-Overview].

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
