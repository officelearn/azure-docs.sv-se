---
title: Azure Active Directory-kodexempel | Microsoft Docs
description: Innehåller ett index över tillgänglig Azure Active Directory (V2 slutpunkten) kodexempel, ordnade efter scenario.
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
ms.openlocfilehash: b7a894ccd27ddcda2ab4b98c69333d37de077863
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156083"
---
# <a name="azure-active-directory-code-samples-v2-endpoint"></a>Kodexempel för Azure Active Directory (V2 slutpunkten)

Du kan använda Microsoft Azure Active Directory (Azure AD) för att:

- Lägg till autentisering och auktorisering till ditt webbprogram och webb-API: er.
- Kräv en åtkomst-token för att få åtkomst till en skyddad webb-API.

Den här artikeln beskriver kort och innehåller länkar till exempel för Azure AD-V2-slutpunkten. De här exemplen visar hur du kan göra, tillsammans med kodstycken som du kan använda i dina program. På sidan kod exempel hittar du detaljerad viktigt avsnitt som hjälp med krav, installation, och Ställ in. Kommentarer i koden finns det för att förstå viktiga avsnitt.

> [!NOTE]
> Om du är intresserad av V1-exempel finns [Azure AD-kodexempel (V1 slutpunkten)](active-directory-code-samples.md).

Enkelt scenario för varje typ av exemplet finns [apptyper för Azure Active Directory v2.0-slutpunkten](active-directory-v2-flows.md).

Du kan även bidra till exemplen på GitHub. Mer information finns i avsnittet [Microsoft Azure Active Directory-exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-apps"></a>Stationär dator och mobil offentliga klientprogram

Följande exempel visar offentliga klienten program (desktop/mobila program) som har åtkomst till Microsoft Graph eller ett webb-API för namnet på en användare.

klientprogram | Plattform | Flödet/bevilja | Anrop Microsoft Graph | Anropar ett webb-API för ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Fjärrskrivbord (WPF)      | .NET / C#  | Interaktiv | [DotNet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) <p/> [DotNet-admin-begränsade-scope-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [DotNet-intern-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2)
Mobile (UWP)   | .NET / C# (UWP) | Interaktiv | [DotNet-intern-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) |
Mobil (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktiv | [xamarin-intern-v2](https://Github.com/azure-samples/active-directory-xamarin-native-v2) |
Mobile (iOS)       | iOS / Objective C eller swift | Interaktiv | [IOS-swift-intern-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) <p/> [IOS-intern-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |
Mobil (Android)   | Android / Java | Interaktiv |   [Android-intern-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webbprogram som loggar in användare, anropa Microsoft Graph eller anropa ett webb-API med användarens identitet.

 Plattform | Endast loggar in användare | Loggar in användare och anropar Microsoft Graph 
 -------- | ------------------- | --------------------------------- 
ASP.NET 4.x | [appmodelv2-webapp-openIDConnect-dotNet](https://GitHub.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) <p/> [DotNet-webapp-openidconnect-v2](https://GitHub.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |              [ASPNET-ansluta-rest-sample](https://github.com/microsoftgraph/aspnet-connect-rest-sample)   
ASP.NET Core 2.0 | [aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) |              [aspnetcore ansluta exempel](https://github.com/microsoftgraph/aspnetcore-connect-sample)   
Node.js      |                   | [AppModelv2-WebApp-OpenIDConnect-nodejs](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs)     
Ruby      |                   | [Ruby-ansluta-rest-sample](https://github.com/microsoftgraph/ruby-connect-rest-sample)     

## <a name="daemon-applications"></a>Daemon för program

Följande exempel visar skrivbordet eller web program som har åtkomst till Microsoft Graph eller ett webb-API med Programidentitet (ingen användare).

klientprogram | Plattform | Flödet/bevilja | Anrop Microsoft Graph 
------------------ | -------- | ---------- | -------------------- 
Webbapp | .NET / C#  | Klientens autentiseringsuppgifter | [DotNet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) 

## <a name="single-page-applications-spa"></a>Sida program (SPA)

Det här exemplet visas hur du skriver en enstaka sida program som skyddas med Azure AD.

 Plattform |  Anrop Microsoft Graph 
 -------- |  --------------------- 
JavaScript (msal.js)  | [JavaScript-graphapi-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-v2) 
JavaScript (msal.js + AngularJS) | [vinkel-ansluta-rest-sample](https://github.com/microsoftgraph/angular-connect-rest-sample) 
JavaScript (Hello.JS)  | [JavaScript-graphapi-webb-v2](https://github.com/azure-samples/active-directory-javascript-graphapi-web-v2) 
JavaScript (hello.js + vinkel 4) | [angular4 ansluta exempel](https://github.com/microsoftgraph/angular4-connect-sample) 

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-ad"></a>Webb-API som skyddas av Azure AD

I följande exempel visas hur du skyddar ett webb-API med Azure AD-V2-slutpunkten.

Plattform | Exempel | Beskrivning
 -------- | ------------------- | ---------------------
Node.js | [DotNet-intern-aspnetcore-v2](https://GitHub.com/azure-samples/active-directory-dotnet-native-aspnetcore-v2) | En ASP.NET Core Web API-anrop från en WPF-program med hjälp av Azure AD-V2.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API som anropar Microsoft Graph eller en annan webb-API

Det här exemplet är inte tillgänglig ännu.

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph-exempel

Mer information om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och kurser som visar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD, se [Microsoft Graph Community exempel och självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

[Utvecklarhandbok för Azure Active Directory](active-directory-developers-guide.md)

[Azure AD Graph API konceptuell och referenser](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API hjälpbibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
