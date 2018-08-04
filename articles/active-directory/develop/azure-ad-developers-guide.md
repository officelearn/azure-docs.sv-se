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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6c3148f6ca9e538d2fed3467ba5d2129e123c7fd
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502292"
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory för utvecklare

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst som utvecklare kan använda för att skapa appar som på ett säkert sätt kan logga in användare som har ett arbets- eller skolkonto hos Microsoft. Azure AD stöder utvecklare som skapar såväl verksamhetsspecifika appar för en klient som appar för flera klienter. Förutom grundläggande inloggning, kan Azure AD även låta appar anropa både Microsoft API:er som [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/concepts/overview) och anpassade API:er som bygger på Azure AD-plattformen. Den här dokumentationen innehåller information om hur du lägger till stöd för Azure AD i ditt program med hjälp av protokoll av branschstandardtyp som OAuth2.0 och OpenID Connect.

> [!NOTE]
> Merparten av innehållet på den här sidan fokuserar på Azure AD v1.0-slutpunkten, vilken enbart stöder Microsoft arbets- och skolkonton. Om du vill logga in konsumentkonton eller personliga Microsoft-konton, så läs informationen om [Azure AD v2.0-slutpunkten](active-directory-appmodel-v2-overview.md). Azure AD v2.0-slutpunkten erbjuder en enhetlig utvecklarupplevelse för appar som vill logga in både användare med Azure AD-konton (arbete och skola) och personliga Microsoft-konton.

| | |
| --- | --- |
|[Grundläggande om autentisering](authentication-scenarios.md) | En introduktion till autentisering med Azure AD. |
|[Typer av program](authentication-scenarios.md#application-types-and-scenarios) | En översikt över de autentiseringsscenarier som stöds av Azure AD. |      
| | |

## <a name="get-started"></a>Kom igång
Med följande guidade konfigurationer får du hjälp med att skapa en app på den plattform du önskar med hjälp av SDK:n för Azure AD Library (ADAL). Om du söker efter information om hur man använder Microsoft Authentication Library (MSAL), så läs vår dokumentation om [Azure AD v2.0-slutpunkten](active-directory-appmodel-v2-overview.md).

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobilappar och skrivbordsappar](./media/azure-ad-developers-guide/NativeApp_Icon.png)<br />Mobilappar och skrivbordsappar</center> | [Översikt](authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](quickstart-v1-ios.md)<br /><br />[Android](quickstart-v1-android.md) | [.NET (WPF)](quickstart-v1-dotnet.md)<br /><br />[Xamarin](quickstart-v1-xamarin.md) |
| <center>![Webbappar](./media/azure-ad-developers-guide/Web_app.png)<br />Webbappar</center> | [Översikt](authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](quickstart-v1-aspnet-webapp.md)<br /><br />[Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) | [Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)<br/><br/> [Node.js](quickstart-v1-openid-connect-code.md) |
| <center>![Appar med en sida](./media/azure-ad-developers-guide/SPA.png)<br />Appar med en sida</center> | [Översikt](authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |
| <center>![Webb-API:er](./media/azure-ad-developers-guide/Web_API.png)<br />Webb-API:er</center> | [Översikt](authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Tjänst-till-tjänst](./media/azure-ad-developers-guide/Service_App.png)<br />Tjänst-till-tjänst</center> | [Översikt](authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#daemon-applications-accessing-web-apis-with-the-applications-identity)|  |
|  |  |  |  |  |

## <a name="how-to-guides"></a>Instruktionsguider
Guiderna nedan vägleder dig igenom några av de vanligaste uppgifterna i Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Programregistrering](quickstart-v1-integrate-apps-with-azure-ad.md)           | Så här registrerar du ett program i Azure AD. |
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
