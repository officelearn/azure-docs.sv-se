---
title: Översikt över Azure Active Directory för utvecklare (v 1.0)
description: Den här artikeln innehåller en översikt över hur du loggar in Microsoft arbets-och skol konton med hjälp av Azure Active Directory v 1.0-slutpunkt och-plattform.
services: active-directory
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e790728cbc7415532754d10773d466e9a0280422
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373914"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Översikt över Azure Active Directory för utvecklare (v 1.0)

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst som utvecklare kan använda för att skapa appar som på ett säkert sätt kan logga in användare som har ett arbets- eller skolkonto hos Microsoft. Azure AD stöder utvecklare som skapar såväl verksamhetsspecifika appar för en klient som appar för flera klienter. Förutom grundläggande inloggning, kan Azure AD även låta appar anropa både Microsoft API:er som [Microsoft Graph](https://docs.microsoft.com/graph/overview) och anpassade API:er som bygger på Azure AD-plattformen. Den här dokumentationen innehåller information om hur du lägger till stöd för Azure AD i ditt program med hjälp av protokoll av branschstandardtyp som OAuth2.0 och OpenID Connect.

> [!NOTE]
> Merparten av innehållet på den här sidan fokuserar på slut punkten och plattformen för v 1.0, som endast stöder Microsoft arbets-eller skol konton. Om du vill logga in på konsumenter eller personliga Microsoft-konton kan du läsa informationen på [slut punkten och plattformen för v 2.0](v2-overview.md). V 2.0-slutpunkten erbjuder en enhetlig utvecklings upplevelse för appar som vill logga in på alla Microsoft-identiteter.

| | |
| --- | --- |
|[Grundläggande om autentisering](v1-authentication-scenarios.md) | En introduktion till autentisering med Azure AD. |
|[Typer av program](app-types.md) | En översikt över de autentiseringsscenarier som stöds av Azure AD. |
| | |

## <a name="get-started"></a>Kom i gång

V 1.0 snabb starter och självstudier vägleder dig genom att skapa en app på önskad plattform med hjälp av Azure AD Authentication Library (ADAL) SDK. Se **snabb starter** och **v 1.0-självstudierna** för v 1.0 i [Microsoft Identity Platform (Azure Active Directory för utvecklare)](index.yml) för att komma igång.

## <a name="how-to-guides"></a>Instruktionsguider

Mer information och genom gångar för de vanligaste uppgifterna i Azure AD finns i **v 1.0 instruktions guider** .

## <a name="reference-topics"></a>Referensämnen

Följande artiklar innehåller detaljerad information om API:er, protokollmeddelanden och de termer som används i Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Autentiseringsbibliotek (ADAL)](active-directory-authentication-libraries.md)   | En översikt över de bibliotek och SDK:er som tillhandahålls av Azure AD. |
| [Kodexempel](sample-v1-code.md)                                  | En lista över alla Azure AD-kodexempel. |
| [Ordlista](developer-glossary.md)                                      | Termer och definitioner av ord som används i den här dokumentationen. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
