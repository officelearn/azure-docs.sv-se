---
title: "Autentiseringsbibliotek för Azure Active Directory v2.0 | Microsoft Docs"
description: "Kompatibel klientbibliotek och server mellanprogram bibliotek och relaterade biblioteket, källa och exempel länkar för Azure Active Directory v2.0-slutpunkten."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 50a1cc0bf9e00cf5b866b88b3e88c62b06a2376b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0-autentiseringsbibliotek
Den [Azure Active Directory (AD Azure) v2.0-slutpunkten](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare) stöder branschstandard OAuth 2.0 och OpenID Connect 1.0-protokollet. Du kan använda olika bibliotek från Microsoft och andra företag med v2.0-slutpunkten.

När du skapar ett program som använder v2.0-slutpunkten rekommenderar vi att du använder bibliotek som har skrivits av protokollet domän experter som följer en Security Development Lifecycle (SDL)-metod som [det följt av Microsoft][Microsoft-SDL]. Om du väljer att hand kod stöd för protokoll, rekommenderar vi du följer SDL-metoder och uppmärksam på säkerhetsaspekter i standarderna för varje protokoll.

> [!NOTE]
> Letar du efter Azure AD v1.0 bibliotek (ADAL)? Checka ut den [ADAL-biblioteket guiden](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-authentication-libraries). 
> 
> 

## <a name="types-of-libraries"></a>Typer av bibliotek
Azure AD v2.0-slutpunkten fungerar med två typer av bibliotek:

* **Klientbibliotek**. Interna klienter och servrar kan du använda klientbibliotek för att få åtkomst-token för att anropa en resurs, till exempel Microsoft Graph.
* **Servern mellanprogram bibliotek**. Webbappar som använder server mellanprogram bibliotek för användarinloggning. Webb-API: er använda server mellanprogram bibliotek för att validera token som skickas av interna klienter eller av andra servrar.

## <a name="library-support"></a>Stöd
Eftersom du kan välja något standardkompatibel bibliotek när du använder v2.0-slutpunkten är det viktigt att du vet var för support. Kontakta ägaren biblioteket problem och funktioner som efterfrågas i biblioteket kod. Kontakta Microsoft-problem och funktioner som efterfrågas i protokollimplementering för tjänsten på klientsidan.

Bibliotek finns i två kategorier för support:

* **Stöds av Microsoft**. Microsoft tillhandahåller korrigeringar för dessa bibliotek och har utfört SDL vederbörlig möda åt på dessa bibliotek.
* **Kompatibel**. Microsoft har testat dessa bibliotek i grundläggande scenarier och bekräftat att den fungerar med v2.0-slutpunkten. Microsoft inte tillhandahåller åtgärdar för dessa bibliotek och inte har gjort en genomgång av dessa bibliotek. Problem och funktionsförfrågningar ska dirigeras till biblioteksprojekt öppen källkod.

En lista över bibliotek som fungerar med v2.0-slutpunkten finns i nästa avsnitt i den här artikeln.


## <a name="microsoft-supported-client-libraries"></a>Stöds av Microsoft klientbibliotek

> [!IMPORTANT]
> MSAL preview bibliotek är lämpliga för användning i en produktionsmiljö. Vi ger samma nivå produktionsstöd för dessa bibliotek som vi gör våra aktuella produktions-bibliotek (ADAL). Vi kan göra ändringar i MSAL-API, interna format och andra mekanismer för dessa bibliotek utan föregående meddelande, som du behöver ta tillsammans med felkorrigeringar eller funktionsförbättringar under förhandsgranskningen. Detta kan påverka ditt program. Exempelvis kan kan en ändring av cache-format påverka dina användare, till exempel att de behöver logga in igen. Ändra ett API måste du kanske uppdatera din kod. När vi anger versionen för allmän tillgänglighet som vi gör att du måste uppdatera till version för allmän tillgänglighet i sex månader som program som skrivits med en förhandsgranskning fungerar versionen av biblioteket inte längre.

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient Windows Store UWP Xamarin-iOS och Android | MSAL .NET (förhandsgranskning) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Skrivbordsapp](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (förhandsgranskning) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Den enda sidan App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS macOS | MSAL (förhandsgranskning) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-App](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (förhandsgranskning) | [Den centrala databasen](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-App](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Stöds av Microsoft server mellanprogram bibliotek

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect mellanprogram |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-App](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | OWIN OAuth ägar mellanprogram för AzureAD |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | JWT-hanterare för .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET OpenID Connect mellanprogram |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET-säkerhet (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC-app](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET OAuth ägar mellanprogram |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.NET-säkerhet (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | JWT-hanterare för .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webbprogram](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>Kompatibel klientbibliotek
| Plattform | Biblioteksnamn | Testad version | Källkod | Exempel |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[Exempel på inbyggda appen](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Exempel på inbyggda appen](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |

## <a name="compatible-server-middleware-libraries"></a>Kompatibla servern mellanprogram bibliotek
| Plattform | Biblioteksnamn | Testad version | Källkod | Exempel |
|:---:|:---:|:---:|:---:|:---:|
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [Version 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [PHP Leagues oauth2-klient](https://github.com/thephpleague/oauth2-client) | [Version 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-klient](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Python Flask |[Flask OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask OAuthlib](https://github.com/lepture/flask-oauthlib) |[Webbapp](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
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
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
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
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
