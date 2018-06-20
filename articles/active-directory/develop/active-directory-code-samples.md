---
title: Azure Active Directory-kodexempel | Microsoft Docs
description: Innehåller ett index med Azure Active Directory (v1 slutpunkten) kodexempel, ordnade efter scenario.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5d7f0d1fc32f18991be6614bb7661b63570a8700
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264930"
---
# <a name="azure-active-directory-code-samples-v1-endpoint"></a>Kodexempel för Azure Active Directory (V1 slutpunkten)

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Du kan använda Microsoft Azure Active Directory (Azure AD) för att lägga till autentisering och auktorisering i ditt webbprogram och webb-API: er.

Det här avsnittet innehåller länkar till exempel som du kan använda för att lära dig mer om Azure AD V1-slutpunkten. De här exemplen visar hur du gör tillsammans med kodstycken som du kan använda i dina program. På sidan kod exempel hittar du detaljerad Läs-mig avsnitt som hjälper till med krav, installation och konfiguration. Och koden har kommenterats som hjälper dig att förstå viktiga avsnitt.

> [!NOTE]
> Om du är intresserad av i Azure AD-V2-kodexempel [v2.0 kodexempel av scenariot](active-directory-v2-code-samples.md).

Enkelt scenario för varje typ av exemplet finns [autentiseringsscenarier för Azure AD](active-directory-authentication-scenarios.md).

Du kan även bidra till våra exempel på GitHub. Mer information finns i avsnittet [Microsoft Azure Active Directory-exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Stationär dator och mobil offentliga klientprogram anropar Microsoft Graph eller ett webb-API

Följande exempel visar offentliga klienten program (desktop/mobila program) som har åtkomst till Microsoft Graph eller ett webb-API för namnet på en användare.

klientprogram | Plattform | Flödet/bevilja | Anrop Microsoft Graph | En ASP.NET eller ASP.NET Core 2.0 Web API-anrop
------------------ | -------- | ---------- | -------------------- | -------------------------
Fjärrskrivbord (WPF)           | .NET / C# | Interaktiv | [DotNet-intern-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [DotNet intern skrivbordet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [DotNet-intern-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [DotNet-webapi-manuell-jwt-validering](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Mobile (UWP)            | .NET / C#  | Interaktiv | [DotNet-intern-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  [DotNet windows store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (enstaka klient webb-API) </p> [DotNet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (flera innehavare webb-API)|
Mobil (Android, iOS, UWP)   | .NET / C# (Xamarin) | Interaktiv | [DotNet-intern-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobil (Android)           | Android/Java | Interaktiv |   [Android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | iOS/Objective C | Interaktiv |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Fjärrskrivbord (konsol)          | .NET / C# | Användarnamn / lösenord </p> Windows-integrerad autentisering | | [DotNet-intern-fjärradministrerade](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Fjärrskrivbord (konsol)           | .NET core / C# | Profil för enheter | | [DotNet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="web-applications"></a>Webbprogram

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Webbprogram logga in användare, anropar Microsoft Graph eller ett webb-API med användarens identitet

 Plattform | Endast loggar in användare | Anrop Microsoft Graph- eller AAD-diagram| En annan ASP.NET eller ASP.NET Core 2.0 Web API-anrop
 -------- | ------------------- | --------------------- | -------------------------
ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) </p> [WebApp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) | [DotNet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) (AAD diagram) |
ASP.NET Core 2.0 | [DotNet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [WebApp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) (AAD diagram) | [DotNet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
ASP.NET 4.5 | [DotNet-webapp-webapi-oauth2-användaridentiteten](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | |
Python | | [Python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
Java | | [Java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
PHP | | [PHP-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Webbprogram som visar rollbaserad åtkomstkontroll (behörighet)

Följande exempel visar hur du Implementera rollbaserad åtkomstkontroll som används för att begränsa behörigheterna för vissa funktioner i ett webbprogram till vissa användare. Användarna har behörighet beroende på om de hör till en Azure AD-grupp eller en roll.

Plattform | Exempel | Beskrivning
 -------- | ------------------- | ---------------------
ASP.NET 4.5 | [DotNet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) | En .NET 4.5 MVC-webbapp som använder Azure AD **grupper** för auktorisering
ASP.NET 4.5 | [DotNet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | En .NET 4.5 MVC-webbapp som använder Azure AD **roller** för auktorisering

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Daemon för program (åtkomst till webb-API: er med programmets identitet)

Följande exempel visar skrivbordet eller web program som har åtkomst till Microsoft Graph eller ett webb-API med ingen användare (med Programidentitet).

klientprogram | Plattform | Flödet/bevilja | Anrop Microsoft Graph | En ASP.NET eller ASP.NET Core 2.0 Web API-anrop
------------------ | -------- | ---------- | -------------------- | -------------------------
Daemon app (konsol)          | .NET / C#  | Klientens autentiseringsuppgifter med app hemlighet eller certifikat | | [daemon för DotNet](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [DotNet-daemon-certifikat-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Daemon app (konsol)         | .NET core / C# | Klientens autentiseringsuppgifter med certifikat| | [dotnetcore-daemon-certifikat-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Skrivbord            | Java | Klientautentiseringsuppgifter |   [Java-intern-fjärradministrerade](https://github.com/azure-samples/active-directory-java-native-headless) |
ASP.NET-webbprogram  | .NET / C# | Klientautentiseringsuppgifter |    | [DotNet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Webb-API:er

### <a name="web-api-protected-by-azure-active-directory"></a>Webb-API som skyddas av Azure Active Directory

I följande exempel visas hur du skyddar ett node.js-webb-API med Azure AD.

Plattform | Exempel | Beskrivning
 -------- | ------------------- | ---------------------
Node.js | [nod-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |  NodeJS webb-API som skyddas med Azure AD och OAuth 2.0-åtkomsttoken.

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Webb-API som anropar Microsoft Graph eller en annan webb-API

Följande exempel visar ett webb-API som anropar en annan webb-API. Det andra exemplet visar hur du hanterar villkorlig åtkomst.

 Plattform |  Anrop Microsoft Graph | En annan ASP.NET eller ASP.NET Core 2.0 Web API-anrop
 -------- |  --------------------- | -------------------------
ASP.NET 4.5 | [DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) |[DotNet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof)
ASP.NET 4.5 | [DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[DotNet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="single-page-applications"></a>Sida program

Det här exemplet visas hur du skriver en enstaka sida program som skyddas med Azure AD.

 Plattform |  Anrop Microsoft Graph | Egna API-anrop | En annan Web API-anrop
 -------- |  --------------------- | ------------------ | ----------------
JavaScript / ASP.NET 4.x |  | [JavaScript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
JavaScript (AngularJS) / ASP.NET 4.x |  | [angularjs singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |
JavaScript (AngularJS) / ASP.NET 4.x |  |  | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="other-microsoft-graph-samples"></a>Andra Microsoft Graph-exempel

Exempel och självstudier som visar olika användningsmönster för Microsoft Graph API, inklusive autentisering med Azure AD finns i [Microsoft Graph Community exempel och självstudier](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Se också

[Azure Active Directory-Guide för utvecklare](active-directory-developers-guide.md)

[Azure AD Graph API konceptuell och referenser](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API hjälpbibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
