---
title: Azure Active Directory v2.0-slutpunkten | Microsoft Docs
description: "En introduktion till utveckling av program med både Account och Azure Active Directory-inloggning."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 446e39f57448cbc0cd9a6f93a388159d7eca5863
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>Logga in Account och Azure Active Directory-användare i ett enda program
Tidigare hade programutvecklare som ville stöder både personliga Microsoft-konton och fungerar konton från Azure Active Directory att integrera med två separata system. Azure Active Directory (AD Azure) v2.0-slutpunkten introducerar en ny autentisering-API-version som förenklar processen. Azure AD v2.0-slutpunkten kan logga in från båda typer av konton med hjälp av en enkel integrering. Program som använder Azure AD v2.0-slutpunkten kan också använda REST-API: er från den [Microsoft Graph API](https://graph.microsoft.io) genom att använda någon typ av konto.

## <a name="getting-started"></a>Komma igång
Välj din favoritplattform i listan nedan om du vill skapa ett program med hjälp av Microsofts Öppna källa bibliotek och ramverk. Du kan också använda protokoll för OAuth 2.0 och OpenID Connect skickar och tar emot protokollmeddelanden direkt utan att använda en autentiseringsbibliotek.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Lär dig mer om Azure AD v2.0-slutpunkten
Mer information om vad du kan göra med Azure AD v2.0-slutpunkten:

* Identifiera den [typer av program som du kan skapa med Azure AD v2.0-slutpunkten](active-directory-v2-flows.md).
* Förstå de [begränsningar, restriktioner och villkor](active-directory-v2-limitations.md) med Azure AD v2.0-slutpunkten.
* Det här videoklippet en översikt över Azure AD v2.0-slutpunkten:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>Ytterligare resurser
Utforska detaljerad information om Azure AD-plattformen v2.0-slutpunkten:

* [Referera till Azure AD v2.0-protokoll](active-directory-v2-protocols.md)
* [Azure AD v2.0 tokens referens](active-directory-v2-tokens.md)
* [Referera till Azure AD v2.0-autentiseringsbibliotek](active-directory-v2-libraries.md)
* [Scope och medgivande i Azure AD v2.0-slutpunkten](active-directory-v2-scopes.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Om du behöver bara logga in arbets- och skolkonton konton från Azure Active Directory, börja med den [Azure AD-guide för utvecklare](active-directory-developers-guide.md). Azure AD v2.0-slutpunkten är avsedd att användas av utvecklare som uttryckligen måste du logga in personliga Microsoft-konton.

[!INCLUDE  [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
