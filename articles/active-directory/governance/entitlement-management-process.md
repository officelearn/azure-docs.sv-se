---
title: Begär process-och e-postaviseringar i Azure AD-hantering av rättigheter (för hands version) – Azure Active Directory
description: Lär dig mer om process processen för ett Access-paket och när e-postmeddelanden skickas i Azure Active Directory rättighets hantering (för hands version).
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
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ad17f4c4d338e1d0b8118c1a172e0aaad7118e
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489103"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Begär process och e-postmeddelanden i hantering av Azure AD-hantering (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När en användare skickar en begäran till ett Access-paket, startas en process för att leverera denna begäran. Hantering av Azure AD-behörighet skickar också e-postaviseringar till god kännare och beställare när viktiga händelser inträffar under processen.

Den här artikeln beskriver processen för begäran och de e-postmeddelanden som skickas.

## <a name="request-process"></a>Begär process

En användare som behöver åtkomst till ett Access-paket kan skicka en åtkomstbegäran. Beroende på principens konfiguration kan begäran kräva ett godkännande. När en begäran godkänns börjar en process tilldela användaren åtkomst till varje resurs i åtkomst paketet. Följande diagram visar en översikt över processen och de olika tillstånden.

![Diagram över godkännande process](./media/entitlement-management-process/request-process.png)

| Tillstånd | Beskrivning |
| --- | --- |
| Skickat | Användaren skickar en begäran. |
| Väntar på godkännande | Om principen för ett Access-paket kräver godkännande flyttas en begäran till väntar på godkännande. |
| Har upphört att gälla | Om inga god kännare godkänner en begäran inom tids gränsen för godkännandebegäran, förfaller begäran. Användaren måste skicka in begäran igen för att försöka igen. |
| Avvisad | God kännaren nekar en begäran. |
| GODKÄND | God kännaren godkänner en begäran. |
| Levererar | Användaren har **inte** tilldelats åtkomst till alla resurser i åtkomst paketet. Om det är en extern användare har användaren ännu inte till gång till resurs katalogen och accepterade behörighets frågan. |
| Levererade | Användaren har tilldelats åtkomst till alla resurser i åtkomst paketet. |
| Utökad åtkomst | Användaren utökat tilldelningen om tillägg tillåts i principen. |
| Åtkomst upphör | Användarens åtkomst till Access-paketet har upphört att gälla. Användaren måste skicka en begäran för att få åtkomst igen. |

## <a name="email-notifications"></a>E-postmeddelanden

Om du är en god kännare skickas e-postaviseringar när du behöver godkänna en åtkomstbegäran och när en åtkomstbegäran har slutförts. Om du är en begär Ande, skickas e-postaviseringar som anger status för din begäran. Följande diagram visar när de här e-postaviseringarna skickas.

![E-postprocessen för rättighets hantering](./media/entitlement-management-process/email-notifications.png)

Följande tabell innehåller mer information om var och en av dessa e-postaviseringar.

| # | E-postämne | När det skickas | Skickat till |
| --- | --- | --- | --- |
| 1 | Åtgärd krävs: Granska åtkomstbegäran från *[Reber]* till *[Access Package]* senast *[datum]* | När en beställare skickar en begäran om ett Access-paket | Alla god kännare |
| 2 | Åtgärd krävs: Granska åtkomstbegäran från *[Reber]* till *[Access Package]* senast *[datum]* | X dagar före tids gränsen för begäran om godkännande | Alla god kännare |
| 3 | Status meddelande: *[begär ande]* åtkomst förfrågan till *[Access-paket]* har upphört att gälla | När god kännare inte godkänner eller nekar en åtkomstbegäran inom varaktigheten för begäran | Beställare |
| 4 | Status meddelande: *[refrågar]* åtkomstbegäran till *[Access Package]* har slutförts | När den första god kännaren godkänner eller nekar en åtkomstbegäran | Alla god kännare |
| 5 | Du har nekats åtkomst till *[Access Package]* | När en begär ande har nekats åtkomst till Access-paketet | Beställare |
| 6 | Du har nu åtkomst till *[Access Package]*  | När en begär ande har beviljats åtkomst till alla resurser i åtkomst paketet | Beställare |
| 7 | Din åtkomst till *[Access Package]* upphör att gälla om X dag (ar) | X dagar innan begär ande åtkomst till åtkomst paketet upphör att gälla | Beställare |
| 8 | Din åtkomst till *[Access Package]* har upphört att gälla | När beställarens åtkomst till ett Access-paket upphör att gälla | Beställare |

### <a name="access-request-emails"></a>E-postmeddelanden för åtkomstbegäran

När en begär ande skickar en åtkomstbegäran för ett Access-paket som har kon figurer ATS för att kräva godkännande får alla god kännare som kon figurer ATS i principen ett e-postmeddelande med information om begäran. Information omfattar beställarens namn, organisation, start-och slutdatum om det tillhandahålls, affärs justering, när begäran skickades och när begäran upphör att gälla. E-postmeddelandet innehåller en länk där god kännare kan godkänna eller neka åtkomst förfrågan. Här är ett exempel på ett e-postmeddelande som skickas till en god kännare när en begär ande skickar en åtkomstbegäran.

![Granska åtkomstbegäran via e-post](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Godkända eller nekade e-postmeddelanden

Fråge ställare meddelas när deras åtkomstbegäran godkänns och är tillgänglig för åtkomst, eller när deras åtkomst förfrågan nekas. När en god kännare får en åtkomstbegäran som skickats av en begär Ande, kan han eller hon godkänna eller neka åtkomst förfrågan. God kännaren måste lägga till en affärs motivering för sitt beslut.

När en åtkomstbegäran godkänns startar rättighets hanterings processen med att bevilja begär ande åtkomst till var och en av resurserna i åtkomst paketet. När beställaren har beviljats åtkomst till alla resurser i åtkomst paketet skickas ett e-postmeddelande till beställaren om att deras åtkomstbegäran har godkänts och att de nu har åtkomst till Access-paketet. Här är ett exempel på ett e-postmeddelande som skickas till en beställare när de beviljas åtkomst till ett Access-paket.

När en åtkomstbegäran nekas skickas ett e-postmeddelande till beställaren. Här är ett exempel på ett e-postmeddelande som skickas till en beställare när deras åtkomstbegäran nekas.

### <a name="expired-access-request-emails"></a>Utgångna e-postbegäran

Begär Anden meddelas när deras åtkomst förfrågan har upphört att gälla. När en begär ande skickar en åtkomstbegäran, har begäran en varaktighet efter vilken den upphör att gälla. Om det inte finns några god kännare som skickar ett godkännande/neka-beslut fortsätter begäran att förbli i ett tillstånd som väntar på godkännande. När begäran når sin konfigurerade förfallo tid går begäran ut och kan inte längre godkännas eller nekas av god kännaren. I det här fallet hamnar begäran i ett tillstånd som har gått ut. En utgången begäran kan inte längre godkännas eller nekas. Ett e-postmeddelande skickas till beställaren om att deras åtkomstbegäran har upphört att gälla och att de behöver skicka åtkomstbegäran igen. Här är ett exempel på ett e-postmeddelande som skickas till en beställare när deras åtkomst förfrågan har upphört att gälla.

![E-postbegäran om utgången åtkomst](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Nästa steg

- [Begär åtkomst till ett Access-paket](entitlement-management-request-access.md)
- [Godkänn eller neka åtkomst begär Anden](entitlement-management-request-approve.md)
