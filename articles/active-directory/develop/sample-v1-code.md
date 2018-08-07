---
title: Azure Active Directory-kodexempel | Microsoft Docs
description: Innehåller ett index i Azure Active Directory (v1-slutpunkt) kodexempel ordnas efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 221ff82e9c8d5164dfc7d7ee25623e6f73f59e13
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581745"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Kodexempel för Azure Active Directory (V1-slutpunkt)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Du kan använda Microsoft Azure Active Directory (Azure AD) för att lägga till autentisering och auktorisering till dina webbprogram och webb-API: er.

Det här avsnittet innehåller länkar till exempel som du kan använda för att lära dig mer om Azure AD V1-slutpunkten. De här exemplen visar hur man gör tillsammans med kodfragment som du kan använda i dina program. På sidan kod exemplet hittar du detaljerade Läs-mig avsnitt som hjälp med krav, installation och konfiguration. Och koden har kommenterats för att förstå viktiga avsnitt.

> [!NOTE]
> Om du är intresserad av kodexempel för Azure AD V2, se [v2.0-kodexempel efter scenario](sample-v2-code.md).

Information om grundläggande scenario för varje typ av exemplet finns i [autentiseringsscenarier för Azure AD](authentication-scenarios.md).

Du kan också bidra till vårt exempel på GitHub. Läs hur genom att läsa [Microsoft Azure Active Directory-exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Stationära och mobila offentliga klientprogram anropar Microsoft Graph eller ett webb-API

Följande exempel visar offentlig klient program (desktop/mobila program) som har åtkomst till Microsoft Graph eller ett webb-API för namnet på en användare.

Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph | Ett ASP.NET eller ASP.NET Core 2.0 webb-API-anrop
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | .NET / C# | Interaktiv | [DotNet-intern-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet-intern-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [aspnetcore-DotNet-intern](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi-manuell-jwt-verifiering](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .NET / C#  | Interaktiv | [DotNet-intern-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [DotNet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (enskild klient webb-API) </p> [DotNet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (webb-API med flera innehavare)|
Mobil (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktiv | [DotNet-intern-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | Android/Java | Interaktiv |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | iOS/mål-C | Interaktiv |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Fjärrskrivbord (konsol)          | .NET / C# | Användarnamn / lösenord </p> Windows-integrerad autentisering | | [DotNet-intern-fjärradministrerad](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Fjärrskrivbord (konsol)           | .NET core / C# | Profil för enheter | | [DotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Webbprogram

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webbprogram som loggar in användare, anropa Microsoft Graph eller ett webb-API med användarens identitet

 Plattform | Endast loggar in användare | Anropar Microsoft Graph eller AAD Graph| En annan ASP.NET eller ASP.NET Core 2.0 Web API-anrop
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [WebApp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD Graph) |
ASP.NET Core 2.0 | [DotNet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [WebApp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD Graph) | [DotNet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [DotNet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [Python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [Java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [PHP-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webbprogram som demonstrerar rollbaserad åtkomstkontroll (auktorisering)

Följande exempel visar hur du implementerar rollbaserad åtkomstkontroll som används för att begränsa behörigheterna för vissa funktioner i ett webbprogram till vissa användare. Användarna har behörighet beroende på om de hör till en Azure AD-grupp eller roll.

Plattform | Exempel | Beskrivning
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [DotNet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | En .NET 4.5 MVC-webbapp som använder Azure AD **grupper** för auktorisering
ASP.NET 4.5 | [DotNet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | En .NET 4.5 MVC-webbapp som använder Azure AD **roller** för auktorisering

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-program (åtkomst till webb-API: er med programmets identitet)

Följande exempel visar desktop eller web program som har åtkomst till Microsoft Graph eller ett webb-API utan användare (med Programidentitet).

Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph | Ett ASP.NET eller ASP.NET Core 2.0 webb-API-anrop
------------------ | -------- | ---------- | -------------------- | -------------------------
Daemon för app (konsol)          | .NET / C#  | Klientens autentiseringsuppgifter med apphemlighet eller certifikat | | [DotNet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-daemon-certifikat-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon för app (konsol)         | .NET core / C# | Klientens autentiseringsuppgifter med certifikat| | [dotnetcore-daemon-certifikat-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Skrivbord            | Java | Klientautentiseringsuppgifter |   [Java-intern-fjärradministrerad](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET-Webbapp  | .NET / C# | Klientautentiseringsuppgifter |    | [DotNet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-active-directory"></a>Webb-API som skyddas av Azure Active Directory

I följande exempel visas hur du skyddar en node.js-webb-API med Azure AD.

Plattform | Exempel | Beskrivning
 -------- | ------------------- | ---------------------
Node.js | [noden webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  NodeJS webb-API som skyddas med hjälp av Azure AD och OAuth 2.0-åtkomsttoken.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API att anropa Microsoft Graph eller en annan webb-API

Följande exempel visar ett webb-API som anropar en annan webb-API. Det andra exemplet visar hur du hanterar villkorlig åtkomst.

 Plattform |  Anropar Microsoft Graph | En annan ASP.NET eller ASP.NET Core 2.0 Web API-anrop
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [onbehalfof-webapi-DotNet](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[onbehalfof-webapi-DotNet](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>En sida-program

Detta exempel visar hur du skriver en enda sida-program som skyddas med Azure AD.

 Plattform |  Anropar Microsoft Graph | En egen API-anrop | Anropar en annan webb-API
 -------- |  --------------------- | ------------------ | ----------------
JavaScript / ASP.NET 4.x |  | [JavaScript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) / ASP.NET 4.x |  | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) / ASP.NET 4.x |  |  | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph-exempel

Exempel och självstudier som visar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel och självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

[Utvecklarhandbok för Azure Active Directory](azure-ad-developers-guide.md)

[Azure AD Graph API konceptuell och referenser](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API hjälpbibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
