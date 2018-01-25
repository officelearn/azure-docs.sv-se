---
title: "Azure Active Directory för utvecklare | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över inloggning med Microsofts arbets- och skolkonton med hjälp av Azure Active Directory."
services: active-directory
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 89a232af6387f6403e6e341cced16d06e9979dae
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="azure-active-directory-for-developers"></a>Azure Active Directory för utvecklare
Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst som utvecklare kan använda för att på ett säkert sätt logga in användare som har ett arbets- eller skolkonto hos Microsoft. Den här dokumentationen innehåller information om hur du lägger till stöd för Azure AD i ditt program med hjälp av protokoll av branschstandardtyp: OAuth2.0 och OpenID Connect.

| | |
| --- | --- |
|[Grundläggande om autentisering](active-directory-authentication-scenarios.md) | En introduktion till autentisering med Azure AD. |
|[Typer av program](active-directory-authentication-scenarios.md#application-types-and-scenarios) | En översikt över de autentiseringsscenarier som stöds av Azure AD. |                                
                                                                              
## <a name="get-started"></a>Kom igång
Följande guider vägleder dig genom användningen av Microsoft-autentiseringsbibliotek för inloggning av Azure AD-användare.

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![Mobilappar och skrivbordsappar](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />Mobilappar och skrivbordsappar</center> | [Översikt](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET (WPF)](active-directory-devquickstarts-dotnet.md)<br /><br />[.NET (UWP)](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md) |
| <center>![Webbappar](./media/active-directory-developers-guide/Web_app.png)<br />Webbappar</center> | [Översikt](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |  |
| <center>![Appar med en sida](./media/active-directory-developers-guide/SPA.png)<br />Appar med en sida</center> | [Översikt](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Webb-API:er](./media/active-directory-developers-guide/Web_API.png)<br />Webb-API:er</center> | [Översikt](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[Node.js](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![Tjänst-till-tjänst](./media/active-directory-developers-guide/Service_App.png)<br />Tjänst-till-tjänst</center> | [Översikt](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)|  |

## <a name="how-to-guides"></a>Instruktionsguider
Följande vägledningar innehåller information om hur du utför vanliga uppgifter med hjälp av Azure AD.

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[Programregistrering](active-directory-integrating-applications.md)           | Så här registrerar du ett program i Azure AD. |
|[Program för flera innehavare](active-directory-devhowto-multi-tenant-overview.md)    | Så här loggar du in med valfritt Microsoft-arbetskonto. |
|[OAuth och OpenID Connect-protokoll](active-directory-protocols-openid-connect-code.md)| Så här loggar du in användare och anropar webb-API:er med Microsofts autentiseringsprotokoll. |
|[Ytterligare vägledningar](active-directory-developers-guide-index.md#guides)        |  En lista över vägledningar som är tillgängliga för Azure AD.   |

## <a name="reference-topics"></a>Referensämnen
Följande artiklar innehåller detaljerad information om API:er, protokollmeddelanden och de termer som används i Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Autentiseringsbibliotek (ADAL)](active-directory-authentication-libraries.md)   | En översikt över de bibliotek och SDK:er som tillhandahålls av Azure AD. |
| [Kodexempel](active-directory-code-samples.md)                                  | En lista över alla Azure AD-kodexempel. |
| [Ordlista](active-directory-dev-glossary.md)                                      | Termer och definitioner av ord som används i den här dokumentationen. |
| [Ytterligare referensämnen](active-directory-developers-guide-index.md#reference)| En lista över referensämnen som är tillgängliga för Azure AD.   |


> [!NOTE]
> För inloggning med personliga Microsoft-konton bör du överväga användning av en [Azure AD v2.0-slutpunkt](active-directory-appmodel-v2-overview.md). Azure AD v2.0-slutpunkten för samman personliga Microsoft-konton med Microsoft-arbetskonton (från Azure AD) och möjliggör ett enda autentiseringssystem.


[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]