---
title: Översikt över Azure Active Directory för utvecklare (v1.0)
description: Den här artikeln innehåller en översikt över inloggning i Microsofts arbets- och skolkonton med hjälp av slutpunkten och plattformen Azure Active Directory v1.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ade350c91ebd2f3a68b52011e598f739a14c220f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154499"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Översikt över Azure Active Directory för utvecklare (v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst som utvecklare kan använda för att skapa appar som på ett säkert sätt kan logga in användare som har ett arbets- eller skolkonto hos Microsoft. Azure AD stöder utvecklare som skapar såväl verksamhetsspecifika appar för en klient som appar för flera klienter. Förutom grundläggande inloggning, kan Azure AD även låta appar anropa både Microsoft API:er som [Microsoft Graph](https://docs.microsoft.com/graph/overview) och anpassade API:er som bygger på Azure AD-plattformen. Den här dokumentationen innehåller information om hur du lägger till stöd för Azure AD i ditt program med hjälp av protokoll av branschstandardtyp som OAuth2.0 och OpenID Connect.

> [!NOTE]
> Det mesta av innehållet på den här sidan fokuserar på v1.0-slutpunkten och plattformen, som endast stöder Microsofts arbets- eller skolkonton. Om du vill logga in på konsumentkonton eller personliga Microsoft-konton läser du informationen på [v2.0-slutpunkten och plattformen](../develop/v2-overview.md). V2.0-slutpunkten ger en enhetlig utvecklarupplevelse för appar som vill logga in på alla Microsoft-identiteter.

| | |
| --- | --- |
|[Grundläggande om autentisering](v1-authentication-scenarios.md) | En introduktion till autentisering med Azure AD. |
|[Typer av program](app-types.md) | En översikt över de autentiseringsscenarier som stöds av Azure AD. |
| | |

## <a name="get-started"></a>Komma igång

Snabbstartarna och självstudiekurserna v1.0 hjälper dig att skapa en app på önskad plattform med hjälp av Azure AD Authentication Library (ADAL) SDK. Se **v1.0-snabbstarterna** och **v1.0-självstudierna** i [Microsofts identitetsplattform (Azure Active Directory för utvecklare)](index.yml) för att komma igång.

## <a name="how-to-guides"></a>Anvisningar

Mer information och genomgångar av de vanligaste uppgifterna i Azure AD finns i **instruktioner för v1.0.**

## <a name="reference-topics"></a>Referensämnen

Följande artiklar innehåller detaljerad information om API:er, protokollmeddelanden och de termer som används i Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Autentiseringsbibliotek (ADAL)](active-directory-authentication-libraries.md)   | En översikt över de bibliotek och SDK:er som tillhandahålls av Azure AD. |
| [Kodexempel](sample-v1-code.md)                                  | En lista över alla Azure AD-kodexempel. |
| [Ordlista](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)                                      | Termer och definitioner av ord som används i den här dokumentationen. |
|  |  |

## <a name="videos"></a>Videoklipp

Se [Azure Active Directory-utvecklarplattformsvideor](videos.md) för hjälp med att migrera till den nya Microsoft-identitetsplattformen.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
