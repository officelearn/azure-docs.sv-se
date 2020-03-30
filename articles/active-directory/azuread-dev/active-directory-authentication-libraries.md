---
title: Azure Active Directory-autentiseringsbibliotek | Microsoft-dokument
description: Azure AD Authentication Library (ADAL) gör det möjligt för klientprogramutvecklare att enkelt autentisera användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 32375c14d95dc9e100cbf496c8550f0a195a11dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154618"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory Authentication Library-bibliotek

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Med ADAL (Azure Active Directory Authentication Library) v1.0 kan programutvecklare autentisera användare till moln eller lokalt Active Directory (AD) och hämta token för att skydda API-anrop. ADAL gör autentiseringen enklare för utvecklare genom funktioner som:

- Konfigurerbar tokencache som lagrar åtkomsttoken och uppdaterar token
- Automatisk tokenuppdatering när en åtkomsttoken upphör att gälla och en uppdateringstoken är tillgänglig
- Stöd för asynkrona metodanrop

> [!NOTE]
> Letar du efter Azure AD v2.0-bibliotek (MSAL)? Kassan [msal-biblioteksguiden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Microsoft-stödda klientbibliotek

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient, Windows Store, UWP, Xamarin iOS och Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Skrivbordsapp](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referens](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Ensidesapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referens](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-appen](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs (JavaDocs)](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js-webbapp](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referens](https://docs.microsoft.com/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java-webbapp](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referens](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Appen Pythons webb](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referens](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft-stödda serverbibliotek

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN för AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN för OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Webbapp](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN för WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[MVC-webbapp](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identitetsprotokolltillägg för .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT-hanterare för .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD-pass |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webb-API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scenarier

Här är tre vanliga scenarier för att använda ADAL i en klient som har åtkomst till en fjärrresurs:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autentisera användare av ett inbyggt klientprogram som körs på en enhet

I det här fallet har en utvecklare en mobil klient eller ett skrivbordsprogram som behöver komma åt en fjärrresurs, till exempel ett webb-API. Webb-API:et tillåter inte anonyma samtal och måste anropas i kontexten för en autentiserade användare. Webb-API:et är förkonfigurerat för att lita på åtkomsttoken som utfärdats av en specifik Azure AD-klientorganisation. Azure AD är förkonfigurerat för att utfärda åtkomsttoken för den resursen. För att anropa webb-API:et från klienten använder utvecklaren ADAL för att underlätta autentisering med Azure AD. Det säkraste sättet att använda ADAL är att få det att återge användargränssnittet för att samla in användaruppgifter (återges som webbläsarfönster).

ADAL gör det enkelt att autentisera användaren, hämta en åtkomsttoken och uppdatera token från Azure AD och sedan anropa webb-API:et med hjälp av åtkomsttoken.

Ett kodexempel som demonstrerar det här scenariot med autentisering till Azure AD finns i [WPF-program för inbyggd klient till webb-API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autentisera ett konfidentiellt klientprogram som körs på en webbserver

I det här fallet har en utvecklare ett program som körs på en server som behöver komma åt en fjärrresurs, till exempel ett webb-API. Webb-API:et tillåter inte anonyma samtal, så det måste anropas från en auktoriserad tjänst. Webb-API:et är förkonfigurerat för att lita på åtkomsttoken som utfärdats av en specifik Azure AD-klientorganisation. Azure AD är förkonfigurerat för att utfärda åtkomsttoken för den resursen till en tjänst med klientautentiseringsuppgifter (klient-ID och hemlighet). ADAL underlättar autentisering av tjänsten med Azure AD returnerar en åtkomsttoken som kan användas för att anropa webb-API. ADAL hanterar också hanteringen av åtkomsttokens livstid genom att cachelägga den och förnya den efter behov. Ett kodexempel som visar det här scenariot finns i [Daemon console Application to Web API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autentisera ett konfidentiellt klientprogram som körs på en server för en användares räkning

I det här fallet har en utvecklare ett webbprogram som körs på en server som behöver komma åt en fjärrresurs, till exempel ett webb-API. Webb-API:et tillåter inte anonyma samtal, så det måste anropas från en auktoriserad tjänst för en autentiserat användares räkning. Webb-API:et är förkonfigurerat för att lita på åtkomsttoken som utfärdats av en specifik Azure AD-klientorganisation och Azure AD är förkonfigurerat för att utfärda åtkomsttoken för den resursen till en tjänst med klientautentiseringsuppgifter. När användaren har autentiserats i webbprogrammet kan programmet hämta en auktoriseringskod för användaren från Azure AD. Webbprogrammet kan sedan använda ADAL för att hämta en åtkomsttoken och uppdatera token för en användares räkning med hjälp av auktoriseringskoden och klientautentiseringsuppgifterna som är associerade med programmet från Azure AD. När webbprogrammet har åtkomsttoken kan det anropa webb-API:et tills token upphör att gälla. När token upphör att gälla kan webbprogrammet använda ADAL för att få en ny åtkomsttoken med hjälp av uppdateringstoken som tidigare togs emot. Ett kodexempel som visar det här scenariot finns i [Native-klienten till webb-API till webb-API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Se även

- [Utvecklarguide för Azure Active Directory](v1-overview.md)
- [Autentiseringsscenarier för Azure Active-katalog](v1-authentication-scenarios.md)
- [Exempel på Azure Active Directory-kod](sample-v1-code.md)
