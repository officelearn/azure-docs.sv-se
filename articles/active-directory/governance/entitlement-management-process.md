---
title: Begär process-och e-postaviseringar i Azure AD-hantering av rättigheter – Azure Active Directory
description: Läs om process processen för ett Access-paket och när e-postmeddelanden skickas i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199909"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Begär process-och e-postaviseringar i hantering av Azure AD-berättigande

När en användare skickar en begäran till ett Access-paket börjar processen att leverera denna åtkomstbegäran. Hantering av Azure AD-berättigande skickar e-postaviseringar till god kännare och beställare när viktiga händelser inträffar under processen. Den här artikeln beskriver begär ande processen och de e-postmeddelanden som skickas.

## <a name="request-process"></a>Begär process

En användare som behöver åtkomst till ett Access-paket kan skicka en åtkomstbegäran. Beroende på principens konfiguration kan begäran kräva ett godkännande. När en begäran godkänns börjar en process tilldela användaren åtkomst till varje resurs i åtkomst paketet. Följande diagram visar en översikt över processen och de olika tillstånden:

![Diagram över godkännande process](./media/entitlement-management-process/request-process.png)

| Status | Beskrivning |
| --- | --- |
| Läggs | Användaren skickar en begäran. |
| Väntar på godkännande | Om principen för ett Access-paket kräver godkännande flyttas en begäran till väntar på godkännande. |
| Upphörd | Om inga god kännare godkänner en begäran inom tids gränsen för godkännandebegäran, förfaller begäran. Användaren måste skicka in begäran igen för att försöka igen. |
| Tillåts | God kännaren nekar en begäran. |
| Godkända | God kännaren godkänner en begäran. |
| Kvalitativ | Användaren har **inte** tilldelats åtkomst till alla resurser i åtkomst paketet. Om det är en extern användare kanske användaren ännu inte har åtkomst till resurs katalogen och godkänt medgivande meddelandet. |
| Levererade | Användaren har tilldelats åtkomst till alla resurser i åtkomst paketet. |
| Utökad åtkomst | Användaren utökat tilldelningen om tillägg tillåts i principen. |
| Åtkomst upphör | Användarens åtkomst till Access-paketet har upphört att gälla. Användaren måste skicka en begäran för att få åtkomst igen. |

## <a name="email-notifications"></a>E-postmeddelanden

Om du är en god kännare skickas e-postaviseringar när du behöver godkänna en åtkomstbegäran och när en åtkomstbegäran har slutförts. Om du är en begär Ande, skickas e-postaviseringar som anger status för din begäran.

