---
title: Azure Active Directory v1.0-kodexempel | Microsoft Docs
description: Innehåller ett index i Azure Active Directory (v1.0 slutpunkt) kodexempel ordnas efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca03c8d52de1580b755833dd990d1d7671f372ca
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785699"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Kodexempel för Azure Active Directory (v1.0 slutpunkt)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Du kan använda Microsoft Azure Active Directory (Azure AD) för att lägga till autentisering och auktorisering till dina webbprogram och webb-API: er.

Det här avsnittet innehåller länkar till exempel som du kan använda för att lära dig mer om Azure AD v1.0-slutpunkten. De här exemplen visar hur man gör tillsammans med kodfragment som du kan använda i dina program. På sidan kod exemplet hittar du detaljerade Läs-mig avsnitt som hjälp med krav, installation och konfiguration. Och koden har kommenterats för att förstå viktiga avsnitt.

> [!NOTE]
> Om du är intresserad av kodexempel för Azure AD V2, se [v2.0-kodexempel efter scenario](sample-v2-code.md).

Information om grundläggande scenario för varje typ av exemplet finns i [autentiseringsscenarier för Azure AD](authentication-scenarios.md).

Du kan också bidra till vårt exempel på GitHub. Läs hur genom att läsa [Microsoft Azure Active Directory-exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Enkelsidigt program

Detta exempel visar hur du skriver ett enkelsidigt program som skyddas med Azure AD.

 Plattform | En egen API-anrop | Anropar en annan webb-API
 -------- |  --------------------- | ------------------ 
![Den här bilden visar JavaScript-logotyp](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Den här bilden visar Angular JS-logotyp](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Webbprogram

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webbprogram som loggar in användare, anropa Microsoft Graph eller ett webb-API med användarens identitet

Följande exempel illustrerar webbprogram som registrerar användare. Vissa av dessa program kan anropa Microsoft Graph eller dina egna webb-API, namnet på den inloggade användaren.

 Plattform | Endast loggar in användare | Anropar Microsoft Graph eller AAD Graph| En annan ASP.NET eller ASP.NET Core 2.0 Web API-anrop
 -------- | ------------------- | --------------------- | -------------------------
![Den här bilden visar ASP.NET-logotyp](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![Den här bilden visar ASP.NET-logotyp](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [webApp-openidconnect-dotnet](quickstart-v1-aspnet-webapp.md) </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
![Den här bilden visar Python-logotyp](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Den här avbildningen innehåller en Java-logg](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![Den här bilden visar PHP-logotyp](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webbprogram som demonstrerar rollbaserad åtkomstkontroll (auktorisering)

Följande exempel visar hur du implementerar rollbaserad åtkomstkontroll (RBAC). RBAC används för att begränsa behörigheterna för vissa funktioner i en webbapp till vissa användare. Användarna har behörighet beroende på om de hör till en **Azure AD-grupp** eller har ett visst program **rollen**.

Plattform | Exempel |
 -------- | ------------------- |
![Den här bilden visar ASP.NET-logotyp](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | En .NET 4.5 MVC-webbapp som använder Azure AD **roller** för auktorisering

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Stationära och mobila offentliga klientprogram anropar Microsoft Graph eller ett webb-API

Följande exempel illustrerar offentliga klientprogram (stationär dator/pmobile program) som har åtkomst till Microsoft Graph eller ett webb-API för namnet på en användare. Beroende på de enheter och plattformar logga program in användare på olika sätt (flöden/beviljar):

- Interaktivt
- Tyst (med integrerad Windows-autentisering på Windows eller användarnamn/lösenord)
- Genom att delegera de interaktiv inloggningen till en annan enhet (enheten kodflödet som används på enheter som inte ger webbkontroller)

Klientprogram | Plattform | Flow/bevilja | Anropar Microsoft Graph | Anropar en ASP.NET eller ASP.NET Core 2.x webb-API
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![Den här bilden visar .NET /C# logotyp](media/sample-v2-code/logo_NET.png)  | Interaktiv | En del av [dotnet-intern-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet-intern-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .![Den här bilden visar .NET /C#/UWP](media/sample-v2-code/logo_Windows.png)   | Interaktiv | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Det här exemplet används [WAM](/windows/uwp/security/web-account-manager), inte [ADAL.NET](https://aka.ms/adalnet)|  [DotNet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (UWP-program med hjälp av ADAL.NET för att anropa en enda klient webb-API) </p> [DotNet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (UWP-program med hjälp av ADAL.NET för att anropa en webb-API med flera innehavare)|
Mobil (Android, iOS, UWP)   | ![Den här bilden visar .NET /C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interaktiv | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Den här bilden visar Android-logotyp](media/sample-v2-code/logo_Android.png) | Interaktiv |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | ![Den här bilden visar iOS / Objective C eller Swift](media/sample-v2-code/logo_iOS.png) | Interaktiv |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Fjärrskrivbord (konsol)          | ![Den här bilden visar .NET /C# logotyp](media/sample-v2-code/logo_NET.png) | Användarnamn / lösenord </p>  Integrerad Windows-autentisering | | [DotNet-intern-fjärradministrerad](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Fjärrskrivbord (konsol)          | ![Den här bilden visar Java-logotyp](media/sample-v2-code/logo_Java.png) | Användarnamn / lösenord | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Fjärrskrivbord (konsol)           | ![Den här bilden visar .NET Core /C# logotyp](media/sample-v2-code/logo_NETcore.png) | Kodflöde för enhet | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon-program (åtkomst till webb-API: er med programmets identitet)

Följande exempel visar desktop eller web program som har åtkomst till Microsoft Graph eller ett webb-API utan användare (med Programidentitet).

Klientprogram | Plattform | Flow/bevilja | Ett ASP.NET eller ASP.NET Core 2.0 webb-API-anrop
------------------ | -------- | ---------- | -------------------- 
Daemon för app (konsol)          | ![Den här bilden visar .NET-logotyp](media/sample-v2-code/logo_NETframework.png) | Klientens autentiseringsuppgifter med apphemlighet eller certifikat | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon för app (konsol)         | ![Den här bilden visar .NET-logotyp](media/sample-v2-code/logo_NETcore.png) | Klientens autentiseringsuppgifter med certifikat| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET-Webbapp  | ![Den här bilden visar .NET-logotyp](media/sample-v2-code/logo_NETframework.png) | Klientautentiseringsuppgifter | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-active-directory"></a>Webb-API som skyddas av Azure Active Directory

I följande exempel visas hur du skyddar en node.js-webb-API med Azure AD.

I föregående avsnitt i den här artikeln kan du också hitta andra exempel som visar ett klientprogram **anropa** en ASP.NET eller ASP.NET Core **webb-API**. De här exemplen nämns inte igen i det här avsnittet, men du hittar dem i den sista kolumnen i tabellerna över eller under

| Plattform | Exempel |
|--------|-------------------|
| ![Den här bilden visar Node.js-logotyp](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API att anropa Microsoft Graph eller en annan webb-API

Följande exempel visar ett webb-API som anropar en annan webb-API. Det andra exemplet visar hur du hanterar villkorlig åtkomst.

| Plattform |  Anropar Microsoft Graph | En annan ASP.NET eller ASP.NET Core 2.0 Web API-anrop |
| -------- |  --------------------- | ------------------------- |
| ![Den här bilden visar ASP.NET-logotyp](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph-exempel

Exempel och självstudier som visar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community-exempel och självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

[Utvecklarhandbok för Azure Active Directory](v1-overview.md)

[Azure Active Directory-autentisering-bibliotek](active-directory-authentication-libraries.md)

[Azure AD Graph API konceptuell och referenser](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API hjälpbibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
