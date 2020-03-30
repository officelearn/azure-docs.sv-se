---
title: Begär process & meddelanden - Azure AD-berättigandehantering
description: Lär dig mer om begäran om ett åtkomstpaket och när e-postmeddelanden skickas i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128525"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Begär process- och e-postaviseringar i Azure AD-berättigandehantering

När en användare skickar en begäran till ett åtkomstpaket börjar en process leverera åtkomstbegäran. Hantering av Azure AD-berättigande skickar e-postmeddelanden till godkännare och beställare när viktiga händelser inträffar under processen. I den här artikeln beskrivs begäranden och de e-postmeddelanden som skickas.

## <a name="request-process"></a>Begär process

En användare som behöver åtkomst till ett åtkomstpaket kan skicka en åtkomstbegäran. Beroende på principens konfiguration kan begäran kräva ett godkännande. När en begäran godkänns börjar en process tilldela användaren åtkomst till varje resurs i åtkomstpaketet. Följande diagram visar en översikt över processen och de olika tillstånden:

![Diagram över godkännandeprocess](./media/entitlement-management-process/request-process.png)

| Status | Beskrivning |
| --- | --- |
| Skickat | Användaren skickar en begäran. |
| Väntar på godkännande | Om principen för ett åtkomstpaket kräver godkännande flyttas en begäran till väntande godkännande. |
| Upphörd | Om inga godkännare godkänner en begäran inom tidsgränsen för godkännandebegäran upphör begäran att gälla. Om du vill försöka igen måste användaren skicka in sin begäran igen. |
| Nekad | Godkännaren nekar en begäran. |
| Godkända | Godkännaren godkänner en begäran. |
| Leverera | Användaren har **inte** tilldelats åtkomst till alla resurser i åtkomstpaketet. Om detta är en extern användare kanske användaren inte har åtkomst till resurskatalogen ännu. De kanske inte heller har accepterat samtyckesprompten. |
| Levererade | Användaren har tilldelats åtkomst till alla resurser i åtkomstpaketet. |
| Åtkomst utökad | Om tillägg tillåts i principen utökade användaren tilldelningen. |
| Åtkomsten har upphört att gälla | Användarens åtkomst till åtkomstpaketet har upphört att gälla. För att få åtkomst igen måste användaren skicka en begäran. |

## <a name="email-notifications"></a>E-postmeddelanden

Om du är godkännare får du e-postmeddelanden när du behöver godkänna en åtkomstbegäran. Du får också meddelanden när en åtkomstbegäran har slutförts. Du får också e-postmeddelanden som anger status för din begäran om du är en beställare.

