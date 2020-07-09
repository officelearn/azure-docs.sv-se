---
title: Kod exempel för Microsoft Identity Platform
description: Innehåller ett index över tillgängliga kod exempel för Microsoft Identity Platform (v 2.0-slut punkt), ordnat efter scenario.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 06/01/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 42b7cf88942fb9b54b1292ece18cf14eb0d21958
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165970"
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

De här exemplen visar hur du skriver ett program med en enda sida som skyddas med Microsoft Identity Platform. I de här exemplen används en varianter av MSAL.js.

| Plattform | Beskrivning | Länk |
| -------- | --------------------- | -------- |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-anrop Microsoft Graph |[Java Script-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-anrop Microsoft Graph att använda auth Code Flow med PKCE |[Java Script-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Den här bilden visar JavaScript-filens ](media/sample-v2-code/logo_js.png) [JavaScript-skript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-anrop B2C |[B2C – Java Script-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![I den här bilden visas ett ](media/sample-v2-code/logo_angular.png) [JavaScript-skript (MSAL-vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-anrop Microsoft Graph  | [Active-Directory-JavaScript-singlepageapp-vinkel](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![I den här bilden visas ett ](media/sample-v2-code/logo_angular.png) [JavaScript-skript (MSAL-vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-anrop anpassad webb-API | [MS-Identity-JavaScript-vinkel-Spa-aspnetcore-WebAPI](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![I den här bilden visas ett ](media/sample-v2-code/logo_angular.png) [JavaScript-skript (MSAL-vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA-anrop B2C |[Active-Directory-B2C-JavaScript-vinkel-Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Den här bilden visar JavaScript-filen med reagera på en logo typ ](media/sample-v2-code/logo_react.png) [(msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA anropar anpassat webb-API som i sin tur anropar Microsoft Graph  | [MS-Identity-JavaScript-reakta-Spa-dotnetcore-WebAPI-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![I den här bilden visas ett ](media/sample-v2-code/logo_angular.png) [JavaScript-skript (MSAL-vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA-anrop för flera innehavare med anpassat webb-API |[MS-Identity-JavaScript-vinkel-Spa-ASPNET-ASPNET-WebAPI-flera innehavare](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant) |
| ![I den här bilden visas ett ](media/sample-v2-code/logo_angular.png) [JavaScript-skript (MSAL-vinkel)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA anropar anpassat webb-API med app-roller och säkerhets grupper |[MS-Identity-JavaScript-vinkel-Spa-dotnetcore-WebAPI-roles-Groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webb program som loggar in användare. Några exempel visar också programmet som anropar Microsoft Graph eller ditt eget webb-API med användarens identitet.

| Plattform | Endast tecken i användare | Loggar in användare och anropar Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Själv studie kurs om ASP.NET Core WebApp-loggar – användare](https://aka.ms/aspnetcore-webapp-sign-in) | Samma exempel i [ASP.net Core webbappens anrop Microsoft Graph-](https://aka.ms/aspnetcore-webapp-call-msgraph) fasen |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Snabb start för ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotNet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotNet-admin-restricted-scope – v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph – utbildning – aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)  |                   | [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Den här bilden visar ruby-logotypen](media/sample-v2-code/logo_ruby.png) |                   | [msgraph – utbildning – rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop och mobila offentliga klient program

I följande exempel visas offentliga klient program (Station ära eller mobila program) som har åtkomst till Microsoft Graph-API: et eller ditt eget webb-API i namnet på en användare. Förutom *Skriv bordet (konsolen) med ett WAM* -exempel använder alla dessa klient program Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph | Anropar ett webb-API för ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Desktop (WPF)      | ![Den här bilden visar .NET/C#-logo typen](media/sample-v2-code/logo_NET.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code)| [dotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Skriv bord (konsol)   | ![Den här bilden visar logo typen .NET/C# (skriv bord)](media/sample-v2-code/logo_NET.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) | [dotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Skriv bord (konsol)   | ![Den här bilden visar logo typen .NET/C# (skriv bord)](media/sample-v2-code/logo_NETcore.png) | [Användar namn/lösen ord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Skriv bord (konsol) med WAM  | ![Den här bilden visar logo typen .NET/C# (skriv bord)](media/sample-v2-code/logo_NETcore.png) | Interaktiv med [Web Account Manager](/windows/uwp/security/web-account-manager) (WAM) |[dotNet-Native-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Användar namn/lösen ord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Skriv bord (konsol)   | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Användar namn/lösen ord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C#-logo typen (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |[Xamarin – inbyggt-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Den här bilden visar iOS/mål-C eller Swift](media/sample-v2-code/logo_iOS.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |[iOS – Swift-objc-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS – Native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |[macOS-Swift-objc-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobil (Android-Java)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android-Kotlin)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [Authorization code (Auktoriseringskod)](msal-authentication-flows.md#authorization-code) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-program

I följande exempel visas ett program som har åtkomst till Microsoft Graph-API med en egen identitet (utan användare).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsol | ![Den här bilden visar .NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webbapp | ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [Dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konsol | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konsol | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [MS-Identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Konsol löst program

I följande exempel visas ett offentligt klient program som körs på en enhet utan en webbläsare. Appen kan vara ett kommando rads verktyg, en app som körs på Linux eller Mac eller ett IoT-program. Exemplet innehåller en app som använder Microsoft Graph API, i namnet på en användare som loggar in interaktivt på en annan enhet (till exempel en mobil telefon). Det här klient programmet använder Microsoft Authentication Library (MSAL).

| Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Skriv bord (konsol)   | ![Den här bilden visar logo typen .NET/C# (skriv bord)](media/sample-v2-code/logo_NETcore.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Skriv bord (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Skriv bord (konsol)   | ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | [Enhets kod flöde](msal-authentication-flows.md#device-code) |[MS-Identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Webb-API:er

Följande exempel visar hur du skyddar ett webb-API med slut punkten för Microsoft Identity Platform och hur du anropar ett underordnat API från webb-API: et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web API (Service) för [dotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webb-API (tjänst) för [MS-Identity-ASPNET-WebAPI-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | Webb-API (Service) för [MS-Identity-Java-WebAPI](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png) | Webb-API (tjänst) för [Active-Directory-JavaScript-NodeJS-WebAPI-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png) | B2C Web API (Service) för [Active-Directory-B2C-JavaScript-NodeJS-WebAPI](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions som webb-API: er

Följande exempel visar hur du skyddar en Azure-funktion med HttpTrigger och exponerar ett webb-API med Microsoft Identity Platform-slutpunkten och hur du anropar ett underordnat API från webb-API: et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core logo typ](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web API (Service) Azure Function i [dotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webb-API (Service) för [NodeJS och Passport – Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png)</p>Python | Webb-API (Service) för [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Den här bilden visar Node.js logo typ](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webb-API (Service) för [NodeJS och Passport-Azure-AD som använder på uppdrag av](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph exempel

Mer information om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och självstudier som demonstrerar olika användnings mönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel & självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se även

- [Guide för Azure Active Directory (v 1.0)-utvecklare](../azuread-dev/v1-overview.md)
- [Microsoft Graph API-konceptuell och referens](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
