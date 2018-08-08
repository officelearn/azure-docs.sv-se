---
title: Azure Active Directory-kodexempel | Microsoft Docs
description: Innehåller ett index över tillgängliga Azure Active Directory (V2-slutpunkt) kodexempel ordnas efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0be97bfbbaafd6045fd36be57d85e917f0325779
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600660"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Kodexempel för Azure Active Directory (V2-slutpunkt)

Du kan använda Microsoft Azure Active Directory (Azure AD) för att:

- Lägg till autentisering och auktorisering till dina webbprogram och webb-API: er.
- Kräv en åtkomsttoken för att få åtkomst till ett skyddat webb-API.

Den här artikeln beskriver kortfattat och tillhandahåller länkar till exempel för Azure AD V2-slutpunkten. De här exemplen visar hur man gör, tillsammans med kodfragment som du kan använda i dina program. På sidan kod exemplet hittar du detaljerade viktigt avsnitt som hjälp med krav, installation, och Ställ in. Kommentarer i koden finns det för att förstå viktiga avsnitt.

> [!NOTE]
> Om du är intresserad av V1-exempel finns i [kodexempel för Azure AD (V1-slutpunkt)](sample-v1-code.md).

Information om grundläggande scenario för varje typ av exemplet finns i [apptyperna för Azure Active Directory v2.0-slutpunkten](v2-app-types.md).

Du kan också bidra till exemplen på GitHub. Läs hur genom att läsa [Microsoft Azure Active Directory-exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Stationära och mobila offentliga klientappar

Följande exempel visar offentlig klient program (desktop/mobila program) som har åtkomst till Microsoft Graph eller ett webb-API för namnet på en användare.

Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph | En ASP.NET Core 2.0 webb-API-anrop
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)      | .NET / C#  | Interaktiv | [DotNet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin-begränsade-scope-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-intern-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobile (UWP)   | .NET / C# (UWP) | Interaktiv | [DotNet-intern-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobil (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktiv | [xamarin-intern-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | iOS / Objective C eller swift | Interaktiv | [IOS-swift-intern-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS-intern-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobil (Android)   | Android / Java | Interaktiv |   [Android-intern-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webbprogram som inloggning av användare, anropa Microsoft Graph eller anropa en webb-API med användarens identitet.

 Plattform | Endast loggar in användare | Loggar in användare och anropar Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-connect-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore-connect-sample](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-connect-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Daemon för program

Följande exempel visar desktop eller web program som har åtkomst till Microsoft Graph eller ett webb-API med Programidentitet (inga användare).

Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
Webbapp | .NET / C#  | Klientautentiseringsuppgifter | [DotNet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Program för ensidesapp (SPA)

Detta exempel visar hur du skriver en enda sida-program som skyddas med Azure AD.

 Plattform |  Anropar Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [angular-connect-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript-graphapi-web-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + Angular 4) | [angular4 ansluta exemplet](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-ad"></a>Webb-API som skyddas av Azure AD

I följande exempel visas hur du skyddar ett webb-API med Azure AD V2-slutpunkten.

Plattform | Exempel | Beskrivning
 -------- | ------------------- | ---------------------
Node.js | [DotNet-intern-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | En ASP.NET Core Web API-anrop från en WPF-program med Azure AD V2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API att anropa Microsoft Graph eller en annan webb-API

Det här exemplet är inte tillgänglig ännu.

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph-exempel

Vill veta mer om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och självstudier som demonstrerar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel och självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

[Utvecklarhandbok för Azure Active Directory](azure-ad-developers-guide.md)

[Azure AD Graph API konceptuell och referenser](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API hjälpbibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
