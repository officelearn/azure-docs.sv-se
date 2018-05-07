---
title: Azure Active Directory-Autentiseringsbibliotek | Microsoft Docs
description: Azure AD Authentication Library (ADAL) kan klienten programvaruutvecklare autentisera enkelt användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop.
services: active-directory
documentationcenter: ''
author: SaeedAkhter-MSFT
manager: mtillman
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1e82fa62c86c80b1e68fea995ca6f90861688d5c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory-Autentiseringsbibliotek

Azure Active Directory Authentication Library (ADAL) v1.0 gör det möjligt för programutvecklare att autentisera användare till molnet eller lokala Active Directory (AD) och hämta token för att skydda API-anrop. ADAL underlättar autentisering för utvecklare med hjälp av funktioner som:

- Konfigurerbara token-cache att komma åt token och uppdatera token
- automatisk token uppdatering när en åtkomst-token upphör att gälla och en uppdateringstoken är tillgänglig
- stöd för asynkrona metodanrop

> [!NOTE]
> Letar du efter Azure AD v2.0-bibliotek (MSAL)? Checka ut den [MSAL biblioteket guiden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Stöds av Microsoft klientbibliotek

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient Windows Store UWP Xamarin-iOS och Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Skrivbordsapp](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referens](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET-klient Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Skrivbordsapp](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Den enda sidan App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referens](https://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[Den centrala databasen](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js-webbapp](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)| |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java-webbapp](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-webbapp](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) | |

## <a name="microsoft-supported-server-libraries"></a>Stöds av Microsoft Server-bibliotek

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN för AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-App](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN för OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Webbapp](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN för WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-Webbapp](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Tillägg för Identity-protokollet för .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT-hanterare för .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webb-API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scenarier

Här följer tre vanliga scenarier för att använda ADAL i en klient som ansluter till en fjärresurs:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autentisering av användare av en native client-program som körs på en enhet

I detta scenario har en utvecklare en mobil klient eller program som behöver åtkomst till en fjärresurs, till exempel ett webb-API. Webb-API tillåter inte anonyma anrop och måste anropas i kontexten för en autentiserad användare. Webb-API som är förkonfigurerad att lita på åtkomst-token som utfärdas av en specifik Azure AD-klient. Azure AD är förkonfigurerad att utfärda åtkomsttoken för den här resursen. Om du vill anropa webb-API från klienten använder utvecklaren ADAL för att underlätta autentisering med Azure AD. Det säkraste sättet att använda ADAL är den återge användargränssnittet för att samla in autentiseringsuppgifter (renderas som webbläsarfönster).

ADAL gör det enkelt att autentisera användaren får en åtkomst-token och uppdateringstoken från Azure AD och sedan anropa webb-API: et med åtkomst-token.

Kodexempel som visar det här scenariot använder autentisering till Azure AD, se [intern WPF klientprogrammet Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autentisering av ett konfidentiellt klientprogram som körs på en webbserver

I det här scenariot kan har en utvecklare ett program som körs på en server som behöver åtkomst till en fjärresurs, till exempel ett webb-API. Web API inte tillåter anonym anropar så måste anropas från en auktoriserad tjänst. Webb-API som är förkonfigurerad att lita på åtkomst-token som utfärdas av en specifik Azure AD-klient. Azure AD är förkonfigurerad att utfärda åtkomsttoken för den här resursen till en tjänst med klientens autentiseringsuppgifter (klient-ID och hemligt). ADAL underlättar autentisering av tjänsten med Azure AD som returnerar en åtkomsttoken som kan användas för att anropa webb-API. ADAL hanterar också hantera livslängden för åtkomst-token med cachelagringen och förnya efter behov. Kodexempel som visar det här scenariot, se [Daemon konsolen programmet till webb-API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autentisering av ett konfidentiellt klientprogram som körs på en server för en användares räkning

I det här scenariot kan har en utvecklare ett webbprogram som körs på en server som behöver åtkomst till en fjärresurs, till exempel ett webb-API. Webb-API tillåter inte anonym anrop, så måste anropas från en auktoriserad tjänst på uppdrag av en autentiserad användare. Webb-API som är förkonfigurerad för att lita på åtkomsttoken som utfärdats av en viss Azure AD-klient och Azure AD har förkonfigurerats att utfärda åtkomsttoken för den här resursen till en tjänst med klientens autentiseringsuppgifter. När användaren har autentiserats i webbprogrammet kan programmet hämta ett auktoriseringskod för användaren från Azure AD. Webbprogrammet kan sedan använda ADAL för att få en åtkomst-token och uppdatera token för en användare som använder de kod och klient-autentiseringsuppgifter som är kopplade till programmet från Azure AD. När webbprogrammet åtkomsttoken tillgång kan anropa det webb-API förrän token upphör att gälla. När token upphör att gälla kan webbprogrammet använda ADAL för att få en ny åtkomsttoken med hjälp av uppdateringen token som tidigare togs emot. Kodexempel som visar det här scenariot, se [Native client webb-API: et för Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Se även

- [Utvecklarhandbok för Azure Active Directory](active-directory-developers-guide.md)
- [Autentiseringsscenarier för Azure Active directory](active-directory-authentication-scenarios.md)
- [Azure Active Directory-kodexempel](active-directory-code-samples.md)
