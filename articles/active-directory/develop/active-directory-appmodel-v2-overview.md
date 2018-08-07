---
title: Azure Active Directory v2.0-slutpunkten | Microsoft Docs
description: En introduktion till att skapa program med både Account och Azure Active Directory-inloggning.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: hirsin, jmprieur, elisol, dastrock
ms.custom: aaddev
ms.openlocfilehash: 3d71e889fccaa6de59d9a528754a5e11d0220bef
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577143"
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Logga in Account och Azure Active Directory-användare i ett enda program
Tidigare hade programutvecklare som ville stöder både personliga Microsoft-konton och arbetskonton från Azure Active Directory att integrera med två separata system. Azure Active Directory (Azure AD) v2.0-slutpunkten introducerar en ny API-autentiseringsversion som förenklar processen. Azure AD v2.0-slutpunkten kan logga in från båda typer av konton med en enkel integration. Program som använder Azure AD v2.0-slutpunkten kan också använda REST-API: er från den [Microsoft Graph API](https://graph.microsoft.io) med hjälp av valfri typ av konto.

## <a name="getting-started"></a>Komma igång
Välj din favoritplattform i listan nedan för att skapa ett program med hjälp av Microsofts öppna käll-bibliotek och ramverk. Du kan också använda OAuth 2.0 och OpenID Connect-protokoll för att skicka och ta emot protokollmeddelanden direkt utan att använda autentiseringsbibliotek.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Läs mer om Azure AD v2.0-slutpunkten
Läs mer om vad du kan göra med Azure AD v2.0-slutpunkten:

* Identifiera den [typer av program som du kan skapa med Azure AD v2.0-slutpunkten](active-directory-v2-flows.md).
* Förstå den [begränsningar, restriktioner och villkor](active-directory-v2-limitations.md) med Azure AD v2.0-slutpunkten.
* Den här videon för en översikt över Azure AD v2.0-slutpunkten:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Ytterligare resurser
Utforska detaljerad information om Azure AD v2.0-slutpunkt-plattformen:

* [Referera till Azure AD v2.0-protokoll](active-directory-v2-protocols.md)
* [Azure AD v2.0 tokens referens](v2-id-and-access-tokens.md)
* [Referera till Azure AD v2.0-autentiseringsbibliotek](active-directory-v2-libraries.md)
* [Omfång och godkännande i Azure AD v2.0-slutpunkten](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Om du behöver bara logga in arbets- och skolkonton konton från Azure Active Directory kan du börja med den [utvecklarhandboken för Azure AD](azure-ad-developers-guide.md). Azure AD v2.0-slutpunkten är avsedd att användas av utvecklare som uttryckligen måste du logga in personliga Microsoft-konton.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
