---
title: Support-och hjälp alternativ för Azure AD Apps-utvecklare
description: Lär dig mer om att få hjälp och support för utvecklings-relaterade frågor och problem när du skapar program som integreras med Microsoft-identiteter (Azure Active Directory och Microsoft-konto)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: af363bb30d6515ce969afe146c780baa4b31cd83
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117217"
---
# <a name="support-and-help-options-for-developers"></a>Support- och hjälpalternativ för utvecklare

Om du bara börjar integrera med Azure Active Directory (Azure AD), Microsoft-identiteter eller Microsoft Graph API eller när du implementerar en ny funktion för ditt program, finns det tillfällen när du behöver få hjälp från communityn eller förstå de support alternativ som du har som utvecklare. Den här artikeln hjälper dig att förstå dessa alternativ, inklusive:

> [!div class="checklist"]
> * Så här söker du efter om din fråga inte har besvarats av communityn eller om en befintlig dokumentation för funktionen som du försöker implementera redan finns
> * I vissa fall vill du bara använda våra support verktyg för att felsöka ett visst problem
> * Om du inte hittar det svar du behöver kan du ställa en fråga på *Stack Overflow*
> * Om du hittar ett problem med ett av våra autentiseringspaket, Utlös ett *GitHub* -problem
> * Slutligen, om du behöver prata med någon, kanske du vill öppna en support förfrågan

## <a name="search"></a>Sök

Om du har en utvecklings fråga kanske du kan hitta svaret i dokumentationen, [GitHub-exempel](https://github.com/azure-samples)eller svar på [Stack Overflow](https://www.stackoverflow.com) frågor.

### <a name="scoped-search"></a>Omfattnings sökning

För snabbare resultat kan du begränsa sökningen till Stack Overflow, dokumentationen och kod exemplen med hjälp av följande fråga i din favorit sökmotor:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Där *{dina Sök villkor}* motsvarar dina Sök nyckelord.

## <a name="use-the-development-support-tools"></a>Använd verktyg för utvecklings support

| Verktyg  | Beskrivning  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Klistra in ett ID eller en åtkomsttoken för att avkoda anspråks namnen och värdena. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Verktyg som du kan använda för att göra förfrågningar och se svar mot Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Skicka en fråga till Stack Overflow

Stack Overflow är den föredragna kanalen för utvecklings frågor. Här är medlemmar i utvecklings gruppen och Microsoft-team medlemmar direkt inblandade i att hjälpa dig att lösa dina problem.

Om du inte hittar ett svar på din fråga via sökning skickar du en ny fråga till Stack Overflow. Använd någon av följande Taggar när du ställer frågor som hjälper communityn att identifiera och besvara din fråga snabbare:

|Komponent/Area  | Taggar |
|---------|---------|
| ADAL-bibliotek | [ADAL](https://stackoverflow.com/questions/tagged/adal) |
| MSAL-bibliotek     | [msal](https://stackoverflow.com/questions/tagged/msal) |
| OWIN mellanprogram  | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[Azure-AD-B2B]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure-B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-AD-B2C]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Microsoft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Andra områden som rör autentiserings-eller auktoriserings ämnen | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Följande inlägg från Stack Overflow innehåller tips om hur du ställer frågor och hur du lägger till käll koden. Följ dessa rikt linjer för att öka sannolikheten för att community-medlemmar ska kunna utvärdera och svara på din fråga snabbt:

* [Hur gör jag för att fråga en lämplig fråga](https://stackoverflow.com/help/how-to-ask)
* [Så här skapar du ett minimalt, komplett och verifierbart exempel](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Skapa ett GitHub-problem

Om du hittar ett fel eller problem som rör våra bibliotek kan du generera ett problem i våra GitHub-databaser. Eftersom våra bibliotek är öppna källor kan du också skicka in en pull-begäran.

En lista över bibliotek och deras GitHub-databaser finns i följande avsnitt:

* [ADAL-bibliotek (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) och GitHub-databaser
* Bibliotek för [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) och GitHub-databaser

## <a name="open-a-support-request"></a>Öppna en supportbegäran

Om du behöver prata med någon kan du öppna en support förfrågan. Om du är en Azure-kund finns det flera tillgängliga support alternativ. Information om hur du jämför planer finns på [den här sidan](https://azure.microsoft.com/support/plans/). Support för utvecklare är också tillgängligt för Azure-kunder. Information om hur du köper support avtal för utvecklare finns på [den här sidan](https://azure.microsoft.com/support/plans/developer/).

* Om du redan har ett support avtal för Azure [öppnar du en support förfrågan här](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Om du inte är en Azure-kund kan du också öppna en supportbegäran med Microsoft via [vår kommersiella support](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Du kan också prova en [virtuell agent](https://support.microsoft.com/contactus/?ws=support) för att få support eller ställa frågor.