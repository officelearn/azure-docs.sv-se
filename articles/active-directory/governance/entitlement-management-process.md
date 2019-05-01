---
title: Begär processen och e-postaviseringar i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Läs mer om processen för begäran för ett paket för åtkomst och när e-postmeddelanden skickas i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/26/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab18c8f165fc30636cd05091be1181743f9972d
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873663"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Begär processen och e-postaviseringar i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När en användare skickar en begäran om att ett åtkomst-paket, startas en process att leverera begäran. Hantering av Azure AD-rättigheten skickar också e-postmeddelanden till godkännare och beställare när viktiga händelser som inträffar under processen.

Den här artikeln beskriver processen för begäran och e-postmeddelanden som skickas.

## <a name="request-process"></a>Processen för begäran

En användare som behöver åtkomst till en åtkomst-paketet kan skicka en förfrågan. Beroende på konfigurationen av principen, kan förfrågan kräva ett godkännande. När en begäran har godkänts, börjar en process att tilldela användaråtkomst till varje resurs i åtkomst-paketet. Följande diagram visar en översikt över processen och de olika tillstånden.

![Processdiagram för godkännande](./media/entitlement-management-process/request-process.png)

| Status | Beskrivning |
| --- | --- |
| Skickat | Användare skickar en begäran. |
| Väntar på godkännande | Om principen för ett åtkomst-paket kräver godkännande, flyttas en begäran till väntar på godkännande. |
| Har upphört att gälla | Om inga godkännare gå igenom en begäran inom tidsgränsen för begäran om godkännande, förfrågan upphör att gälla. Om du vill försöka igen måste användaren skicka sina begäran igen. |
| Avvisad | Godkännare nekar en begäran. |
| Godkända | Godkännaren godkänner en begäran. |
| Levererar | Användaren har **inte** har tilldelats åtkomst till alla resurser i åtkomst-paketet. Om det här är en extern användare har ännu inte användaren nås resursbiblioteket och accepterat behörigheter prompten. |
| Levererade | Användaren har tilldelats åtkomst till alla resurser i åtkomst-paketet. |
| Utökad åtkomst | Om tillägg är tillåtna i principen, utökas tilldelningen av användaren. |
| Åtkomst har upphört att gälla | Användaråtkomst till paketet åtkomst har gått ut. För att få åtkomst igen, måste du skicka en begäran. |

## <a name="email-notifications"></a>E-postmeddelanden

Om du är godkännare kan skickas e-postaviseringar när du måste godkänna en åtkomstbegäran och när en åtkomstbegäran har slutförts. Om du är en begärande skickas e-postmeddelanden som indikerar statusen för din begäran. I följande diagram visas när dessa e-postmeddelanden skickas.

![Berättigande process för hantering av e-post](./media/entitlement-management-process/email-notifications.png)

I följande tabell finns mer information om var och en av dessa e-postmeddelanden.

| # | E-postämne | När de skickas | Skickas till |
| --- | --- | --- | --- |
| 1 | Åtgärd krävs: Granska åtkomstbegäran från *[begärande]* till *[åtkomst paket]* av *[date]* | När en begärande skickar en begäran om ett paket för åtkomst | Alla godkännare |
| 2 | Åtgärd krävs: Granska åtkomstbegäran från *[begärande]* till *[åtkomst paket]* av *[date]* | X dagar före godkännandet tidsgräns för begäran | Alla godkännare |
| 3 | Meddelande om återställningsstatus: *[begärande]*'s åtkomstbegäran till *[åtkomst paket]* har upphört att gälla | När godkännarna inte godkänna eller neka en åtkomstbegäran i varaktigheten för begäran | Beställare |
| 4 | Meddelande om återställningsstatus: *[begärande]* åtkomstbegäran till *[åtkomst paket]* har slutförts | När den första godkännaren godkänner eller nekar en åtkomstbegäran | Alla godkännare |
| 5 | Du har nekats åtkomst till *[åtkomst paket]* | När en begärande har nekats åtkomst till access-paketet | Beställare |
| 6 | Nu har du åtkomst till *[åtkomst paket]*  | När en begärande har beviljats åtkomst till alla resurser i åtkomst-paketet | Beställare |
| 7 | Din åtkomst till *[åtkomst paket]* går ut i X dagar | X dagar innan den begärandes åtkomst till paketet åtkomst går ut | Beställare |
| 8 | Din åtkomst till *[åtkomst paket]* har upphört att gälla | När den begärandes åtkomst till ett åtkomst-paket upphör att gälla | Beställare |

### <a name="review-access-request-emails"></a>Granska åtkomst begäran e-postmeddelanden

När en begärande skickar en förfrågan om en åtkomst-paket som har konfigurerats för att kräva godkännande kan få ett e-postmeddelande med information om begäran i alla godkännare som konfigurerats i principen. Information om omfattar beställarens namn, organisation, få åtkomst till start- och datum om affärsrelaterad motivering när begäran har skickats och när begäran att förfalla. E-postmeddelandet innehåller en länk där godkännarna kan godkänna eller neka förfrågan. Här är ett exempel e-postmeddelande som skickas till en godkännare när en begärande skickar en förfrågan.

![Granska åtkomst till e-post](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Godkända eller inte får e-postmeddelanden

Beställare meddelas när deras åtkomstbegäran är godkänd och tillgängliga för åtkomst, eller när deras åtkomstbegäran om nekas. När en godkännare går igenom en åtkomstbegäran som skickats av en begärande, kan de godkänna eller neka förfrågan. Godkännaren behöver lägga till en motivering till sina beslut.

När en åtkomstbegäran har godkänts, startar rättigheten management ge begärande-åtkomst till var och en av resurserna i åtkomst-paketet. Efter begäranden har beviljats åtkomst till alla resurser i åtkomst-paketet, skickas ett e-postmeddelande till frågeställaren att deras förfrågan har godkänts och att de har nu åtkomst till access-paketet. Här är ett exempel e-postmeddelande som skickas till en begärande när de har beviljats åtkomst till en åtkomst-paketet.

När en åtkomstbegäran om nekas, skickas ett e-postmeddelande till frågeställaren. Här är ett exempel e-postmeddelande som skickas till en begärande när deras åtkomstbegäran om nekas.

### <a name="expired-access-request-emails"></a>Har upphört att gälla åtkomst begäran e-postmeddelanden

Beställare meddelas när deras förfrågan har upphört att gälla. När en begärande skickar en åtkomstbegäran, har begäran en varaktighet för begäran som därefter upphör den. Om det finns inga godkännare som skickar ett Godkänn/neka beslut, fortsätter begäran att vara i tillståndet väntar på godkännande. När begäran når sin konfigurerade giltighetstid, kan begäran går ut och inte längre vara godkänns eller avvisas av godkännarna. I det här fallet hamnar begäran i upphört att gälla. En begäran om har upphört att gälla kan inte längre godkännas eller avvisas. Ett e-postmeddelande skickas till den begärande att deras förfrågan har upphört att gälla och att de behöver för att skicka förfrågan. Här är ett exempel e-postmeddelande som skickas till en begärande när deras förfrågan har upphört att gälla.

![Har upphört att gälla åtkomst till e-post](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett åtkomst-paket](entitlement-management-request-access.md)
- [Godkänn eller avvisa förfrågningar](entitlement-management-request-approve.md)
