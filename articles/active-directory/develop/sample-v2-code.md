---
title: Kodexempel för Microsofts identitetsplattform
description: Innehåller ett index över tillgängliga kodexempel för Microsoft identity platform (v2.0-slutpunkt), ordnade efter scenario.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 15578b6adc19eb3513e0d7e1d3d2c400c9170250
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535986"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Kodexempel för Microsoft-identitetsplattform (v2.0-slutpunkt)

Du kan använda Microsofts identitetsplattform för att:

- Lägg till autentisering och auktorisering i webbprogram och webb-API:er.
- Kräv en åtkomsttoken för att komma åt ett skyddat webb-API.

I den här artikeln beskrivs och ges länkar till exempel för slutpunkten för Microsoft-identitetsplattformen. Dessa exempel visar hur det görs och innehåller även kodavsnitt som du kan använda i dina program. På exempelsidan för kod hittar du detaljerade läsavsnitt som hjälper till med krav, installation och installation. Kommentarer i koden hjälper dig att förstå de kritiska avsnitten.

> [!NOTE]
> Om du är intresserad av v1.0-exempel läser du [Azure AD-kodexempel (v1.0-slutpunkt).](../azuread-dev/sample-v1-code.md)

Information om vilket grundläggande scenariot för varje exempeltyp finns [i Apptyper för slutpunkten för Microsoft identity-plattformen](v2-app-types.md).

