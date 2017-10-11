---
title: Azure Active Directory v2.0-slutpunkten | Microsoft Docs
description: "En introduktion till att skapa appar med både Account och Azure Active Directory-inloggning."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
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
ms.openlocfilehash: 1e286044fb1a1b367fcac2dc14c47f68d5ed120d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Logga in Account & Azure AD-användare i en enda app
Tidigare var en app-utvecklare som ville stöder både personliga Microsoft-konton och fungerar konton från Azure Active Directory krävs för att integrera med två separata system.  Den **Azure AD v2.0-slutpunkten** introducerar en ny autentisering-API-version som du kan logga in båda typer av konton med hjälp av en enkel integrering.  Appar som använder v2.0-slutpunkten kan också använda REST API: er från den [Microsoft Graph](https://graph.microsoft.io) med hjälp av någon typ av konto.

## <a name="getting-started"></a>Komma igång
Välj din favoritplattform i listan nedan för att skapa en app med våra bibliotek med öppen källkod och ramverk.  Du kan också använda vår dokumentation för OAuth 2.0 & OpenID Connect-protokollet för att skicka och ta emot protokollmeddelanden direkt utan att använda ett auth-bibliotek.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Senaste nytt
Den här informationen kan vara användbart i förstå vad & vad är inte möjlig med v2.0-slutpunkten.

* Lär dig mer om den [typer av appar som du kan skapa med v2.0-slutpunkten](active-directory-v2-flows.md).
* Förstå de [begränsningar, restriktioner och villkor](active-directory-v2-limitations.md) med v2.0-slutpunkten.
* Checka ut den här översikten för v2.0-slutpunkten:

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Referens
Dessa länkar kan vara användbart för att utforska plattform i mer detalj:

* [Referens för v2.0-protokollet](active-directory-v2-protocols.md)
* [Referens för v2.0-Token](active-directory-v2-tokens.md)
* [Referens för v2.0-bibliotek](active-directory-v2-libraries.md)
* [Scope och medgivande i v2.0-slutpunkten](active-directory-v2-scopes.md)
* [Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>Hjälp och support
Det här är ett bra sätt att få hjälp med utveckling i Azure Active Directory.

* [Stackspill`azure-active-directory` och `adal`taggar](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Feedback om Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> Om du bara behöver logga in arbets- och skolkonton konton från Azure Active Directory ska du starta med vår [Azure AD-guide för utvecklare](active-directory-developers-guide.md).  V2.0-slutpunkten är avsedd att användas av utvecklare som uttryckligen måste du logga in personliga Microsoft-konton.

