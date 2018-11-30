---
title: Support och hjälper dig att alternativ för Azure Identity-utvecklare | Microsoft Docs
description: Vet hur man få hjälp och support för utveckling frågor och problem när du skapar program som integreras med Microsoft Azure-identiteter (Azure Active Directory och MSA)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 60186d44437a866793b40c9d6f0263235803475f
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422447"
---
# <a name="support-and-help-options-for-developers"></a>Support och hjälper dig att alternativ för utvecklare 

Oavsett om du bara börjar att integrera med Azure Active Directory, Microsoft identiteter eller Microsoft Graph API, eller när du implementerar en ny funktion för ditt program, finns gånger som du vill få hjälp från communityn eller förstå den stöd för alternativ som du har som utvecklare. Den här artikeln hjälper dig att förstå alternativen nedan en sammanfattning:

> [!div class="checklist"]
> * Sök för att kontrollera om ditt problem fråga inte har besvarats av communityn, eller om en befintlig dokumentationen för funktionen du försöker att implementera redan finns
> * I vissa fall kan vill du bara använda vår support-verktygen för att felsöka ditt problem för en specifik
> * Om du inte hittar svaret från vad du behöver kan du vilja ställa en fråga på *Stack Overflow*
> * Om du upptäcker ett problem med en av våra autentiseringsbibliotek, generera en *GitHub* problemet
> * Slutligen, om du vill tala med någon kan du kanske vill öppna en supportbegäran


## <a name="search"></a>Search

Om du har en utvecklings-relaterad fråga kan du kanske kan hitta vad du behöver på vår dokumentation vår [github-exempel](https://github.com/azure-samples), eller svar på [Stack Overflow](https://www.stackoverflow.com) frågor.

### <a name="scoped-search"></a>Omfattad sökning
För snabbare resultat, begränsa sökningen till Stack Overflow, vår dokumentation och våra kodexempel med hjälp av följande på din [sökmotor](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Där *{Your söktermer}* är dina sökord.
<br/>

## <a name="use-our-development-support-tools"></a>Använd vår support-utvecklingsverktyg

|Verktyget  |Beskrivning  |
|---------|---------|
|[jwt.MS](https://jwt.ms)| Klistra in ID eller åtkomst-token för att avkoda de anspråk namn och värden |
|[Felkod analyzer](https://apps.dev.microsoft.com/portal/tools/errors)| Klistra in en felkod som togs emot under inloggning eller medgivanden sidor för att se möjliga orsaker och reparationer |
|[Microsoft Graph-testaren](https://developer.microsoft.com/graph/graph-explorer)| Verktyg som låter dig göra begäranden och se svar mot Microsoft Graph API|

<br/>

[![Stackspill](./media/developer-support-help-options/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Ställa en fråga till Stack Overflow

Stack Overflow är prioriterade kanalen för utveckling-relaterade frågor – där båda är medlemmar i communityn för Microsoft-gruppmedlemmar är direkt inblandade i att hjälpa dig att lösa problemet.

Om du inte hittar svar på ditt problem vid en sökning, skicka en ny fråga att Stack Overflow: Använd något av följande taggar när du gör frågor så att communityn identifiera, sedan svara på din fråga på rimlig:

|Komponenten/område  |Taggar  |
|---------|---------|
|ADAL-biblioteket |[[adal]](https://stackoverflow.com/questions/tagged/adal)|
|MSAL bibliotek     |[[msal]](https://stackoverflow.com/questions/tagged/msal)|
|OWIN-mellanprogrammet  |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph-API](https://developer.microsoft.com/graph/) |[[microsoft graph]](https://stackoverflow.com/questions/tagged/microsoft-graph)
|Annat område som rör autentisering eller auktorisering ämnen |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> I följande inlägg från Stack Overflow innehåller tips om hur du skapar frågor och tips om hur du lägger till källkoden - följa dessa riktlinjer kan du öka risken för community-medlemmar att utvärdera och svara snabbt på din fråga:  
> - [Hur jag för att ställa en bra fråga](https://stackoverflow.com/help/how-to-ask)
> - [Så här skapar du en Minimal, fullständig och kontrollerbara exempel](https://stackoverflow.com/help/mcve)

<br/>


[![Stackspill](./media/developer-support-help-options/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Skapa ett GitHub-ärende

 Om du hittar något fel eller problem som rör våra bibliotek, skapar du ett problem på vår GitHub-lagringsplatser. Eftersom våra bibliotek är öppen källkod, kan du också skicka en pull-begäran. I följande artikel innehåller en lista med bibliotek och deras GitHub-databaser:

- [ADAL och MSAL Owin-mellanprogrammet](active-directory-authentication-libraries.md) bibliotek och GitHub-lagringsplatser

<br/>

## <a name="open-a-support-request"></a>Öppna en supportbegäran

Om du vill tala med någon kan öppna du en supportbegäran. Om du är en Azure-kund, finns det flera alternativ för support. Jämförelse mellan planer finns [den här sidan](https://azure.microsoft.com/support/plans/). Developer support är också tillgängligt för Azure-kunder. Information om hur du köper supportavtal för utvecklare finns i [den här sidan](https://azure.microsoft.com/support/plans/developer/).

- Om du redan har en Azure-supportavtalet, [öppna en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Om du inte är Azure-kund kan du också öppna en supportförfrågan med Microsoft via [vår kommersiella support](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Du kan även försöka [vår virtuella agent](https://support.microsoft.com/contactus/?ws=support) att få support eller ställa frågor.

### <a name="free-chat-support-for-a-limited-time"></a>Kostnadsfria chattsupport under en begränsad tid

Du kan också använda våra chat-supporten – som är kostnadsfritt för Microsoft-Partners under en begränsad tid. Om ditt företag inte är en Microsoft-Partner, du kan registrera sig utan kostnad och hämta andra fördelar genom att gå [här](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Efter att ditt företag, du kan börja chatta begäran [här](https://aka.ms/devchat).