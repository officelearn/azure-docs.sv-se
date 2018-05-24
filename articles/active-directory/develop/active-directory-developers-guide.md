---
title: Azure Active Directory för utvecklare | Microsoft Docs
description: Den här artikeln innehåller en översikt över inloggning med Microsofts arbets- och skolkonton med hjälp av Azure Active Directory.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6f3c0e93b20bbc570f4715318a49b502549ff295
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257557"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory för utvecklare

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst som utvecklare kan använda för att skapa appar som på ett säkert sätt kan logga in användare som har ett arbets- eller skolkonto hos Microsoft. Azure AD stöder utvecklare som skapar såväl verksamhetsspecifika appar för en klient som appar för flera klienter. Förutom grundläggande inloggning, kan Azure AD även låta appar anropa både Microsoft API:er som [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) och anpassade API:er som bygger på Azure AD-plattformen. Den här dokumentationen innehåller information om hur du lägger till stöd för Azure AD i ditt program med hjälp av protokoll av branschstandardtyp som OAuth2.0 och OpenID Connect.

> [!NOTE]
> Merparten av innehållet på den här sidan fokuserar på Azure AD v1.0-slutpunkten, vilken enbart stöder Microsoft arbets- och skolkonton. Om du vill logga in konsumentkonton eller personliga Microsoft-konton, så läs informationen om [Azure AD v2.0-slutpunkten](active-directory-appmodel-v2-overview.md). Azure AD v2.0-slutpunkten erbjuder en enhetlig utvecklarupplevelse för appar som vill logga in både användare med Azure AD-konton (arbete och skola) och personliga Microsoft-konton.

| | |
| --- | --- |
|[Grundläggande om autentisering](active-directory-authentication-scenarios.md) | En introduktion till autentisering med Azure AD. |
|[Typer av program](active-directory-authentication-scenarios.md#application-types-and-scenarios) | En översikt över de autentiseringsscenarier som stöds av Azure AD. |      
| | |

## <a name="get-started"></a>Kom igång
Med följande guidade konfigurationer får du hjälp med att skapa en app på den plattform du önskar med hjälp av SDK:n för Azure AD Library (ADAL). Om du söker efter information om hur man använder Microsoft Authentication Library (MSAL), så läs vår dokumentation om [Azure AD v2.0-slutpunkten](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobilappar och skrivbordsappar](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobilappar och skrivbordsappar</center> | [Översikt](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Webbappar](./media/active-directory-developers-guide/Web_app.png)<br />Webbappar</center> | [Översikt](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) | |
| <center>![Appar med en sida](./media/active-directory-developers-guide/SPA.png)<br />Appar med en sida</center> | [Översikt](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Webb-API:er](./media/active-directory-developers-guide/Web_API.png)<br />Webb-API:er</center> | [Översikt](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Tjänst-till-tjänst](./media/active-directory-developers-guide/Service_App.png)<br />Tjänst-till-tjänst</center> | [Översikt](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Instruktionsguider
Guiderna nedan vägleder dig igenom några av de vanligaste uppgifterna i Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Programregistrering](active-directory-integrating-applications.md)           | Så här registrerar du ett program i Azure AD. |
|[Program för flera innehavare](active-directory-devhowto-multi-tenant-overview.md)    | Så här loggar du in med valfritt Microsoft-arbetskonto. |
|[OAuth och OpenID Connect-protokoll](active-directory-protocols-openid-connect-code.md)| Så här loggar du in användare och anropar webb-API:er med Microsofts autentiseringsprotokoll. |
|  |  |

## <a name="reference-topics"></a>Referensämnen
Följande artiklar innehåller detaljerad information om API:er, protokollmeddelanden och de termer som används i Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Autentiseringsbibliotek (ADAL)](active-directory-authentication-libraries.md)   | En översikt över de bibliotek och SDK:er som tillhandahålls av Azure AD. |
| [Kodexempel](active-directory-code-samples.md)                                  | En lista över alla Azure AD-kodexempel. |
| [Ordlista](active-directory-dev-glossary.md)                                      | Termer och definitioner av ord som används i den här dokumentationen. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
