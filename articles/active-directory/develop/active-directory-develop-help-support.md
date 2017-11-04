---
title: "Alternativ för support och hjälp för Azure Identity utvecklare | Microsoft Docs"
description: "Veta hur de kan få hjälp och support för utveckling-relaterade frågor och problem när du skapar program som integreras med Microsoft Azure identiteter (Azure Active Directory och MSA)"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7c382da9bd9032b30f7c620e839c41c1756ba3f6
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2017
---
# <a name="support-and-help-options-for-developers"></a>Alternativ för support och hjälp för utvecklare 

Oavsett om du bara börjar att integrera med Azure Active Directory, Microsoft identiteter eller Microsoft Graph API eller när du implementerar en ny funktion i tillämpningsprogrammet gånger som behövs för att få hjälp i communityn eller förstå den stöd för alternativ som du har som utvecklare. Den här artikeln hjälper dig att förstå dessa alternativ under en sammanfattning:

> [!div class="checklist"]
> * Sökning för att kontrollera om problemet frågan inte har besvarats av gemenskapen eller om en befintlig dokumentationen för funktionen som du försöker implementera redan finns
> * I vissa fall kan vill du bara använda vår supportverktyg för att felsöka en specifik problemet
> * Om du inte hittar svar från vad du behöver du kanske vill ställa en fråga på *Stack Overflow*
> * Om du upptäcker ett problem med en av våra autentiseringsbibliotek höja ett *GitHub* problemet
> * Slutligen, om du behöver prata med någon du kanske vill öppna en supportbegäran


## <a name="search"></a>Söka

Om du har en utvecklings-relaterade fråga kan du kanske kan hitta svaret måste på vår dokumentation vår [github exempel](https://github.com/azure-samples), eller svar på [Stack Overflow](https://www.stackoverflow.com) frågor.

### <a name="scoped-search"></a>Bred sökning
Begränsa sökningen till Stack Overflow vår dokumentation och våra kodexempel för snabbare resultat med hjälp av följande på din [sökmotor](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Där *{din söktermer}* är dina sökord.
<br/>

## <a name="use-our-development-support-tools"></a>Använd våra utvecklingsverktyg för support

|Verktyget  |Beskrivning  |
|---------|---------|
|[jwt.MS](https://jwt.ms)| Klistra in-ID eller åtkomst-token för att avkoda anspråk namn och värden |
|[Felkod analyzer](https://apps.dev.microsoft.com/portal/tools/errors)| Klistra in en felkod som togs emot under inloggning eller medgivanden om du vill se möjliga orsaker och reparationer |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Verktyg som låter dig göra begäranden och se svar mot Microsoft Graph API|

<br/>

[![Stackspill](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Skicka en fråga till Stack Overflow

Stackspill är prioriterade kanalen för utveckling-relaterade frågor - där både medlemmar i communityn som Microsoft gruppmedlemmar är direkt inblandade i att hjälpa dig att lösa problemet.

Om du inte hittar svar på ditt problem via sökning, skicka en ny fråga stackspill: Använd något av följande taggar när du skapar frågor som hjälper dig identifiera, gemenskapen sedan svaret på en rimlig tid:

|Komponenten/område  |Taggar  |
|---------|---------|
|ADAL-biblioteket |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|MSAL bibliotek     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|Mellanprogram OWIN  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Annat område som är relaterade till autentisering eller auktorisering avsnitt |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Följande meddelandena från Stack Overflow innehåller tips om hur du skapar frågor och tips om att lägga till källkoden - följa dessa riktlinjer kan hjälpa dig att öka risken för community-medlemmar att utvärdera och snabbt svara på din fråga:  
> - [Hur jag för att be en bra fråga](https://stackoverflow.com/help/how-to-ask)
> - [Så här skapar du en Minimal, fullständig och verifieras exempel](https://stackoverflow.com/help/mcve)

<br/>


[![Stackspill](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Skapa ett GitHub-problem

 Om du upptäcker ett fel eller problem relaterade till våra bibliotek höja ett problem i vår GitHub-databaser. Eftersom våra bibliotek är öppen källkod, kan du också att skicka en pull-begäran. I följande artikel innehåller en lista med bibliotek och deras GitHub-databaser:

- [ADAL, MSAL och Owin mellanprogram](active-directory-authentication-libraries.md) bibliotek och GitHub-databaser

<br/>

## <a name="open-a-support-request"></a>Öppna ett supportärende

Om du behöver prata med någon kan du öppna en supportbegäran. Om du är en Azure-kund, finns det flera supportalternativ. Om du vill jämföra planer finns [den här sidan](https://azure.microsoft.com/support/plans/). Support för utvecklare är också tillgängligt för Azure-kunder. Information om hur du köper Developer supportplaner finns [den här sidan](https://azure.microsoft.com/support/plans/developer/).

- Om du redan har en Azure stöder planerar, [öppna ett supportärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Om du inte är en Azure-kund kan du också öppna en supportbegäran med Microsoft via [vår kommersiella support](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Du kan också försöka [våra virtuella agent](https://support.microsoft.com/contactus/?ws=support) att få support eller ställa frågor.

### <a name="free-chat-support-for-a-limited-time"></a>Ledigt chatt stöd för en begränsad tid

Du kan också använda vår chatt-stöd - som är gratis för Microsoft-Partners för en begränsad tid. Om ditt företag inte är en Microsoft-Partner kan du registrera den gratis och få andra fördelar genom att gå [här](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

När registrera ditt företag, kan du starta chatt begäran [här](https://aka.ms/devchat).