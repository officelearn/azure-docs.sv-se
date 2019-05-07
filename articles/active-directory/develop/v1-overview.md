---
title: Azure Active Directory för utvecklare (v1.0) översikt
description: Den här artikeln innehåller en översikt över inloggning med Microsofts arbets- och skolkonton med hjälp av Azure Active Directory v1.0 slutpunkt och plattform.
services: active-directory
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2af95d979e90f25c013f5112322d830232199a4b
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067650"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Azure Active Directory för utvecklare (v1.0) översikt

Azure Active Directory (Azure AD) är en molnbaserad identitetstjänst som utvecklare kan använda för att skapa appar som på ett säkert sätt kan logga in användare som har ett arbets- eller skolkonto hos Microsoft. Azure AD stöder utvecklare som skapar såväl verksamhetsspecifika appar för en klient som appar för flera klienter. Förutom grundläggande inloggning, kan Azure AD även låta appar anropa både Microsoft API:er som [Microsoft Graph](https://docs.microsoft.com/graph/overview) och anpassade API:er som bygger på Azure AD-plattformen. Den här dokumentationen innehåller information om hur du lägger till stöd för Azure AD i ditt program med hjälp av protokoll av branschstandardtyp som OAuth2.0 och OpenID Connect.

> [!NOTE]
> Merparten av innehållet på den här sidan fokuserar på v1.0 slutpunkt och plattform, vilken enbart stöder Microsoft arbets- eller skolkonton. Om du vill logga in konsumentkonton eller personliga Microsoft-konton, se information på den [v2.0-slutpunkten och plattformen](v2-overview.md). V2.0-slutpunkten erbjuder en enhetlig utvecklarupplevelse för appar som vill logga in alla Microsoft-identiteter.

| | |
| --- | --- |
|[Grundläggande om autentisering](authentication-scenarios.md) | En introduktion till autentisering med Azure AD. |
|[Typer av program](app-types.md) | En översikt över de autentiseringsscenarier som stöds av Azure AD. |
| | |

## <a name="get-started"></a>Kom igång

V1.0 snabbstarter och självstudier hjälper dig att skapa en app på den plattform du önskar med hjälp av de Azure AD Authentication Library (ADAL) SDK. Se den **v1.0 Snabbstarter** och **v1.0 självstudier** i [Microsoft identity-plattformen (Azure Active Directory för utvecklare)](index.yml) att komma igång.

## <a name="how-to-guides"></a>Instruktionsguider

Se den **v1.0 anvisningar hjälper** för detaljerad information och genomgångar av de vanligaste uppgifterna i Azure AD.

## <a name="reference-topics"></a>Referensämnen

Följande artiklar innehåller detaljerad information om API:er, protokollmeddelanden och de termer som används i Azure AD.

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [Autentiseringsbibliotek (ADAL)](active-directory-authentication-libraries.md)   | En översikt över de bibliotek och SDK:er som tillhandahålls av Azure AD. |
| [Kodexempel](sample-v1-code.md)                                  | En lista över alla Azure AD-kodexempel. |
| [Ordlista](developer-glossary.md)                                      | Termer och definitioner av ord som används i den här dokumentationen. |
|  |  |


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
