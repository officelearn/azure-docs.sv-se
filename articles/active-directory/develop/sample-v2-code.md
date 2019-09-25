---
title: Kod exempel för Microsoft Identity Platform | Microsoft Docs
description: Innehåller ett index över tillgängliga kod exempel för Microsoft Identity Platform (v 2.0-slut punkt), ordnat efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0afefe81e81f8ba83a5c8ac2b0fad61926bbf09
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268534"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Kod exempel för Microsoft Identity Platform (v 2.0-slut punkt)

Du kan använda Microsoft Identity Platform för att:

- Lägg till autentisering och auktorisering för dina webb program och webb-API: er.
- Kräv en åtkomsttoken för att få åtkomst till ett skyddat webb-API.

Den här artikeln beskriver och tillhandahåller länkar till exempel för Microsoft Identity Platform-slutpunkten. De här exemplen visar hur det är färdigt och innehåller även kodfragment som du kan använda i dina program. På sidan kod exempel hittar du detaljerade README-avsnitt som hjälper dig med krav, installation och konfiguration. Kommentarer i koden hjälper dig att förstå de kritiska avsnitten.

> [!NOTE]
> Om du är intresse rad av v 1.0-exempel kan du se [kod exempel för Azure AD (v 1.0-slutpunkt)](sample-v1-code.md).

Information om det grundläggande scenariot för varje exempel typ finns i [app types för Microsoft Identity Platform](v2-app-types.md)-slutpunkten.

Du kan också bidra till exemplen på GitHub. Mer information finns i [Microsoft Azure Active Directory exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Program med en enda sida

De här exemplen visar hur du skriver ett program med en enda sida som skyddas med Microsoft Identity Platform. I de här exemplen används en av varianter för MSAL. js.

| Plattform | Beskrivning | Länka |
| -------- | --------------------- | -------- |
| ![Den här bilden visar JavaScript-](media/sample-v2-code/logo_js.png) filens [JavaScript-skript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anrop Microsoft Graph |[javascript-graphapi-web-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Den här bilden visar JavaScript-](media/sample-v2-code/logo_js.png) filens [JavaScript-skript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Den här bilden visar JavaScript-](media/sample-v2-code/logo_js.png) filens [JavaScript-skript (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar egen webb-API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Den här bilden visar](media/sample-v2-code/logo_angular.png) JavaScript- [Java Script (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs) för vinkeln js| Anrop Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs/samples/MsalAngularjsDemoApp)
| ![I den här bilden visas ett](media/sample-v2-code/logo_angular.png) [JavaScript-skript (MSAL-vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Anrop Microsoft Graph  | [MSALAngularDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular/samples/MSALAngularDemoApp) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webb program som loggar in användare. Några exempel visar också programmet som anropar Microsoft Graph eller ditt eget webb-API med användarens identitet.

| Plattform | Endast tecken i användare | Loggar in användare och anropar Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Själv studie kurs om ASP.NET Core WebApp-loggar – användare](https://aka.ms/aspnetcore-webapp-sign-in) | Samma exempel i [ASP.net Core webbappens anrop Microsoft Graph-](https://aka.ms/aspnetcore-webapp-call-msgraph) fasen |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Snabb start för ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp): en MSAL4J-webbapp som anropar Microsoft Graph |
| ![Den här bilden visar Node. js-logotypen](media/sample-v2-code/logo_nodejs.png)  |                   | [Snabb start för Node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Den här bilden visar ruby-logotypen](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop och mobila offentliga klient program

I följande exempel visas offentliga klient program (Station ära eller mobila program) som har åtkomst till Microsoft Graph-API: et eller ditt eget webb-API i namnet på en användare. Alla dessa klient program använder Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph | Anropar ett webb-API för ASP.NET Core 2,0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![Den här bilden visar .NET/C# logo typen](media/sample-v2-code/logo_NET.png) | [interaktiv](msal-authentication-flows.md#interactive)| [dotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Skriv bord (konsol)   | ![Den här bilden visar .NET/C# (Desktop)-logo typen](media/sample-v2-code/logo_NET.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Skriv bord (konsol)   | ![Den här bilden visar .NET/C# (Desktop)-logo typen](media/sample-v2-code/logo_NETcore.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C# (Xamarin)-logo typen](media/sample-v2-code/logo_xamarin.png) | [interaktiv](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Den här bilden visar iOS/mål-C eller Swift](media/sample-v2-code/logo_iOS.png) | [interaktiv](msal-authentication-flows.md#interactive) |[iOS – Swift-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [interaktiv](msal-authentication-flows.md#interactive) |[macOS-Swift-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [interaktiv](msal-authentication-flows.md#interactive) |  [android-native-v2](https://github.com/azure-samples/active-directory-android-native-v2 ) |  |

## <a name="daemon-applications"></a>Daemon-program

I följande exempel visas ett program som har åtkomst till Microsoft Graph-API med en egen identitet (utan användare).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsolen | ![Den här bilden visar .NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webbapp | ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |

## <a name="headless-applications"></a>Konsol löst program

I följande exempel visas ett offentligt klient program som körs på en enhet utan en webbläsare. Appen kan vara ett kommando rads verktyg, en app som körs på Linux eller Mac eller ett IoT-program. Exemplet innehåller en app som använder Microsoft Graph API, i namnet på en användare som loggar in interaktivt på en annan enhet (till exempel en mobil telefon). Det här klient programmet använder Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Skriv bord (konsol)   | ![Den här bilden visar .NET/C# (Desktop)-logo typen](media/sample-v2-code/logo_NETcore.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |

## <a name="web-apis"></a>Webb-API:er

Följande exempel visar hur du skyddar ett webb-API med slut punkten för Microsoft Identity Platform och hur du anropar ett underordnat API från webb-API: et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core Web API (Service) för [dotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webb-API (tjänst) för [MS-Identity-ASPNET-WebAPI-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph exempel

Mer information om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och självstudier som demonstrerar olika användnings mönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel & självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

- [Guide för Azure Active Directory (v 1.0)-utvecklare](v1-overview.md)
- [Koncept och referens för Azure AD-Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx)
- [Hjälp program bibliotek för Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
