---
title: Granskning och rapportering av en B2B-samarbets användare – Azure AD
description: Gäst användar egenskaper kan konfigureras i Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74273298"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Granskning och rapportering av en B2B-samarbets användare
Med gäst användare har du gransknings funktioner som liknar medlems användare. 

## <a name="access-reviews"></a>Åtkomstgranskningar
Du kan använda åtkomst granskningar för att regelbundet kontrol lera om gäst användare fortfarande behöver åtkomst till dina resurser. Funktionen **åtkomst granskningar** finns i **Azure Active Directory** under **Hantera** > **organisations relationer**. (Du kan också söka efter "åtkomst granskningar" från **alla tjänster** i Azure Portal.) Information om hur du använder åtkomst granskningar finns i [Hantera gäst åtkomst med åtkomst granskningar för Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Granskningsloggar

Azure AD audit-loggarna innehåller poster för system-och användar aktiviteter, inklusive aktiviteter som initieras av gäst användare. För att få åtkomst till gransknings loggar går du till **Azure Active Directory**, under **övervakning**, och väljer **gransknings loggar**. Här är ett exempel på Inbjudnings-och inlösnings historiken för oogle för inbjudna Sam:

![Skärm bild som visar och exempel på gransknings loggens utdata](./media/auditing-and-reporting/audit-log.png)

Du kan se var och en av dessa händelser för att få information. Låt oss till exempel titta på godkännande informationen.

![Skärm bild som visar och exempel på utdata från aktivitets information](./media/auditing-and-reporting/activity-details.png)

Du kan också exportera dessa loggar från Azure AD och använda det rapporterings verktyg som du väljer för att hämta anpassade rapporter.

### <a name="next-steps"></a>Nästa steg

- [Användaregenskaper för B2B-samarbete](user-properties.md)

