---
title: Azure Active Directory-kodexempel | Microsoft Docs
description: "Ett index över kodexempel för Azure Active Directory, ordnade efter scenario."
services: active-directory
documentationcenter: dev-center-name
author: msmbaldwin
manager: mtillman
editor: 
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: mbaldwin
ms.custom: aaddev
ms.openlocfilehash: 130d26828acd5394756c47f22217272338cc33e6
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="azure-active-directory-code-samples"></a>Azure Active Directory-kodexempel
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Du kan använda Microsoft Azure Active Directory (Azure AD) för att lägga till autentisering och auktorisering i ditt webbprogram och webb-API: er. Det här avsnittet länkar till exempel som visar hur du gör och kodstycken som du kan använda i dina program. På sidan kod exempel hittar du detaljerad Läs-mig avsnitt som hjälper till med krav, installation och konfiguration. Och koden har kommenterats som hjälper dig att förstå viktiga avsnitt.

Enkelt scenario för varje typ av exemplet finns Autentiseringsscenarier för Azure AD.

Bidra till våra exempel på GitHub: [Microsoft Azure Active Directory-exempel och dokumentation](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Webbläsare till webbprogram
De här exemplen visar hur du skriver ett webbprogram som leder användarens webbläsare för att logga in dem på Azure AD.

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| C#/.NET |[WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Använd OpenID Connect (ASP.Net OpenID Connect OWIN mellanprogram) för att autentisera användare från en Azure AD-klient. |
| C#/.NET |[WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |En flera innehavare .NET MVC-webbapp som använder OpenID Connect (ASP.Net OpenID Connect OWIN mellanprogram) för att autentisera användare från flera Azure AD-klienter. |
| C#/.NET |[WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) |Använda WS-Federation (ASP.Net WS-Federation OWIN mellanprogram) för att autentisera användare från en Azure AD-klient. |
| C# / .NET Core |[WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) |En .NET MVC-webbapp som använder OpenID Connect för att logga in användare från en enda Azure Active Directory (Azure AD)-klient med mellanprogram ASP.NET Core OpenID Connect. |

## <a name="single-page-application-spa"></a>Sida program (SPA)
Det här exemplet visas hur du skriver en enstaka sida program som skyddas med Azure AD.  

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| JavaScript, C#/.NET |[SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |Använd ADAL för JavaScript och Azure AD för att skydda en AngularJS-baserade sida app har implementerats med en ASP.NET web API-serverdel. |

## <a name="native-application-to-web-api"></a>Det ursprungliga programmet i webb-API
Följande kodexempel visar hur du bygga ursprunglig klientprogram som kan anropa webb-API: er som skyddas av Azure AD. De använder [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) och [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| Javascript |[NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) |Använda ADAL-plugin för Apache Cordova för att skapa en Apache Cordova-app som anropar ett webb-API och använder Azure AD för autentisering. |
| C#/.NET |[NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) |Ett .NET WPF-program som anropar ett webb-API som skyddas med hjälp av Azure AD. |
| C#/.NET |[NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Windows Store-programmet som anropar ett webb-API som skyddas med Azure AD. |
| C#/.NET |[NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) |Windows Store-programmet anropa ett webb-API som skyddas med Azure AD för flera innehavare. |
| C#/.NET |[WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) |En native client-program som anropar ett webb-API som hämtar en token för agerar på uppdrag av den ursprungliga användaren, och använder sedan token för att anropa en annan webb-API. |
| C#/.NET |[NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) |Windows Store-programmet för Windows Phone 8.1 som anropar ett webb-API som skyddas av Azure AD. |
| ObjC |[NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) |Ett iOS-program som anropar ett webb-API som kräver Azure AD för autentisering. |
| C#/.NET |[WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) |En native client-program som innehåller logik för att bearbeta en JWT-token i ett webb-API, istället för att använda OWIN mellanprogram. |
| C#/Xamarin |[NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) |En Xamarin-bindning till den interna Azure AD Authentication Library (ADAL) för Android-biblioteket. |
| C#/Xamarin |[NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) |En Xamarin bindning till den interna Azure AD Authentication Library (ADAL) för iOS. |
| C#/Xamarin |[NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) |En Xamarin-projektet som riktar sig till fem plattformar och anropar ett webb-API som skyddas av Azure AD. |
| C#/.NET |[NativeClient-Headless-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) |En intern program som utför icke-interaktiv autentisering och anropar ett webb-API som skyddas av Azure AD. |

## <a name="web-application-to-web-api"></a>Webbprogram i webb-API
Följande kodexempel visar hur du använder [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) att skapa webbprogram som anropet web API: er som skyddas av Azure AD.

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| C#/.NET |[WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) |Anropa ett webb-API med den inloggade användarens behörighet. |
| C#/.NET |[WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) |Anropa ett webb-API med programmets behörigheter. |
| C#/.NET |[WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) |Lägg till auktorisering med [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) till ett befintligt webbprogram så kan det anropa ett webb-API. |
| JavaScript |[WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) |Konfigurera en REST API-tjänst som är integrerad med Azure AD för API-skydd. Innehåller en Node.js-server med en webb-API. |
| C#/.NET |[WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |En flera innehavare MVC-webbprogram som använder OpenID Connect (ASP.Net OpenID Connect OWIN mellanprogram) för att autentisera användare från en Azure AD-klient. Använder en Auktoriseringskoden för att anropa Graph API. |

## <a name="server-or-daemon-application-to-web-api"></a>Servern eller Daemon programmet till webb-API
Följande kodexempel visar hur du skapar ett program för daemon eller server som hämtar resurser från ett webb-API med hjälp av [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) och [OAuth 2.0 i Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| C#/.NET |[Daemon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) |Ett konsolprogram anropar ett webb-API. Autentiseringsuppgifter för klienten är ett lösenord. |
| C#/.NET |[Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) |Ett konsolprogram som anropar ett webb-API. Autentiseringsuppgifter för klienten är ett certifikat. |

## <a name="calling-microsoft-graph-api"></a>Anropar Microsoft Graph API
Följande kodexempel visar hur du skapar program som anropar Microsoft Graph API för att läsa och skriva katalogdata.

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| C#/.NET |[WebApp-MSGraphAPI-DotNet](https://github.com/microsoftgraph/aspnet-snippets-sample) |Ett program som använder Microsoft Graph API för åtkomst till Azure AD directory-data. |
| C#/.NET |[UWPApp-MSGraphAPI-DotNet](https://github.com/microsoftgraph/uwp-csharp-snippets-sample) |Den här Uwp-appen visar hur du kan komma åt flera resurser, inklusive Microsoft Azure Active Directory (AD) och API: er för Office 365 genom att göra förfrågningar till Microsoft Graph API i en Windows 10-app. |

## <a name="calling-azure-ad-graph-api"></a>Anropar Azure AD Graph API
Följande kodexempel visar hur du skapar program som anropar Azure AD Graph-API för att läsa och skriva katalogdata.

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| Java |[WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) |Ett program som använder Graph API för åtkomst till Azure AD directory-data. |
| PHP |[WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |Ett program som använder Graph API för åtkomst till Azure AD directory-data. |
| C#/.NET |[ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) |Ett konsolprogram som använder differentiella frågan i Graph API för att hämta periodiska ändras till användarobjekt i en Azure AD-klient. |
| C#/.NET |[WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) |Ett MVC-program används Graph API-frågor för att skapa en enkel företagets Organisationsschema. |
| PHP |[WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) |Ett PHP-program som anropar Graph API för att registrera ett tillägg och sedan läsa, uppdatera och ta bort värden i attributet för anknytning. |

## <a name="authorization"></a>Auktorisering
Följande kodexempel visar hur du använder Azure AD för auktorisering.

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| C#/.NET |[WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) |Utföra rollbaserad åtkomstkontroll (RBAC) med hjälp av Azure Active Directory gruppanspråk i ett program som är integrerade med Azure AD. |
| C#/.NET |[WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) |Utföra rollbaserad åtkomstkontroll (RBAC) med hjälp av Azure Active Directory application roller i ett program som är integrerade med Azure AD. |

## <a name="legacy-walkthroughs"></a>Äldre genomgång
Dessa genomgång använda något äldre teknik, men kan fortfarande vara av intresse.

| Språk-plattformen | Exempel | Beskrivning |
| --- | --- | --- |
| C#/.NET |[Roll- och ACL-baserade auktorisering i en Microsoft Azure AD-program](http://go.microsoft.com/fwlink/?LinkId=331694) |Utför rollbaserad auktorisering (RBAC) och ACL-baserad auktorisering i ett program som är integrerade med Azure AD. |
| C#/.NET |[AAL - Windows Store-app till REST-tjänst - autentisering](http://go.microsoft.com/fwlink/?LinkId=330605) |Använd [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) (tidigare AAL) för Windows Store Beta att lägga till funktioner för autentisering av användare i en Windows Store-app. |
| C#/.NET |[ADAL - inbyggda appen för REST-tjänst - autentisering med AAD via dialogrutan](http://go.microsoft.com/fwlink/?LinkId=259814) |Använd [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) att lägga till funktioner för autentisering av användare i en WPF-klient. |
| C#/.NET |[ADAL - inbyggda appen för REST-tjänst - autentisering med ACS via dialogrutan](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |Använd [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) och [Access Control Service 2.0 (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) att lägga till funktioner för autentisering av användare i en WPF-klient. |
| C#/.NET |[ADAL - Server till Server-autentisering](http://go.microsoft.com/fwlink/?LinkId=259816) |Använd [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md) att skydda-anrop från en sida processer till en MVC4 Web API REST-tjänst. |
| C#/.NET |[Att lägga till inloggning till ditt webbprogram med hjälp av Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Konfigurera ett .NET-program för att utföra web enkel inloggning mot enterprise Azure AD-katalogen. |
| C#/.NET |[Utveckla webbprogram med flera innehavare med Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Använda Azure AD för att lägga till enkel inloggning och directory åtkomstfunktioner för en .NET-program ska fungera över flera organisationer. |
| JAVA |[Java-Sample-appen för Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkId=263969) |Använd Graph API för att komma åt directory data från Azure AD. |
| PHP |[PHP Exempelapp för Azure AD Graph API](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) |Använd Graph API för att komma åt directory data från Azure AD. |
| C#/.NET |[Exempelapp för Azure AD Graph API](http://go.microsoft.com/fwlink/?LinkID=262648) |Använd Graph API för att komma åt directory data från Azure AD. |
| C#/.NET |[Exempelapp för Azure AD Graph differentiell frågan](http://go.microsoft.com/fwlink/?LinkId=275398) |Använda differentiella frågan i Graph API för att få periodiska ändringar användarobjekt i en Azure AD-klient. |
| C#/.NET |[Sample-appen för att integrera med flera innehavare molnet program för Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) |Integrera ett program med flera innehavare i Azure AD. |
| C#/.NET |[Skydda en Windows Store-programmet och REST-webbtjänst med hjälp av Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Skapa en enkel webb-API-resurs och ett klientprogram för Windows Store med hjälp av Azure AD och [Azure AD Authentication Library (ADAL)](active-directory-authentication-libraries.md). |
| C#/.NET |[Med Graph API för att fråga Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |Konfigurera en Microsoft .NET-program att använda Azure AD Graph API för åtkomst till data från en klient Azure AD-katalog. |

## <a name="see-also"></a>Se också
##### <a name="other-resources"></a>Andra resurser
[Azure Active Directory-Guide för utvecklare](active-directory-developers-guide.md)

[Azure AD Graph API konceptuell och referenser](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Azure AD Graph API hjälpbibliotek](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
