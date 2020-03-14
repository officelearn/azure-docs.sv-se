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
ms.openlocfilehash: a0ba46abcc6e3b837dc0b13422bdc3d714ed0022
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262690"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Kod exempel för Microsoft Identity Platform (v 2.0-slut punkt)

Du kan använda Microsoft Identity Platform för att:

- Lägg till autentisering och auktorisering för dina webb program och webb-API: er.
- Kräv en åtkomsttoken för att få åtkomst till ett skyddat webb-API.

Den här artikeln beskriver och tillhandahåller länkar till exempel för Microsoft Identity Platform-slutpunkten. De här exemplen visar hur det är färdigt och innehåller även kodfragment som du kan använda i dina program. På sidan kod exempel hittar du detaljerade README-avsnitt som hjälper dig med krav, installation och konfiguration. Kommentarer i koden hjälper dig att förstå de kritiska avsnitten.

> [!NOTE]
> Om du är intresse rad av v 1.0-exempel kan du se [kod exempel för Azure AD (v 1.0-slutpunkt)](../azuread-dev/sample-v1-code.md).

Information om det grundläggande scenariot för varje exempel typ finns i [app types för Microsoft Identity Platform-slutpunkten](v2-app-types.md).

Du kan också bidra till exemplen på GitHub. Mer information finns i [Microsoft Azure Active Directory exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Program med en enda sida

De här exemplen visar hur du skriver ett program med en enda sida som skyddas med Microsoft Identity Platform. I de här exemplen används en av varianter för MSAL. js.

| Plattform | Beskrivning | Länk |
| -------- | --------------------- | -------- |
| ![den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo_js.png) [Java Script (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anrop Microsoft Graph |[Java Script-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo_js.png) [Java Script (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar B2C |[B2C – Java Script-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo_js.png) [Java Script (msal. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar egen webb-API |[Java Script-singlepageapp-dotNet-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![den här bilden visar](media/sample-v2-code/logo_angular.png) [Java Script (MSAL AngularJS)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs) för vinkel JS-logotypen| Anrop Microsoft Graph  | [MsalAngularjsDemoApp](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/MsalAngularjsDemoApp)
| ![den här bilden visar vinkel logo typ](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL-vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Anrop Microsoft Graph  | [Java Script-singlepageapp-vinkel](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webb program som loggar in användare. Några exempel visar också programmet som anropar Microsoft Graph eller ditt eget webb-API med användarens identitet.

| Plattform | Endast tecken i användare | Loggar in användare och anropar Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | [Själv studie kurs om ASP.NET Core WebApp-loggar – användare](https://aka.ms/aspnetcore-webapp-sign-in) | Samma exempel i [ASP.net Core webbappens anrop Microsoft Graph-](https://aka.ms/aspnetcore-webapp-call-msgraph) fasen |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Snabb start för ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotNet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotNet-admin-restricted-scope – v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph – utbildning – aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  |                   | [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Den här bilden visar Node. js-logotypen](media/sample-v2-code/logo_nodejs.png)  |                   | [Snabb start för Node. js](https://github.com/azureadquickstarts/appmodelv2-webapp-openidconnect-nodejs) |
| ![Den här bilden visar ruby-logotypen](media/sample-v2-code/logo_ruby.png) |                   | [msgraph – utbildning – rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop och mobila offentliga klient program

I följande exempel visas offentliga klient program (Station ära eller mobila program) som har åtkomst till Microsoft Graph-API: et eller ditt eget webb-API i namnet på en användare. Alla dessa klient program använder Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph | Anropar ett webb-API för ASP.NET Core 2,0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![Den här bilden visar .NET/C# logo typen](media/sample-v2-code/logo_NET.png) | [interaktiv](msal-authentication-flows.md#interactive)| [dotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Skriv bord (konsol)   | ![Den här bilden visar .NET/C# (Desktop)-logo typen](media/sample-v2-code/logo_NET.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) | [dotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Skriv bord (konsol)   | ![Den här bilden visar .NET/C# (Desktop)-logo typen](media/sample-v2-code/logo_NETcore.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Skriv bord (konsol) med WAM  | ![Den här bilden visar .NET/C# (Desktop)-logo typen](media/sample-v2-code/logo_NETcore.png) | [interaktivt med WAM](msal-authentication-flows.md#interactive) |[dotNet-Native-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Skriv bord (konsol)   | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C# (Xamarin)-logo typen](media/sample-v2-code/logo_xamarin.png) | [interaktiv](msal-authentication-flows.md#interactive) |[Xamarin – inbyggt-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Den här bilden visar iOS/mål-C eller Swift](media/sample-v2-code/logo_iOS.png) | [interaktiv](msal-authentication-flows.md#interactive) |[iOS – Swift-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS – Native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [interaktiv](msal-authentication-flows.md#interactive) |[macOS-Swift-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobil (Android-Java)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [interaktiv](msal-authentication-flows.md#interactive) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android-Kotlin)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [interaktiv](msal-authentication-flows.md#interactive) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-program

I följande exempel visas ett program som har åtkomst till Microsoft Graph-API med en egen identitet (utan användare).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsolen | ![Den här bilden visar .NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webbapp | ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [Dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konsolen | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konsolen | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [MS-Identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Konsol löst program

I följande exempel visas ett offentligt klient program som körs på en enhet utan en webbläsare. Appen kan vara ett kommando rads verktyg, en app som körs på Linux eller Mac eller ett IoT-program. Exemplet innehåller en app som använder Microsoft Graph API, i namnet på en användare som loggar in interaktivt på en annan enhet (till exempel en mobil telefon). Det här klient programmet använder Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Skriv bord (konsol)   | ![Den här bilden visar .NET/C# (Desktop)-logo typen](media/sample-v2-code/logo_NETcore.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Skriv bord (konsol)   | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[MS-Identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Webb-API:er

Följande exempel visar hur du skyddar ett webb-API med slut punkten för Microsoft Identity Platform och hur du anropar ett underordnat API från webb-API: et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core Web API (Service) för [dotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webb-API (tjänst) för [MS-Identity-ASPNET-WebAPI-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | Webb-API (Service) för [MS-Identity-Java-WebAPI](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Den här bilden visar Node. js-logotypen](media/sample-v2-code/logo_nodejs.png) | Webb-API (tjänst) för [Active-Directory-JavaScript-NodeJS-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions som webb-API: er

Följande exempel visar hur du skyddar en Azure-funktion med HttpTrigger och exponerar ett webb-API med Microsoft Identity Platform-slutpunkten och hur du anropar ett underordnat API från webb-API: et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.2 | ASP.NET Core Web API (Service) Azure Function i [dotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Den här bilden visar Node. js-logotypen](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webb-API (Service) för [NodeJS och Passport – Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)</p>Python | Webb-API (Service) för [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Den här bilden visar Node. js-logotypen](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webb-API (Service) för [NodeJS och Passport-Azure-AD som använder på uppdrag av](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph exempel

Mer information om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och självstudier som demonstrerar olika användnings mönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel & självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se även

- [Guide för Azure Active Directory (v 1.0)-utvecklare](../azuread-dev/v1-overview.md)
- [Microsoft Graph API-konceptuell och referens](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
