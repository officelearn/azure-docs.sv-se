---
title: Kodexempel för Microsoft identity-plattformen | Microsoft Docs
description: Innehåller ett index över tillgänglig Microsoft identity platform (v2.0-slutpunkt) kodexempel ordnas efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ceae71766c3e51a84cd09e8ac88740353e783bea
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482524"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Kodexempel för Microsoft identity-plattformen (v2.0-slutpunkt)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Du kan använda Microsoft identity-plattformen att:

- Lägg till autentisering och auktorisering till dina webbprogram och webb-API: er.
- Kräv en åtkomsttoken för att få åtkomst till ett skyddat webb-API.

Den här artikeln beskriver kortfattat och hittar du länkar till exempel för Microsoft identity-plattformen slutpunkten. De här exemplen visar hur det är klart och även ange kodstycken som du kan använda i dina program. På sidan kod exemplet hittar du detaljerade viktigt avsnitt som hjälp med krav, installation och inställningar. Kommentarer i koden hjälpa dig att förstå viktiga avsnitt.

> [!NOTE]
> Om du är intresserad av v1.0 exempel se [kodexempel för Azure AD (v1.0 slutpunkt)](sample-v1-code.md).

Information om grundläggande scenario för varje typ av exemplet finns i [typer av appar för Microsoft identity-plattformen slutpunkten](v2-app-types.md).

Du kan också bidra till exemplen på GitHub. Läs hur genom att läsa [Microsoft Azure Active Directory-exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Enkelsidigt program

De här exemplen visar hur du skriver ett enkelsidigt program som skyddas med Microsoft identity-plattformen. De här exemplen med någon av varianter av MSAL.js.

| Plattform | Beskrivning | Länk |
| -------- | --------------------- | -------- |
| ![Den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo_js.png) [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anrop egna webb-API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Den här bilden visar Angular JS-logotypen](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs)| Anropar Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![Den här bilden visar Angular logotypen](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Anropar Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webbprogram som loggar in användare. Några exempel visar också programmet anropar Microsoft Graph eller dina egna webb-API med användarens identitet.

| Plattform | Endast loggar in användare | Loggar in användare och anropar Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Den här bilden visar ASP.NET Core-logotyp](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Självstudie för ASP.NET Core WebApp loggar in användare](https://aka.ms/aspnetcore-webapp-sign-in) | Samma prov i den [ASP.NET Core-Webbapp anropar Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fas |
| ![Den här bilden visar ASP.NET-logotyp](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Snabbstart för ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Den här bilden visar Node.js-logotyp](media/sample-v2-code/logo_nodejs.png)  |                   | [Snabbstart för node.js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Den här bilden visar Ruby-logotyp](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Stationära och mobila offentliga klientappar

Följande exempel visar offentlig klient program (desktop eller mobile program) som har åtkomst till Microsoft Graph API eller dina egna webb-API: namnet på en användare. Alla dessa klientprogram använder Microsoft Authentication Library (MSAL).

| Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph | Anropar en ASP.NET Core 2.0 webb-API |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![Den här bilden visar .NET /C# logotyp](media/sample-v2-code/logo_NET.png) | [interaktiv](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Fjärrskrivbord (konsol)   | ![Den här bilden visar .NET /C# (skrivbord) logotyp](media/sample-v2-code/logo_NET.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Fjärrskrivbord (konsol)   | ![Den här bilden visar .NET /C# (skrivbord) logotyp](media/sample-v2-code/logo_NETcore.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET /C# (Xamarin)-logotyp](media/sample-v2-code/logo_xamarin.png) | [interaktiv](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Den här bilden visar iOS/Objective-C eller Swift](media/sample-v2-code/logo_iOS.png) | [interaktiv](msal-authentication-flows.md#interactive) |[ios-swift-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Mobile (Android)   | ![Den här bilden visar Android-logotyp](media/sample-v2-code/logo_Android.png) | [interaktiv](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Daemon för program

Följande exempel visar ett program som ansluter till Microsoft Graph API med identiteten (med inga användare).

| Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsolen | ![Den här bilden visar .NET Core-logotyp](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webbapp | ![Den här bilden visar ASP.NET-logotyp](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Fjärradministrerad program

I följande exempel visas ett offentliga klientprogram som körs på en enhet utan att en webbläsare. Appen kan vara ett kommandoradsverktyg, en app som körs på Linux- eller Mac- eller ett IoT-program. Exemplet har en app som har åtkomst till Microsoft Graph API, namnet på en användare som loggar in interaktivt på en annan enhet (till exempel en mobiltelefon). Det här klientprogrammet använder Microsoft Authentication Library (MSAL).

| Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Fjärrskrivbord (konsol)   | ![Den här bilden visar .NET /C# (skrivbord) logotyp](media/sample-v2-code/logo_NETcore.png) | [Kodflöde för enhet](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Webb-API:er

Följande exempel visar hur du skyddar ett webb-API med Microsoft identity-plattformen slutpunkt och hur du anropa en underordnad API från webb-API.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core-logotyp](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core webb-API (tjänst) för [dotnet-intern-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Den här bilden visar ASP.NET-logotyp](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webb-API (tjänst) för [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph-exempel

Vill veta mer om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och självstudier som demonstrerar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel och självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

- [Active Directory (v1.0) Utvecklarhandbok för Azure](v1-overview.md)
- [Azure AD Graph API konceptuell och referenser](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Azure AD Graph API hjälpbibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