Följande diagram visar när dessa e-postmeddelanden skickas till antingen godkännarna eller beställaren. Referera till [tabellen e-postmeddelanden](entitlement-management-process.md#email-notifications-table) för att hitta motsvarande nummer till de e-postmeddelanden som visas i diagrammen.

### <a name="first-approvers-and-alternate-approvers"></a>Första godkännare och alternativa godkännare
I följande diagram visas upplevelsen av de första godkännare och alternativa godkännare och de e-postmeddelanden de får under begäran:

![Processflödet för första och alternativa godkännare](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Beställare
Följande diagram visar upplevelsen av beställare och de e-postmeddelanden de får under begäran:

![Processflöde för requestor](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Godkännande i två steg
Följande diagram visar upplevelsen av steg 1- och steg 2-godkännare och de e-postmeddelanden de får under begäran:

![Processflöde i två steg](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabell över e-postmeddelanden
I följande tabell finns mer information om var och en av dessa e-postmeddelanden. Om du vill hantera dessa e-postmeddelanden kan du använda regler. I Outlook kan du till exempel skapa regler för att flytta e-postmeddelandena till en mapp om ämnet innehåller ord från den här tabellen:

| # | E-postämne | När skickas | Skickas till |
| --- | --- | --- | --- |
| 1 | Åtgärd krävs: Godkänna eller neka vidarebefordrad begäran senast *[datum]* | Det här e-postmeddelandet skickas till steg 1-alternativa godkännare (efter att begäran har eskalerats) för att vidta åtgärder. | Alternativ godkännare för steg 1 |
| 2 | Åtgärd krävs: Godkänna eller neka begäran senast *[datum]* | Det här e-postmeddelandet skickas till den första godkännaren, om eskaleringen är inaktiverad, för att vidta åtgärder. | Första godkännaren |
| 3 | Påminnelse: Godkänn eller neka begäran senast *[datum]* för *[beställare]* | Det här e-postmeddelandet för påminnelse skickas till den första godkännaren om eskaleringen är inaktiverad. E-postmeddelandet ber dem att vidta åtgärder om de inte har gjort det. | Första godkännaren |
| 4 | Godkänna eller neka begäran senast *[tid]* på *[datum]* | Det här e-postmeddelandet skickas till den första godkännaren (om eskalering är aktiverad) för att vidta åtgärder. | Första godkännaren |
| 5 | Åtgärd krävs påminnelse: Godkänna eller neka begäran senast *[datum]* för *[beställare]* | Det här e-postmeddelandet för påminnelse skickas till den första godkännaren om eskalering är aktiverat. E-postmeddelandet ber dem att vidta åtgärder om de inte har gjort det. | Första godkännaren |
| 6 | Begäran har upphört att gälla för *[access_package]* | Det här e-postmeddelandet skickas till den första godkännaren och steg-1-alternativa godkännare efter att begäran har upphört att gälla. | Första godkännare, steg-1 alternativa godkännare |
| 7 | Begäran godkänd för *[beställare]* till *[access_package]* | Detta e-postmeddelande kommer att skickas till den första godkännaren och steg-1 alternativa godkännare på begäran slutföras. | Första godkännare, steg-1 alternativa godkännare |
| 8 | Begäran godkänd för *[beställare]* till *[access_package]* | Det här e-postmeddelandet skickas till den första godkännaren och steg-1-alternativa godkännare av en 2-stegsbegäran när steg-1-begäran godkänns. | Första godkännare, steg-1 alternativa godkännare |
| 9 | Begäran nekad *[access_package]* | Detta e-postmeddelande kommer att skickas till beställaren när deras begäran avslås | Requestor |
| 10 | Din begäran har upphört att gälla för *[access_package]* | Detta e-postmeddelande kommer att skickas till beställaren i slutet av en enda eller 2-stegs begäran. E-postmeddelandet meddelar beställaren att begäran har upphört att gälla. | Requestor |
| 11 | Åtgärd krävs: Godkänna eller neka begäran senast *[datum]* | Det här e-postmeddelandet skickas till den andra godkännaren, om eskaleringen är inaktiverad, för att vidta åtgärder. | Andra godkännaren |
| 12 | Åtgärd krävs påminnelse: Godkänna eller neka begäran senast *[datum]* | Det här e-postmeddelandet för påminnelse skickas till den andra godkännaren om eskaleringen är inaktiverad. Meddelandet ber dem att vidta åtgärder om de inte har ännu. | Andra godkännaren |
| 13 | Åtgärd krävs: Godkänna eller neka begäran senast *[datum]* för *[beställare]* | Det här e-postmeddelandet skickas till den andra godkännaren, om eskalering är aktiverad, för att vidta åtgärder. | Andra godkännaren |
| 14 | Åtgärd krävs påminnelse: Godkänna eller neka begäran senast *[datum]* för *[beställare]* | Det här e-postmeddelandet för påminnelse skickas till den andra godkännaren om eskaleringen är aktiverad. Meddelandet ber dem att vidta åtgärder om de inte har ännu. | Andra godkännaren |
| 15 | Åtgärd krävs: Godkänna eller neka vidarebefordrad begäran senast *[datum]* | Det här e-postmeddelandet skickas till alternativa godkännare i steg 2, om eskalering är aktiverad, för att vidta åtgärder. | Steg-2 alternativa godkännare |
| 16 | Begäran godkänd för *[beställare]* till *[access_package]* | Detta e-postmeddelande kommer att skickas till den andra godkännaren och steg-2 alternativa godkännare när du godkänner begäran. | Andra godkännare, steg-2-alternativeämnden |
| 17 | En begäran har upphört att gälla för *[access_package]* | Det här e-postmeddelandet skickas till den andra godkännaren eller alternativa godkännare när begäran har upphört att gälla. | Andra godkännare, steg-2 alternativa godkännare |
| 18 | Du har nu tillgång till *[access_package]* | Det här e-postmeddelandet skickas till slutanvändarna för att börja använda deras åtkomst. | Requestor |
| 19 | Utöka åtkomsten för *[access_package]* *senast [datum]* | Det här e-postmeddelandet skickas till slutanvändarna innan åtkomsten går ut. | Requestor |
| 20 | Åtkomsten har upphört för *[access_package]* | Det här e-postmeddelandet skickas till slutanvändarna när åtkomsten har gått ut. | Requestor |

### <a name="access-request-emails"></a>Få tillgång till e-postmeddelanden för begäran

När en beställare skickar en åtkomstbegäran för ett åtkomstpaket som konfigurerats för att kräva godkännande, får alla godkännare som läggs till i principen ett e-postmeddelande med information om begäran. Informationen i e-postmeddelandet inkluderar: beställarens namnorganisation och affärsmotivering; och det begärda start- och slutdatumet (om sådan finns). Detaljerna kommer också att innehålla när begäran lämnades in och när begäran upphör att gälla.

E-postmeddelandet innehåller en länk godkännare kan klicka på för att gå till Min åtkomst för att godkänna eller neka åtkomstbegäran. Här är ett exempel på e-postmeddelande som skickas till den första godkännaren eller den andra godkännaren (om tvåstegsgodkännande är aktiverat) för att slutföra en åtkomstbegäran:

![Godkänna begäran om åtkomst till paket-e-post](./media/entitlement-management-shared/approver-request-email.png)

Godkännare kan också få ett påminnelsemeddelande. E-postmeddelandet ber godkännaren att fatta ett beslut om begäran. Här är ett exempel e-postmeddelande som godkännaren får för att påminna dem om att vidta åtgärder:

![E-post för begäran om påminnelseåtkomst](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternativa godkännare begär e-postmeddelanden

Om inställningen för alternativa godkännare är aktiverad och begäran fortfarande väntar, vidarebefordras den. Alternativa godkännare får ett e-postmeddelande för att godkänna eller neka begäran. Du kan aktivera alternativa godkännare i steg-1 och steg-2. Här är ett exempel på e-postmeddelandet som de alternativa godkännare får:

![Alternativa godkännare begär e-post](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Både godkännaren och de alternativa godkännarna kan godkänna eller neka begäran.

### <a name="approved-or-denied-emails"></a>Godkända eller nekade e-postmeddelanden

 När en godkännare tar emot en åtkomstbegäran som skickats av en beställare kan de godkänna eller neka åtkomstbegäran. Godkännaren måste lägga till en affärsmotivering för sitt beslut. Här är ett exempel e-postmeddelande som skickas till godkännare och alternativa godkännare efter en begäran har godkänts:

![Godkänd begäran om åtkomst till paket-e-post](./media/entitlement-management-process/approver-request-email-approved.png)

När en åtkomstbegäran godkänns och deras åtkomst har etablerats skickas ett e-postmeddelande till beställaren att de nu har åtkomst till åtkomstpaketet. Här är ett exempel på e-postmeddelande som skickas till en beställare när de beviljas åtkomst till ett åtkomstpaket:

![E-postmeddelande med e-post för begäran om godkänd begäran om begäran om begäran om begäran om begäran](./media/entitlement-management-process/requestor-email-approved.png)

När en åtkomstbegäran nekas skickas ett e-postmeddelande till beställaren. Här är ett exempel e-postmeddelande som skickas till en beställare när deras åtkomstbegäran nekas:

![Begäran om begäran nekad e-post](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>E-postmeddelanden för begäran om godkännandeåtkomst i två steg

Om tvåstegsgodkännande är aktiverat måste minst två godkännare godkänna begäran, en från varje steg, innan beställaren kan få åtkomst.

Under steg-1 får den första godkännaren e-postmeddelandet med åtkomstbegäran och fattar ett beslut. Om de godkänner begäran får alla första godkännare och alternativa godkännare i steg 1 (om eskalering är aktiverad) ett meddelande om att steg-1 är klar. Här är ett exempel på e-postmeddelandet som skickas när steg-1 är klar:

![E-post för åtkomstbegäran i två steg](./media/entitlement-management-process/approver-request-email-2stage.png)

När de första eller alternativa godkännarna har godkänt begäran i steg-1 börjar steg-2. Under steg-2 får den andra godkännaren e-postmeddelandet med åtkomstbegäran. När den andra godkännaren eller suppleanten i steg 2 (om eskalering är aktiverad) bestämmer sig för att godkänna eller neka begäran skickas e-postmeddelanden till den första och andra godkännaren och alla alternativa godkännare i steg-1 och steg-2, samt beställaren.

### <a name="expired-access-request-emails"></a>E-postmeddelanden för utgångna åtkomstbegäran

Åtkomstbegäranden kan upphöra att gälla om ingen godkännare har godkänt eller nekat begäran. 

När begäran når sitt konfigurerade utgångsdatum och upphör att gälla kan den inte längre godkännas eller nekas av godkännarna. Här är ett exempel på e-postmeddelandet som skickas till alla de första, andra (om 2-stegs godkännande är aktiverat) och alternativa godkännare:

![E-post för e-post för godkännare har upphört att gälla](./media/entitlement-management-process/approver-request-email-expired.png)

Ett e-postmeddelande skickas också till beställaren och meddelar dem att deras åtkomstbegäran har upphört att gälla och att de måste skicka åtkomstbegäran igen. Följande diagram visar upplevelsen av beställaren och de e-postmeddelanden de får när de begär att få utöka åtkomsten:

![Begärandetorn utökar åtkomstprocessflödet](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Här är ett exempel på e-postmeddelande som skickas till en beställare när deras åtkomstbegäran har upphört att gälla:

![Begärande begäran om utgångna åtkomstbegäran e-post](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett åtkomstpaket](entitlement-management-request-access.md)
- [Godkänna eller neka åtkomstbegäranden](entitlement-management-request-approve.md)
