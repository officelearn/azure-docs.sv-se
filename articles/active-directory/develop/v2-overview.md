---
title: Om v2.0 | Azure
description: Läs mer om v2.0-slutpunkten och plattformen.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb269df035bcc11583ebb7cff7d1ee2c3f6d8bca
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56208307"
---
# <a name="about-v20"></a>Om v2.0

V2.0-slutpunkten och plattformen har varit i en förhandsversion och förbättrats ständigt. I dag är scenarier med JavaScript-ensidesprogram (SPA) funktionskompletta, och vi vill gärna att du använder MSAL.js för att skapa webbläsarbaserade program och ger oss feedback så att vi kan uppdatera statusen från förhandsversion till allmänt tillgänglig (GA, General Availability).

> [!NOTE]
> MSAL Android, iOS och .NET har fortfarande funktioner under utveckling. Du kan använda dem för att skapa program och skicka feedback.

Upplevelsen i Azure-portalens [Appregistreringar (förhandsversion)](quickstart-register-app.md) har uppdaterats avsevärt och omfattar nu alla dina program som skapats med ADAL eller MSAL och har förbättrad användbarhet.

Tidigare var programutvecklare som ville stödja både personliga Microsoft-konton och arbetskonton från Azure Active Directory (AD Azure) tvungna att integrera med två separata system. V2.0-slutpunkten och plattformen ger en API-autentiseringsversion som förenklar den här processen. Den kan logga in från båda typer av konton med hjälp av en enskild integrering. Program som använder v2.0-slutpunkten kan också använda REST-API:er från [Microsoft Graph API](https://developer.microsoft.com/graph) med hjälp av endera kontotyp.

## <a name="getting-started"></a>Komma igång

Välj din favoritplattform i följande lista för att skapa ett program med hjälp av Microsofts bibliotek och ramverk med öppen källkod:

[!INCLUDE [v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-v20-endpoint-and-platform"></a>Läs mer om v2.0-slutpunkten och plattformen

Läs mer om vad du kan göra med Azure AD v2.0-slutpunkten:

* Identifiera de [typer av program som du kan skapa med Azure AD v2.0-slutpunkten](v2-app-types.md).
* Förstå [begränsningarna, restriktionerna och villkoren](active-directory-v2-limitations.md) med Azure AD v2.0-slutpunkten.

## <a name="additional-resources"></a>Ytterligare resurser

Utforska detaljerad information om v2.0:

* [Om Microsoft Identity-plattformen](about-microsoft-identity-platform.md)
* [Referens för v2.0-protokoll](active-directory-v2-protocols.md)
* [Referens för åtkomsttoken](access-tokens.md)
* [Referens för ID-token](id-tokens.md)
* [Referens för v2.0-autentiseringsbibliotek](reference-v2-libraries.md)
* [Behörigheter och medgivande i v2.0](v2-permissions-and-consent.md)
* [Microsoft Graph-API](https://developer.microsoft.com/graph)

> [!NOTE]
> Om du behöver bara logga in arbets- och skolkonton från Azure Active Directory kan du börja med [utvecklarhandboken för Azure AD](v1-overview.md). v2.0-slutpunkten är avsedd att användas av utvecklare som uttryckligen behöver logga in personliga Microsoft-konton.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
