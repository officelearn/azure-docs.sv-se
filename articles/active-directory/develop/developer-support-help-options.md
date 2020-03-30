---
title: Support- och hjälpalternativ för Azure AD-apputvecklare | Microsoft-dokument
description: Lär dig hur du får hjälp och stöd för utvecklingsrelaterade frågor och problem när du skapar program som integreras med Microsoft-identiteter (Azure Active Directory och Microsoft-konto)
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 89bf49fb44d8575b251a0b33698bc4ce8425cc2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160975"
---
# <a name="support-and-help-options-for-developers"></a>Support- och hjälpalternativ för utvecklare

Om du precis har börjat integrera med Azure Active Directory (Azure AD), Microsoft-identiteter eller Microsoft Graph API, eller när du implementerar en ny funktion i ditt program, finns det tillfällen då du behöver få hjälp från communityn eller förstå supportalternativ som du har som utvecklare. Den här artikeln hjälper dig att förstå dessa alternativ, inklusive:

> [!div class="checklist"]
> * Så här söker du efter om din fråga inte har besvarats av communityn eller om det redan finns en befintlig dokumentation för funktionen som du försöker implementera
> * I vissa fall vill du bara använda våra supportverktyg för att hjälpa dig att felsöka ett specifikt problem
> * Om du inte hittar det svar du behöver kanske du vill ställa en fråga om *Stack Overflow*
> * Om du hittar ett problem med något av våra autentiseringsbibliotek kan du ta upp ett *GitHub-problem*
> * Slutligen, om du behöver prata med någon, kanske du vill öppna en supportbegäran

## <a name="search"></a>Search

Om du har en utvecklingsrelaterad fråga kan du kanske hitta svaret i dokumentationen, [GitHub-exempel](https://github.com/azure-samples)eller svar på [frågor om Stack-spill.](https://www.stackoverflow.com)

### <a name="scoped-search"></a>Begränsad sökning

För snabbare resultat kan du begränsa sökningen till Stack Overflow, dokumentationen och kodexemplen med hjälp av följande fråga i din favoritsökmotor:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Där *{Dina söktermer}* motsvarar dina sökord.

## <a name="use-the-development-support-tools"></a>Använd verktygen för utvecklingsstöd

| Verktyg  | Beskrivning  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Klistra in ett ID eller åtkomsttoken för att avkoda anspråksnamn och värden. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Verktyg som gör att du kan göra förfrågningar och se svar mot Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Skicka en fråga till Stack Overflow

Stack Overflow är den föredragna kanalen för utvecklingsrelaterade frågor. Här är medlemmar i utvecklarcommunityn och Microsoft-teammedlemmar direkt involverade i att hjälpa dig att lösa dina problem.

Om du inte hittar ett svar på din fråga via sökning skickar du en ny fråga till Stack Overflow. Använd någon av följande taggar när du ställer frågor för att hjälpa communityn att identifiera och besvara din fråga snabbare:

|Komponent/område  | Taggar |
|---------|---------|
| ADAL bibliotek | [- Jag vet inte vad du går för.](https://stackoverflow.com/questions/tagged/adal) |
| MSAL-bibliotek     | [- Jag vet inte vad du kan göra.](https://stackoverflow.com/questions/tagged/msal) |
| OWIN mellanprogram  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph-API](https://developer.microsoft.com/graph/) | [[Microsoft-graf]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Alla andra områden som rör autentiserings- eller auktoriseringsämnen | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Följande inlägg från Stack Overflow innehåller tips om hur du ställer frågor och hur du lägger till källkod. Följ dessa riktlinjer för att öka chanserna för community-medlemmar att snabbt bedöma och svara på din fråga:

* [Hur ställer jag en bra fråga](https://stackoverflow.com/help/how-to-ask)
* [Så här skapar du ett minimalt, komplett och verifierbart exempel](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Skapa ett GitHub-problem

Om du hittar ett fel eller problem som är relaterat till våra bibliotek kan du ta upp ett problem i våra GitHub-databaser. Eftersom våra bibliotek är öppen källkod kan du också skicka in en pull-begäran.

En lista över bibliotek och deras GitHub-databaser finns i följande avsnitt:

* [ADAL-bibliotek (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) och GitHub-databaser
* [MSAL-bibliotek (Microsoft Authentication Library)](reference-v2-libraries.md) och GitHub-databaser

## <a name="open-a-support-request"></a>Öppna en supportbegäran

Om du behöver prata med någon kan du öppna en supportförfrågan. Om du är en Azure-kund finns det flera supportalternativ tillgängliga. Om du vill jämföra planer läser du [den här sidan](https://azure.microsoft.com/support/plans/). Utvecklarsupport är också tillgängligt för Azure-kunder. Information om hur du köper supportplaner för utvecklare finns på [den här sidan](https://azure.microsoft.com/support/plans/developer/).

* Om du redan har en Azure-supportplan [öppnar du en supportbegäran här](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Om du inte är En Azure-kund kan du också öppna en supportbegäran med Microsoft via [vår kommersiella support](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Du kan också prova en [virtuell agent](https://support.microsoft.com/contactus/?ws=support) för att få support eller ställa frågor.