Följande diagram visar när de här e-postaviseringarna skickas till god kännarna eller för beställaren. Referera till [tabellen med e-postmeddelanden](entitlement-management-process.md#email-notifications-table) för att hitta motsvarande nummer till e-postaviseringarna som visas i diagrammen.

### <a name="primary-approvers-and-alternate-approvers"></a>Primära god kännare och alternativa god kännare
Följande diagram visar erfarenheten av primära god kännare och alternativa god kännare, och de e-postmeddelanden som de får när de begär processen:

![Process flöde för primära och alternativa god kännare](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Beställare
Följande diagram visar upplevelsen av beställare och de e-postmeddelanden som de får när de begär processen:

![Process flöde för beställare](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>Tabellen e-postavisering
Följande tabell innehåller mer information om var och en av dessa e-postaviseringar. Du kan använda regler för att hantera dessa e-postmeddelanden. I Outlook kan du till exempel skapa regler för att flytta e-postmeddelanden till en mapp om ämnet innehåller ord från den här tabellen:

| # | E-postämne | När det skickas | Skickat till |
| --- | --- | --- | --- |
| 1 | Åtgärd krävs: Godkänn eller neka vidarebefordrad begäran senast *[datum]* | Det här e-postmeddelandet skickas till alternativa god kännare i steg 1 (när begäran har eskalerats) för att vidta åtgärder. | Steg-1 alternativ god kännare |
| 2 | Åtgärd krävs: Godkänn eller neka begäran senast *[datum]* | Det här e-postmeddelandet skickas till primära god kännare i steg 1, om eskalering är inaktive rad, för att vidta åtgärder. | Steg 1 primär god kännare |
| 3 | Påminnelse: Godkänn eller neka begäran senast *[datum]* för *[begär ande]* | E-postpåminnelsen skickas till mellanliggande primär god kännare, om eskalering är inaktive rad, för att vidta åtgärder, endast när de inte har vidtagit åtgärder. | Steg 1 primär god kännare |
| 4 | Godkänn eller neka begäran senast *[tid]* den *[datum]* | Det här e-postmeddelandet skickas till primära god kännare i steg 1 (om eskalering är aktiverat) för att vidta åtgärder. | Steg 1 primär god kännare |
| 5 | Åtgärd krävs påminnelse: Godkänn eller neka begäran senast *[datum]* för *[begär ande]* | E-postpåminnelsen skickas till mellanliggande primär god kännare, om eskalering är aktiverat, för att vidta åtgärder, endast när de inte har vidtagit åtgärder. | Steg 1 primär god kännare |
| 6 | Begäran har upphört att gälla för *[access_package]* | Det här e-postmeddelandet skickas till primära god kännare och/eller mellanliggande god kännare för steg 1, i en enskild fas eller begäran om flera steg efter att begäran har upphört att gälla. | Steg 1 primär god kännare, steg 1 alternativ god kännare |
| 7 | Begäran godkänd för *[beställare]* till *[access_package]* | Det här e-postmeddelandet skickas till de huvudsakliga god kännarna och/eller den alternativa god kännaren för fas 1 när en begäran har slutförts. | Steg 1 primär god kännare, steg 1 alternativ god kännare |
| 8 | Begäran godkänd för *[beställare]* till *[access_package]* | Det här e-postmeddelandet skickas till de huvudsakliga god kännarna och/eller de alternativa god kännarna i steg 1 för en 2-fas-begäran, endast när steg 1 godkänns. | Steg 1 primär god kännare, steg 1 alternativ god kännare |
| 9 | Begäran nekades till *[access_package]* | Det här e-postmeddelandet skickas endast till beställaren när begäran nekas | Begär Ande |
| 10 | Din begäran har upphört att gälla för *[access_package]* | Det här e-postmeddelandet skickas till beställaren i slutet av steg 1, i en enskild fas eller begäran i flera steg, efter att begäran har upphört att gälla. | Begär Ande |
| 18 | Du har nu åtkomst till *[access_package]* | Det här e-postmeddelandet skickas till slutanvändarna för att börja använda sin åtkomst. | Begär Ande |
| 19 | Utöka åtkomsten för *[access_package]* senast *[datum]* | Det här e-postmeddelandet skickas till slutanvändarna innan deras åtkomst upphör att gälla. | Begär Ande |
| 20 | Åtkomst har slutförts för *[access_package]* | Det här e-postmeddelandet skickas till slutanvändarna när deras åtkomst upphör att gälla. | Begär Ande |

### <a name="access-request-emails"></a>E-postmeddelanden för åtkomstbegäran

När en begär ande skickar en åtkomstbegäran för ett Access-paket som kon figurer ATS för att kräva godkännande får alla god kännare som läggs till i principen ett e-postmeddelande med information om begäran. Information omfattar beställarens namn, organisation, start-och slutdatum (om det finns), affärs justering, när begäran skickades och när begäran upphör att gälla.

E-postmeddelandet innehåller en länk god kännare kan klicka på för att gå till åtkomsten för att godkänna eller neka åtkomst förfrågan. Här är ett exempel på ett e-postmeddelande som skickas till en god kännare när en begär ande skickar en åtkomstbegäran:

![Godkänn begäran om åtkomst till paket e-post](./media/entitlement-management-shared/approver-request-email.png)

De primära god kännarna skickas också ett e-postmeddelande med en påminnelse om att vidta åtgärder och fatta beslut för begäran. Här är ett exempel på ett e-postmeddelande med en aviserings primär god kännare som får påminna dem om att vidta åtgärder:

![Påminnelse-e-postmeddelande om åtkomstbegäran](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Alternativa god kännare begär e-post

Om vidarebefordran till alternativa god kännare har Aktiver ATS, enligt vidarebefordrings principen, om begäran fortfarande väntar, vidarebefordras begäran. Den alternativa god kännaren får ett e-postmeddelande om att godkänna eller neka begäran. Här är ett exempel på ett meddelande om att alternativa god kännare får:

![Alternativt e-postbegäran om god kännare](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Både de primära god kännarna och de alternativa god kännarna kan godkänna eller neka begäran.

### <a name="approved-or-denied-emails"></a>Godkända eller nekade e-postmeddelanden

Fråge ställare meddelas när deras åtkomstbegäran godkänns och är tillgänglig för åtkomst, eller när deras åtkomst förfrågan nekas. När en god kännare får en åtkomstbegäran som skickats av en begär Ande, kan han eller hon godkänna eller neka åtkomst förfrågan. God kännaren måste lägga till en affärs motivering för sitt beslut. Här är ett exempel på ett e-postmeddelande som skickas till primära eller alternativa god kännare efter att en begäran har godkänts:

![Granska åtkomstbegäran via e-post](./media/entitlement-management-process/approver-request-email-approved.png)

När en åtkomstbegäran godkänns, och deras åtkomst är etablerad, skickas ett e-postmeddelande till den begär Ande som nu har åtkomst till Access-paketet. Här är ett exempel på ett e-postmeddelande som skickas till en begär Ande när de beviljas åtkomst till ett Access-paket:

![E-postbegäran om utgången åtkomst](./media/entitlement-management-process/requestor-email-approved.png)

När en åtkomstbegäran nekas skickas ett e-postmeddelande till beställaren. Här är ett exempel på ett e-postmeddelande som skickas till en beställare när deras åtkomstbegäran nekas:

![Begär ande nekad e-post](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>Utgångna e-postbegäran

Åtkomst begär Anden kan upphöra att gälla om ingen god kännare har godkänt eller nekat begäran. 

När begäran når sitt konfigurerade förfallo datum och upphör att gälla, kan den inte längre godkännas eller nekas av god kännaren. Här är ett exempel på ett e-postmeddelande om meddelandet som skickas till alla primära och alternativa god kännare:

 ![Ett e-postmeddelande om förfallo datum för god kännare](./media/entitlement-management-process/approver-request-email-expired.png)

 Ett e-postmeddelande skickas också till beställaren, vilket meddelar dem att deras åtkomst förfrågan har upphört att gälla och att de behöver skicka åtkomstbegäran igen. Här är ett exempel på ett e-postmeddelande som skickas till en beställare när deras åtkomst förfrågan har upphört att gälla:

![Begär ande e-postadress för begäran om åtkomst](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett Access-paket](entitlement-management-request-access.md)
- [Godkänn eller neka åtkomst begär Anden](entitlement-management-request-approve.md)
