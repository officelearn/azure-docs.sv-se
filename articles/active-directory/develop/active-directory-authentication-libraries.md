---
title: Azure Active Directory-Autentiseringsbibliotek | Microsoft Docs
description: "Azure AD Authentication Library (ADAL) kan klienten programvaruutvecklare autentisera enkelt användare till molnet eller lokala Active Directory (AD) och sedan hämta åtkomsttoken för att skydda API-anrop."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 603efa917e1b74eca4742795b3f72cb426fcbb2a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory-Autentiseringsbibliotek
Azure Active Directory Authentication Library (ADAL) gör det möjligt för programutvecklare att autentisera användare till molnet eller lokala Active Directory (AD) och hämta token för att skydda API-anrop. ADAL underlättar autentisering för utvecklare med hjälp av funktioner som:
 - Konfigurerbara token-cache att komma åt token och uppdatera token
 - automatisk token uppdatering när en åtkomst-token upphör att gälla och en uppdateringstoken är tillgänglig
 - stöd för asynkrona metodanrop
 - och mycket mer

> [!NOTE]
> Letar du efter Azure AD v2.0-bibliotek (MSAL)? Checka ut den [MSAL biblioteket guiden](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries). 
> 
> 

### <a name="client-libraries"></a>Klientbibliotek

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient Windows Store UWP Xamarin-iOS och Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Skrivbordsapp](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referens](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| .NET-klient Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Skrivbordsapp](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Den enda sidan App](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referens](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[Den centrala databasen](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android-app](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java-webbapp](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>Server-bibliotek 

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN för AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-App](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN för OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Webbapp](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD-Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Webb-API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN för WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC-Webbapp](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Tillägg för Identity-protokollet för .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT-hanterare för .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Klientbibliotek för v2.0 (MSAL)

Den [Azure AD v2.0-slutpunkten](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare) kombinerar Azure AD och Microsoft Accounts bakom en enda slutpunkt. Utvecklare kan använda för att komma åt den här slutpunkten den [stöd för produktion preview MSAL bibliotek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) i stället för ADAL.

| Plattform | Bibliotek | Ladda ned | Källkoden | Exempel | Referens
| --- | --- | --- | --- | --- | --- |
| .NET-klient Windows Store UWP Xamarin-iOS och Android |MSAL för .NET (förhandsgranskning) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Skrivbordsapp](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Referens](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |MSAL för JavaScript (förhandsgranskning) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Den enda sidan App](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Referens](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |MSAL för iOS (förhandsgranskning) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-app](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Referens](https://azuread.github.io/docs/objc/) |
| Android |MSAL för Android (förhandsgranskning) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-app](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Referens](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Scenarier

Här följer tre vanliga scenarier där ADAL kan användas för att autentisera en klient som ansluter till en fjärresurs:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autentisering av användare av en native client-program som körs på en enhet 

I detta scenario har en utvecklare ett WPF-klientprogram som behöver åtkomst till en fjärresurs som skyddas av Azure AD, till exempel ett webb-API. Han har en Azure-prenumeration, vet hur att anropa underordnat webb-API och vet Azure AD-klient som använder webb-API. Han kan därmed använder ADAL för att underlätta autentisering med Azure AD, antingen genom att delegera fullt ut autentiseringsupplevelse till ADAL eller genom att uttryckligen hantera autentiseringsuppgifter. ADAL gör det enkelt att autentisera användaren får en åtkomst-token och uppdateringstoken från Azure AD och använda den åtkomst-token för att göra begäranden i webb-API.

Kodexempel som visar det här scenariot använder autentisering till Azure AD, se [intern WPF klientprogrammet Web API](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autentisering av ett konfidentiellt klientprogram som körs på en webbserver

I det här scenariot kan har en utvecklare ett program som körs på en server som behöver åtkomst till en fjärresurs som skyddas av Azure AD, till exempel ett webb-API. Han har en Azure-prenumeration, vet hur att anropa tjänsten underordnade och vet använder Azure AD-klient webb-API. Han kan därmed använder ADAL för att underlätta autentisering med Azure AD genom att uttryckligen hantera programmets autentiseringsuppgifter. ADAL gör det enkelt att hämta en token från Azure AD med hjälp av autentiseringsuppgifter för programmets klienten och sedan använda denna token så att begäranden till webb-API. ADAL hanterar också hantera livslängden för åtkomst-token med cachelagringen och förnya efter behov. Kodexempel som visar det här scenariot, se [Daemon konsolen programmet till webb-API](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autentisering av ett konfidentiellt klientprogram som körs på en server för en användares räkning 

I det här scenariot kan har en utvecklare ett program som körs på en server som behöver åtkomst till en fjärresurs som skyddas av Azure AD, till exempel ett webb-API. Begäran måste också göras för ett Azure AD-användares räkning. Han har en Azure-prenumeration, vet hur att anropa underordnat webb-API och vet Azure AD-klient tjänsten använder. När användaren har autentiserats till webbprogrammet kan programmet hämta ett auktoriseringskod för användaren från Azure AD. Webbprogrammet kan sedan använda ADAL för att få en åtkomst-token och uppdatera token för en användare som använder de kod och klient-autentiseringsuppgifter som är kopplade till programmet från Azure AD. När webbprogrammet åtkomsttoken tillgång kan anropa det webb-API förrän token upphör att gälla. När token upphör att gälla kan webbprogrammet använda ADAL för att få en ny åtkomsttoken med hjälp av uppdateringen token som tidigare togs emot. Kodexempel som visar det här scenariot, se [Native client webb-API: et för Web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Se även

- [Utvecklarhandbok för Azure Active Directory](active-directory-developers-guide.md)
- [Autentiseringsscenarier för Azure Active directory](active-directory-authentication-scenarios.md)
- [Azure Active Directory-kodexempel](active-directory-code-samples.md)
