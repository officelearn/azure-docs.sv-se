---
title: Alternativ för support och hjälp för utvecklare för Azure AD-appar | Microsoft Docs
description: Vet hur man få hjälp och support för utveckling frågor och problem när du skapar program som integreras med Microsoft-identiteter (Azure Active Directory och Microsoft-konto)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2019
ms.author: celested
ms.reviewer: jmprieur, dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f3f041fa3b594b4b680d794c1c013e55b97da4
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235197"
---
# <a name="support-and-help-options-for-developers"></a>Support och hjälper dig att alternativ för utvecklare

Om du precis har börjat integrera med Azure Active Directory (AD Azure), Microsoft identiteter eller Microsoft Graph API, eller när du implementerar en ny funktion för ditt program, det finns tillfällen när du behöver få hjälp från communityn eller förstå den stöd för alternativ som du har som utvecklare. Den här artikeln hjälper dig att förstå dessa alternativ, inklusive:

> [!div class="checklist"]
> * Hur du söker efter om din fråga inte har besvarats av communityn, eller om en befintlig dokumentationen för funktionen du försöker att implementera redan finns
> * I vissa fall kan vill du bara använda vår support-verktygen för att felsöka specifika problem
> * Om du inte hittar svaret som du behöver kan du vilja ställa en fråga på *Stack Overflow*
> * Om du upptäcker ett problem med en av våra autentiseringsbibliotek, generera en *GitHub* problemet
> * Slutligen, om du vill tala med någon kan du kanske vill öppna en supportbegäran

## <a name="search"></a>Search

Om du har en utvecklings-relaterad fråga kan du kanske kan hitta svaret i dokumentationen, [GitHub-exempel](https://github.com/azure-samples), eller svar på [Stack Overflow](https://www.stackoverflow.com) frågor.

### <a name="scoped-search"></a>Omfattad sökning

Begränsa sökningen till Stack Overflow, dokumentationen och kodexempel med hjälp av följande fråga i en sökmotor för snabbare resultat:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Där *{Your söktermer}* motsvarar dina sökord.

## <a name="use-the-development-support-tools"></a>Använd utvecklingsverktyg för support

| Verktyget  | Beskrivning  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Klistra in ett ID eller åtkomsttoken att avkoda de anspråk namn och värden. |
| [Felkod analyzer](https://apps.dev.microsoft.com/portal/tools/errors)| Klistra in en felkod som togs emot under inloggning eller medgivanden sidor om du vill se möjliga orsaker och lösningar. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Verktyg som låter dig göra begäranden och se svar mot Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Ställa en fråga till Stack Overflow

Stack Overflow är prioriterade kanalen för utveckling-relaterade frågor. Medlemmar i vårt community för utvecklare och Microsofts gruppmedlemmar är här är direkt inblandade i att hjälpa dig att lösa dina problem.

Om du inte hittar svar på din fråga via sökning kan skicka en ny fråga att Stack Overflow. Använd någon av följande taggar när du ställer frågor så att communityn identifiera och svara snabbare på din fråga:

|Komponenten/område  | Taggar |
|---------|---------|
| ADAL-biblioteket | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| MSAL bibliotek     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN-mellanprogrammet  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph-API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Annat område som rör autentisering eller auktorisering ämnen | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

I följande inlägg från Stack Overflow innehåller tips om hur du ställer frågor och hur du lägger till källkoden. Följ dessa riktlinjer för att öka risken för community-medlemmar att utvärdera och svara snabbt på din fråga:

* [Hur jag för att ställa en bra fråga](https://stackoverflow.com/help/how-to-ask)
* [Så här skapar du en minimal, fullständig och kontrollerbara-exempel](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Skapa ett GitHub-ärende

Om du hittar något fel eller problem som rör våra bibliotek, skapar du ett problem i vår GitHub-lagringsplatser. Eftersom våra bibliotek är öppen källkod, kan du även skicka en pull-begäran.

Om du vill se en lista med bibliotek och deras GitHub-databaser finns i följande artiklar:

* [ADAL](active-directory-authentication-libraries.md) bibliotek och GitHub-lagringsplatser
* [MSAL](reference-v2-libraries.md) bibliotek och GitHub-lagringsplatser

## <a name="open-a-support-request"></a>Öppna en supportbegäran

Om du vill tala med någon kan öppna du en supportbegäran. Om du är en Azure-kund, finns det flera alternativ för support. Jämförelse mellan planer finns [den här sidan](https://azure.microsoft.com/support/plans/). Developer support är också tillgängligt för Azure-kunder. Information om hur du köper supportavtal för utvecklare finns i [den här sidan](https://azure.microsoft.com/support/plans/developer/).

* Om du redan har en Azure-supportavtalet, [öppna en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Om du inte är Azure-kund kan du också öppna en supportförfrågan med Microsoft via [vår kommersiella support](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Du kan också prova en [virtuella agenten](https://support.microsoft.com/contactus/?ws=support) att få support eller ställa frågor.

### <a name="free-chat-support-for-a-limited-time"></a>Kostnadsfria chattsupport under en begränsad tid

Du kan också använda våra chat-supporten är gratis för Microsoft-Partners under en begränsad tid. Om ditt företag inte är en Microsoft-Partner, du kan registrera sig utan kostnad och hämta andra fördelar genom att gå [här](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Efter att ditt företag, du kan börja chatta begäran [här](https://aka.ms/devchat).