Du kan också bidra till exemplen på GitHub. Mer information finns i [Exempel och dokumentation för Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Ensidiga program

Dessa exempel visar hur du skriver ett ensidigt program som är skyddat med Microsofts identitetsplattform. Dessa prover använder en av smakerna av MSAL.js.

| Plattform | Beskrivning | Länk |
| -------- | --------------------- | -------- |
| ![Den här bilden visar](media/sample-v2-code/logo_js.png) JavaScript-logotypen [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Den här bilden visar](media/sample-v2-code/logo_js.png) JavaScript-logotypen [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Samtal B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Den här bilden visar](media/sample-v2-code/logo_js.png) JavaScript-logotypen [JavaScript (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Anropar eget webb-API |[javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |
| ![Den här bilden visar](media/sample-v2-code/logo_angular.png) Den kantiga logotypen [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| Anropar Microsoft Graph  | [active-directory-javascript-singlepageapp-kantiga](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Den här bilden visar](media/sample-v2-code/logo_angular.png) Den kantiga logotypen [JavaScript (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Samtal B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |

## <a name="web-applications"></a>Webbprogram

Följande exempel illustrerar webbprogram som loggar in användare. Vissa exempel visar också programmet som anropar Microsoft Graph, eller ditt eget webb-API med användarens identitet.

| Plattform | Endast tecken hos användare | Tecken i användare och samtal Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Den här bilden visar ASP.NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Kärna 2.2 | [ASP.NET Core WebApp loggar in användare handledning](https://aka.ms/aspnetcore-webapp-sign-in) | Samma exempel i [ASP.NET Core-webbappen anropar Microsoft Graph-fasen](https://aka.ms/aspnetcore-webapp-call-msgraph) |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET snabbstart](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-utbildning-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png)  |                   | [ms-identitet-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Den här bilden visar Python-logotypen](media/sample-v2-code/logo_python.png)  |                   | [ms-identitet-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Den här bilden visar Ruby-logotypen](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-utbildning-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Offentliga klientappar för stationära och mobila datorer

Följande exempel visar offentliga klientprogram (stationära eller mobila program) som har åtkomst till Microsoft Graph API eller ditt eget webb-API i en användares namn. Alla dessa klientprogram använder Microsoft Authentication Library (MSAL).

| Klientprogram | Plattform | Flöde/bidrag | Anropar Microsoft Graph | Anropar ett webb-API för ASP.NET Core 2.0 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Stationär dator (WPF)      | ![Den här bilden visar .NET/C#-logotypen](media/sample-v2-code/logo_NET.png) | [interaktiv](msal-authentication-flows.md#interactive)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Stationär dator (konsol)   | ![Den här bilden visar .NET/C# (Desktop) -logotypen](media/sample-v2-code/logo_NET.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Stationär dator (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Integrerad Windows-autentisering](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Stationär dator (konsol)   | ![Den här bilden visar .NET/C# (Desktop) -logotypen](media/sample-v2-code/logo_NETcore.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-upp-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Stationär dator (konsol) med WAM  | ![Den här bilden visar .NET/C# (Desktop) -logotypen](media/sample-v2-code/logo_NETcore.png) | [interaktiv med WAM](msal-authentication-flows.md#interactive) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Stationär dator (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Stationär dator (konsol)   | ![Den här bilden visar Python-logotypen](media/sample-v2-code/logo_python.png) | [Användarnamn/lösenord](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C# (Xamarin) logotyp](media/sample-v2-code/logo_xamarin.png) | [interaktiv](msal-authentication-flows.md#interactive) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![Den här bilden visar iOS/Objective-C eller Swift](media/sample-v2-code/logo_iOS.png) | [interaktiv](msal-authentication-flows.md#interactive) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Stationär dator (macOS)       | macOS | [interaktiv](msal-authentication-flows.md#interactive) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobil (Android-Java)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [interaktiv](msal-authentication-flows.md#interactive) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android-Kotlin)   | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | [interaktiv](msal-authentication-flows.md#interactive) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon applikationer

Följande exempel visar ett program som har åtkomst till Microsoft Graph API med sin egen identitet (utan användare).

| Klientprogram | Plattform | Flöde/bidrag | Anropar Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Konsol | ![Den här bilden visar .NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webbapp | ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Konsol | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [ms-identitet-java-demon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Konsol | ![Den här bilden visar Python-logotypen](media/sample-v2-code/logo_python.png) | [Klientautentiseringsuppgifter](msal-authentication-flows.md#client-credentials) | [ms-identitet-python-demon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Huvudlösa applikationer

Följande exempel visar ett offentligt klientprogram som körs på en enhet utan webbläsare. Appen kan vara ett kommandoradsverktyg, en app som körs på Linux eller Mac eller ett IoT-program. Exemplet innehåller en app som använder Microsoft Graph API, i namn av en användare som loggar in interaktivt på en annan enhet (till exempel en mobiltelefon). Det här klientprogrammet använder Microsoft Authentication Library (MSAL).

| Klientprogram | Plattform | Flöde/bidrag | Anropar Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Stationär dator (konsol)   | ![Den här bilden visar .NET/C# (Desktop) -logotypen](media/sample-v2-code/logo_NETcore.png) | [Flöde av enhetskod](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Stationär dator (konsol)   | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | [Flöde av enhetskod](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Stationär dator (konsol)   | ![Den här bilden visar Python-logotypen](media/sample-v2-code/logo_python.png) | [Flöde av enhetskod](msal-authentication-flows.md#device-code) |[ms-identitet-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Webb-API:er

Följande exempel visar hur du skyddar ett webb-API med slutpunkten för Microsoft identity-plattformen och hur du anropar ett nedströms-API från webb-API:et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Kärna 2.2 | ASP.NET Core web API (tjänst) av [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Webb-API (tjänst) av [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_java.png) | Webb-API (tjänst) av [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Den här bilden visar nod.js-logotypen](media/sample-v2-code/logo_nodejs.png) | Webb-API (tjänst) för [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Den här bilden visar nod.js-logotypen](media/sample-v2-code/logo_nodejs.png) | B2C Web API (tjänst) av [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Fungerar som webb-API:er

Följande exempel visar hur du skyddar en Azure-funktion med HttpTrigger och exponerar ett webb-API med slutpunkten för Microsoft-identitetsplattformen och hur du anropar ett nedströms-API från webb-API:et.

| Plattform | Exempel |
| -------- | ------------------- |
| ![Den här bilden visar ASP.NET Core-logotypen](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Kärna 2.2 | ASP.NET Core web API (tjänst) Azure-funktion av [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Den här bilden visar nod.js-logotypen](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webb-API (tjänst) för [NodeJS och pass-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Den här bilden visar Python-logotypen](media/sample-v2-code/logo_python.png)</p>Python | Webb-API (tjänst) av [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Den här bilden visar nod.js-logotypen](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | Webb-API (tjänst) för [NodeJS och pass-azure-ad som använder på uppdrag av](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Andra Exempel på Microsoft Graph

Mer information om [exempel](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) och självstudier som visar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD, finns i [Microsoft Graph Community-exempel & självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se även

- [Utvecklarguide för Azure Active Directory (v1.0)](../azuread-dev/v1-overview.md)
- [Konceptuell och referens för Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
