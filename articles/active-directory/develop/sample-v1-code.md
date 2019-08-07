---
title: Kod exempel för Azure Active Directory v 1.0 | Microsoft Docs
description: Innehåller ett index för kod exempel för Azure Active Directory (v 1.0-slutpunkt) som organiseras efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bc671080a05e2c4e27fabcbab5cc40b9717dc85
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834891"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory kod exempel (v 1.0-slut punkt)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Du kan använda Microsoft Azure Active Directory (Azure AD) för att lägga till autentisering och auktorisering för dina webb program och webb-API: er.

Det här avsnittet innehåller länkar till exempel som du kan använda för att lära dig mer om Azure AD v 1.0-slutpunkten. De här exemplen visar hur det sker tillsammans med kodfragment som du kan använda i dina program. På sidan kod exempel hittar du detaljerade Read-Me-avsnitt som hjälper dig med krav, installation och konfiguration. Och koden kommenteras för att hjälpa dig att förstå de kritiska avsnitten.

> [!NOTE]
> Om du är intresse rad av Azure AD v2-kod exempel, se [v 2.0 kod exempel efter scenario](sample-v2-code.md).

Information om de grundläggande scenarierna för varje typ av exempel finns i [autentiserings scenarier för Azure AD](authentication-scenarios.md).

Du kan också bidra till våra exempel på GitHub. Mer information finns i [Microsoft Azure Active Directory exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Program med en enda sida

Det här exemplet visar hur du skriver ett program med en enda sida som skyddas med Azure AD.

 Plattform | Anropar sitt eget API | Anropar ett annat webb-API
 -------- |  --------------------- | ------------------ 
![Den här bilden visar JavaScript-logotypen](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Den här bilden visar en vinkel JS-logotyp](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webbprogram

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webb program som loggar in användare, anropar Microsoft Graph eller ett webb-API med användarens identitet

I följande exempel visas webb program som signerar användare. Några av dessa program anropar också Microsoft Graph eller ditt eget webb-API i namnet på den inloggade användaren.

 Plattform | Endast tecken i användare | Anrop Microsoft Graph eller AAD-Graf| Anropar en annan ASP.NET eller ASP.NET Core 2,0 webb-API
 -------- | ------------------- | --------------------- | -------------------------
![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD-diagram) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [webApp-openidconnect-dotnet](quickstart-v1-aspnet-webapp.md) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD-diagram) |
![Den här bilden visar python-logotypen](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Den här bilden visar Java-loggen](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Den här bilden visar PHP-logotypen](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webb program som demonstrerar rollbaserad åtkomst kontroll (auktorisering)

Följande exempel visar hur du implementerar rollbaserad åtkomst kontroll (RBAC). RBAC används för att begränsa behörigheterna för vissa funktioner i ett webb program till vissa användare. Användarna är auktoriserade beroende på om de tillhör en **Azure AD-grupp** eller har en specifik program **roll**.

Plattform | Exempel |
 -------- | ------------------- |
![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | En .NET 4,5 MVC-webbapp som använder Azure AD- **roller** för auktorisering

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Station ära och mobila klient program som anropar Microsoft Graph eller ett webb-API

Följande exempel illustrerar offentliga klient program (deskto/pmobile-program) som har åtkomst till Microsoft Graph eller ett webb-API i namnet på en användare. Beroende på enheterna och plattformarna kan program logga in användare på olika sätt (flöden/bidrag):

- Interaktivt
- Tyst (med integrerad Windows-autentisering på Windows, eller användar namn/lösen ord)
- Genom att delegera interaktiv inloggning till en annan enhet (enhets kod flöde som används på enheter som inte tillhandahåller webb kontroller)

Klient program | Plattform | Flöde/tilldelning | Anrop Microsoft Graph | Anropar ett ASP.NET-eller ASP.NET Core 2. x-webb-API
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![Den här bilden visar .NET/C# logo typen](media/sample-v2-code/logo_NET.png)  | Interaktiv | Del av [dotNet-internt-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet – inbyggd – skriv bord](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobil (UWP)            | .![Den här bilden visar .NET/C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interaktiv | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Det här exemplet använder [WAM](/windows/uwp/security/web-account-manager), inte [ADAL.net](https://aka.ms/adalnet)|  [dotNet-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-program som använder ADAL.NET för att anropa en enskild klient webb-API) </p> [dotNet-WebAPI-multiinnehavare-Windows-Store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-program som använder ADAL.NET för att anropa en webb-API för flera innehavare)|
Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktiv | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Den här bilden visar Android-logotypen](media/sample-v2-code/logo_Android.png) | Interaktiv |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobil (iOS)           | ![Den här bilden visar iOS/mål C eller Swift](media/sample-v2-code/logo_iOS.png) | Interaktiv |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Skriv bord (konsol)          | ![Den här bilden visar .NET/C# logo typen](media/sample-v2-code/logo_NET.png) | Användar namn/lösen ord </p>  Integrerad Windows-autentisering | | [dotNet-automatisk-konsol](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Skriv bord (konsol)          | ![Den här bilden visar Java-logotypen](media/sample-v2-code/logo_Java.png) | Användar namn/lösen ord | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Skriv bord (konsol)           | ![Den här bilden visar .NET Core/C# logo typ](media/sample-v2-code/logo_NETcore.png) | Enhetskodflöde | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-program (åtkomst till webb-API: er med programmets identitet)

I följande exempel visas Skriv bords-eller webb program som har åtkomst till Microsoft Graph eller ett webb-API utan användare (med program identiteten).

Klient program | Plattform | Flöde/tilldelning | Anropar en ASP.NET-eller ASP.NET Core 2,0-webb-API
------------------ | -------- | ---------- | -------------------- 
Daemon-app (konsol)          | ![Den här bilden visar .NET-logo typen](media/sample-v2-code/logo_NETframework.png) | Klientautentiseringsuppgifter med appens hemlighet eller certifikat | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon-app (konsol)         | ![Den här bilden visar .NET-logo typen](media/sample-v2-code/logo_NETcore.png) | Klientautentiseringsuppgifter med certifikat| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-webbapp  | ![Den här bilden visar .NET-logo typen](media/sample-v2-code/logo_NETframework.png) | Klientautentiseringsuppgifter | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-active-directory"></a>Webb-API som skyddas av Azure Active Directory

Följande exempel visar hur du skyddar ett Node. js-webb-API med Azure AD.

I föregående avsnitt i den här artikeln kan du också hitta andra exempel som illustrerar ett klient program som anropar ett ASP.net **-eller ASP.net Core-webb-API**. Dessa exempel nämns inte igen i det här avsnittet, men du hittar dem i den sista kolumnen i tabellerna ovan eller nedanför

| Plattform | Exempel |
|--------|-------------------|
| ![Den här bilden visar Node. js-logotypen](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API som anropar Microsoft Graph eller något annat webb-API

Följande exempel visar ett webb-API som anropar ett annat webb-API. Det andra exemplet visar hur du hanterar villkorlig åtkomst.

| Plattform |  Anrop Microsoft Graph | Anropar en annan ASP.NET eller ASP.NET Core 2,0 webb-API |
| -------- |  --------------------- | ------------------------- |
| ![Den här bilden visar ASP.NET-logotypen](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph exempel

Exempel och självstudier som demonstrerar olika användnings mönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph community-exempel & självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

[Guide för Azure Active Directory utvecklare](v1-overview.md)

[Azure Active Directory autentiseringsscheman](active-directory-authentication-libraries.md)

[Koncept och referens för Azure AD-Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Hjälp program bibliotek för Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
