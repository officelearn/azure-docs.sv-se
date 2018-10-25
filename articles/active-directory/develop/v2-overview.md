---
title: Om v2.0 | Azure
description: Läs mer om v2.0-slutpunkten och plattformen.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 265d34c91a8c803256e718899f5b6ce2738a88e5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956440"
---
# <a name="about-v20"></a>Om v2.0

V2.0-slutpunkten och plattformen har varit i en förhandsversion och förbättrats ständigt. I dag är scenarier med JavaScript-ensidesprogram (SPA) funktionskompletta, och vi vill gärna att du använder MSAL.js för att skapa webbläsarbaserade program och ger oss feedback så att vi kan uppdatera statusen från förhandsversion till allmänt tillgänglig (GA, General Availability).

> [!NOTE]
> MSAL Android, iOS och .NET har fortfarande funktioner under utveckling. Du kan använda dem för att skapa program och skicka feedback.

Utvecklarupplevelsen i Azure-portalen har uppdaterats avsevärt och omfattar nu alla dina program som skapats med ADAL eller MSAL och har förbättrad användbarhet.

Tidigare var programutvecklare som ville stödja både personliga Microsoft-konton och arbetskonton från Azure Active Directory (AD Azure) tvungna att integrera med två separata system. V2.0-slutpunkten och plattformen ger en API-autentiseringsversion som förenklar den här processen. Den kan logga in från båda typer av konton med hjälp av en enskild integrering. Program som använder v2.0-slutpunkten kan också använda REST-API:er från [Microsoft Graph API](https://graph.microsoft.io) med hjälp av endera kontotyp.

## <a name="getting-started"></a>Komma igång

Välj din favoritplattform i följande lista för att skapa ett program med hjälp av Microsofts bibliotek och ramverk med öppen källkod. Du kan även använda OAuth 2.0- och OpenID Connect-protokoll för att skicka och ta emot protokollmeddelanden direkt utan att använda ett autentiseringsbibliotek.

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Läs mer om v2.0-slutpunkten och plattformen

Läs mer om vad du kan göra med Azure AD v2.0-slutpunkten:

* Identifiera de [typer av program som du kan skapa med Azure AD v2.0-slutpunkten](v2-app-types.md).
* Förstå [begränsningarna, restriktionerna och villkoren](active-directory-v2-limitations.md) med Azure AD v2.0-slutpunkten.
* Titta på den här videon för att få en översikt över Azure AD v2.0-slutpunkten:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Ytterligare resurser

Utforska detaljerad information om v2.0:

* [Referens för v2.0-protokoll](active-directory-v2-protocols.md)
* [Referens för åtkomsttoken](access-tokens.md)
* [`id_tokens`-referens](id-tokens.md)
* [Referens för v2.0-autentiseringsbibliotek](reference-v2-libraries.md)
* [Omfång och medgivande i v2.0](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Om du behöver bara logga in arbets- och skolkonton från Azure Active Directory kan du börja med [utvecklarhandboken för Azure AD](v1-overview.md). v2.0-slutpunkten är avsedd att användas av utvecklare som uttryckligen behöver logga in personliga Microsoft-konton.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
