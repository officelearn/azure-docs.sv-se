---
title: Azure Active Directory-Autentiseringsbibliotek | Microsoft Docs
description: Azure AD Authentication Library (ADAL) kan klienten utvecklare att enkelt autentisera användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: bb8191ee715c2fee99e408035209e0d2c8f9f02b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447565"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory-Autentiseringsbibliotek

Azure Active Directory Authentication Library (ADAL) v1.0 programmet ger utvecklare möjlighet att autentisera användare till molnet eller lokala Active Directory (AD) och hämta token för att skydda API-anrop. ADAL underlättar autentisering för utvecklare via funktioner som:

- Konfigurerbara tokencache att butiker åtkomsttoken och uppdatera token
- Automatisk tokenuppdatering när en åtkomst-token upphör att gälla och en uppdateringstoken är tillgänglig
- Stöd för asynkrona metodanrop

> [!NOTE]
> Letar du efter Azure AD v2.0-bibliotek (MSAL)? Kolla in den [MSAL biblioteket guiden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

## <a name="microsoft-supported-client-libraries"></a>Stöds av Microsoft-klientbibliotek

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient, Windows Store, UWP, Xamarin iOS och Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Skrivbordsapp](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referens](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) |
| .NET-klienten, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Skrivbordsapp](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Den enda sidan App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referens](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Den centrala databasen](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js-webbapp](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referens](https://docs.microsoft.com/en-us/javascript/api/adal-node/?view=azure-node-latest) |
| Java |ADAL4J |[Maven 3.](http://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java-webbapp](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referens](http://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python-webbapp](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referens](http://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Stöds av Microsoft Server-bibliotek

| Plattform | Bibliotek | Ladda ned | Källkod | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN for AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-App](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN för OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Webbapp](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN för WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-Webbapp](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Tillägg för Identity-protokollet för .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT-hanterare för .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webb-API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>Scenarier

Här är tre vanliga scenarier för att använda ADAL i en klient som ansluter till en fjärresurs:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autentisera användare av ett internt klientprogram som körs på en enhet

I det här scenariot kan får en utvecklare en mobil klient eller program som behöver åtkomst till en fjärransluten resurs, till exempel ett webb-API. Webb-API: tillåter inte anonyma anrop och måste anropas i kontexten för en autentiserad användare. Webb-API är förinställd på att lita på åtkomsttoken som utfärdas av en viss Azure AD-klient. Azure AD är förinställd på att utfärda åtkomsttoken för den resursen. För att anropa webb-API från klienten använder utvecklaren ADAL för att underlätta autentisering med Azure AD. Det säkraste sättet att använda ADAL är att återge användargränssnittet för att samla in autentiseringsuppgifter (återgiven som webbläsarfönster).

ADAL gör det enkelt att autentisera användaren, hämta en åtkomsttoken och uppdateringstoken från Azure AD och sedan anropa webb-API med hjälp av åtkomst-token.

Ett kodexempel som visar det här scenariot med autentisering till Azure AD, se [Native Client WPF-program till webb-API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autentisera en konfidentiell klient-program som körs på en webbserver

I det här scenariot kan får en utvecklare du ett program som körs på en server som behöver åtkomst till en fjärransluten resurs, till exempel ett webb-API. Web API inte tillåter anonyma anropar så måste den anropas från en auktoriserad tjänst. Webb-API är förinställd på att lita på åtkomsttoken som utfärdas av en viss Azure AD-klient. Azure AD är förinställd på att utfärda åtkomsttoken för den resursen till en tjänst med klientens autentiseringsuppgifter (klient-ID och hemlighet). ADAL underlättar autentisering av tjänsten med Azure AD som returnerar en åtkomsttoken som kan användas för att anropa webb-API. ADAL hanterar också hantera livslängden för åtkomst-token genom att cachelagra och förnya den efter behov. Ett kodexempel som visar det här scenariot finns [Daemon konsolen program till webb-API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autentisera ett konfidentiellt klientprogram som körs på en server för en användares räkning

I det här scenariot kan får en utvecklare du ett webbprogram som körs på en server som behöver åtkomst till en fjärransluten resurs, till exempel ett webb-API. Webb-API: tillåter inte anonyma anrop, så måste den anropas från en auktoriserad tjänst för en autentiserad användare. Webb-API är förinställd på att lita på åtkomsttoken som utfärdas av en viss Azure AD-klient och Azure AD är förinställd på att utfärda åtkomsttoken för den resursen till en tjänst med klientens autentiseringsuppgifter. När användaren har autentiserats i webbprogrammet kan programmet hämta en auktoriseringskod för användaren från Azure AD. Webbprogrammet kan sedan använda ADAL för att hämta en åtkomsttoken och uppdatera token för en användare med hjälp av kod och klienten autentiseringsuppgifterna som är associerade med programmet från Azure AD. När webbprogrammet är tillgång åtkomsttoken kan anropa det webb-API: tills token upphör att gälla. När token upphör att gälla kan webbprogrammet använder ADAL för att få en ny åtkomsttoken med hjälp av uppdateringen åtkomsttoken som tidigare togs emot. Ett kodexempel som visar det här scenariot finns [Native client till webb-API till webb-API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Se även

- [Utvecklarhandbok för Azure Active Directory](azure-ad-developers-guide.md)
- [Autentiseringsscenarier för Azure Active directory](active-directory-authentication-scenarios.md)
- [Azure Active Directory-kodexempel](active-directory-code-samples.md)
