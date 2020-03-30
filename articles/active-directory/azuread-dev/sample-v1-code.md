---
title: Kodexempel för Azure Active Directory v1.0 | Microsoft-dokument
description: Innehåller ett index över Azure Active Directory-kodexempel (v1.0- ändpunkt) som är ordnade efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ae283529abb3b71ee50fc710dd1ebe0d17a12be0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154839"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory-kodexempel (v1.0-slutpunkt)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Du kan använda Microsoft Azure Active Directory (Azure AD) för att lägga till autentisering och auktorisering i webbprogram och webb-API:er.

Det här avsnittet innehåller länkar till exempel som du kan använda för att lära dig mer om slutpunkten för Azure AD v1.0. De här exemplen visar hur det görs tillsammans med kodavsnitt som du kan använda i dina program. På exempelsidan för kod hittar du detaljerade läs-me-ämnen som hjälper till med krav, installation och installation. Och koden kommenteras för att hjälpa dig att förstå de kritiska avsnitten.

> [!NOTE]
> Om du är intresserad av Azure AD V2-kodexempel läser du [v2.0-kodexempel efter scenario](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Information om vilket grundläggande scenariot för varje exempeltyp finns i [Autentiseringsscenarier för Azure AD](v1-authentication-scenarios.md).

Du kan också bidra till våra prover på GitHub. Mer information finns i [Exempel och dokumentation för Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Ensidiga program

Det här exemplet visar hur du skriver ett ensidigt program som är skyddat med Azure AD.

 Plattform | Anropar sitt eget API | Anropar ett annat webb-API
 -------- |  --------------------- | ------------------ 
![Den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Den här bilden visar Angular JS-logotypen](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webbprogram

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webbprogram loggar in användare, anropar Microsoft Graph eller ett webb-API med användarens identitet

Följande exempel illustrerar webbprogram signering användare. Vissa av dessa program anropar även Microsoft Graph eller ditt eget webb-API i den inloggade användarens namn.

 Plattform | Endast tecken hos användare | Anropar Microsoft Graph | Anropar ett annat ASP.NET eller ASP.NET Core 2.0-webb-API
 -------- | ------------------- | --------------------- | -------------------------
![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Kärna 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD-graf) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 |  </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD-graf) |
![Den här bilden visar Python-logotypen](media/sample-v2-code/logo-python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Den här bilden visar Java-loggen](media/sample-v2-code/logo-java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Den här bilden visar PHP-logotypen](media/sample-v2-code/logo-php.png) | | [php-graphapi-webben](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webbprogram som demonstrerar rollbaserad åtkomstkontroll (auktorisering)

Följande exempel visar hur du implementerar rollbaserad åtkomstkontroll (RBAC). RBAC används för att begränsa behörigheterna för vissa funktioner i ett webbprogram till vissa användare. Användarna är auktoriserade beroende på om de tillhör en **Azure AD-grupp** eller har en viss **programroll**.

Plattform | Exempel |
 -------- | ------------------- |
![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-rollclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | En .NET 4.5 MVC-webbapp som använder Azure **AD-roller** för auktorisering

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Offentliga klientprogram för stationära och mobila datorer som anropar Microsoft Graph eller ett webb-API

Följande exempel illustrerar offentliga klientprogram (deskto/pmobile-program) som kommer åt Microsoft Graph eller ett webb-API i en användares namn. Beroende på enheter och plattformar kan program logga in användare på olika sätt (flöden/bidrag):

- Interaktivt
- Tyst (med integrerad Windows-autentisering i Windows eller användarnamn/lösenord)
- Genom att delegera den interaktiva inloggningen till en annan enhet (enhetskodflöde som används på enheter som inte tillhandahåller webbkontroller)

Klientprogram | Plattform | Flöde/bidrag | Anropar Microsoft Graph | Anropar ett ASP.NET eller ASP.NET Core 2.x-webb-API
------------------ | -------- | ---------- | -------------------- | -------------------------
Stationär dator (WPF)           | ![Den här bilden visar .NET/C#-logotypen](media/sample-v2-code/logo-net.png)  | Interaktiv | Ingår [i dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manuell-jwt-validering](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobil (UWP)            | .![Den här bilden visar .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interaktiv | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> I det här exemplet används [WAM](/windows/uwp/security/web-account-manager) [, inte ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-program som använder ADAL.NET för att anropa ett enda klientwebb-API) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-program med ADAL.NET för att anropa ett webb-API för flera innehavare)|
Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interaktiv | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo-android.png) | Interaktiv |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobil (iOS)           | ![Den här bilden visar iOS / Mål C eller Swift](media/sample-v2-code/logo-ios.png) | Interaktiv |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Stationär dator (konsol)          | ![Den här bilden visar .NET/C#-logotypen](media/sample-v2-code/logo-net.png) | Användarnamn / Lösenord </p>  Integrerad Windows-autentisering | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Stationär dator (konsol)          | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo-java.png) | Användarnamn / Lösenord | | [java-native-huvudlös](https://github.com/Azure-Samples/active-directory-java-native-headless)
Stationär dator (konsol)           | ![Den här bilden visar .NET Core/C#-logotypen](media/sample-v2-code/logo-netcore.png) | Flöde av enhetskod | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-program (åtkomst till webb-API:er med programmets identitet)

Följande exempel visar skrivbords- eller webbprogram som har åtkomst till Microsoft Graph eller ett webb-API utan användare (med programidentiteten).

Klientprogram | Plattform | Flöde/bidrag | Anropar ett ASP.NET eller ASP.NET Core 2.0-webb-API
------------------ | -------- | ---------- | -------------------- 
Appen Daemon (konsol)          | ![Den här bilden visar .NET-logotypen](media/sample-v2-code/logo-netframework.png) | Klientautentiseringsuppgifter med apphemlighet eller certifikat | [dotnet-demon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certifikat-autentiseringsuppgifter](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Appen Daemon (konsol)         | ![Den här bilden visar .NET-logotypen](media/sample-v2-code/logo-netcore.png) | Klientautentiseringsuppgifter med certifikat| [dotnetcore-daemon-certifikat-autentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET webbapp  | ![Den här bilden visar .NET-logotypen](media/sample-v2-code/logo-netframework.png) | Klientautentiseringsuppgifter | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-active-directory"></a>Webb-API skyddas av Azure Active Directory

Följande exempel visar hur du skyddar ett node.js webb-API med Azure AD.

I föregående avsnitt i den här artikeln kan du också hitta andra exempel som illustrerar ett klientprogram **som anropar** ett ASP.NET eller ASP.NET Core **Web API**. Dessa prover nämns inte igen i det här avsnittet, men du hittar dem i den sista kolumnen i tabellerna ovan eller nedan

| Plattform | Exempel |
|--------|-------------------|
| ![Den här bilden visar nod.js-logotypen](media/sample-v2-code/logo-nodejs.png)  | [nod-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API anropar Microsoft Graph eller ett annat webb-API

Följande exempel visar ett webb-API som anropar ett annat webb-API. Det andra exemplet visar hur du hanterar villkorlig åtkomst.

| Plattform |  Anropar Microsoft Graph | Anropar ett annat ASP.NET eller ASP.NET Core 2.0-webb-API |
| -------- |  --------------------- | ------------------------- |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Andra Exempel på Microsoft Graph

Exempel och självstudier som visar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD, finns i [Microsoft Graph Community Samples & Tutorials](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se även

- [Guide för Azure Active Directory-utvecklare](v1-overview.md)
- [Azure Active Directory-autentiseringsbibliotek](active-directory-authentication-libraries.md)
- [Konceptuell och referens för Microsoft Graph API](https://docs.microsoft.com/graph/use-the-api)
