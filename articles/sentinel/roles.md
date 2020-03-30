---
title: Behörigheter i Azure Sentinel | Microsoft-dokument
description: I den här artikeln beskrivs hur Azure Sentinel använder rollbaserad åtkomstkontroll för att tilldela behörigheter till användare och identifierar tillåtna åtgärder för varje roll.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: yelevin
ms.openlocfilehash: e7629a53190433c6c331ce372476b0ed768fc5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587183"
---
# <a name="permissions-in-azure-sentinel"></a>Behörigheter i Azure Sentinel

Azure Sentinel använder [rollbaserad åtkomstkontroll(RBAC)](../role-based-access-control/role-assignments-portal.md)för att tillhandahålla [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas användare, grupper och tjänster i Azure.

Med RBAC kan du använda och skapa roller inom säkerhetsoperationsteamet för att ge lämplig åtkomst till Azure Sentinel. Baserat på rollerna har du finkornig kontroll över vad användare med åtkomst till Azure Sentinel kan se. Du kan tilldela RBAC-roller i Azure Sentinel-arbetsytan direkt eller till en prenumeration eller resursgrupp som arbetsytan tillhör.

Det finns tre specifika inbyggda Azure Sentinel-roller.  
**Alla Azure Sentinel-inbyggda roller ger läsbehörighet till data i din Azure Sentinel-arbetsyta.**
- [Azure Sentinel-läsare](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel-responder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel-medarbetare](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Förutom Azure Sentinel-dedikerade RBAC-roller finns det Azure- och Log Analytics RBAC-roller som kan ge en bredare uppsättning behörigheter som inkluderar åtkomst till din Azure Sentinel-arbetsyta och andra resurser:

- **Azure-roller:** [Ägare,](../role-based-access-control/built-in-roles.md#owner) [deltagare](../role-based-access-control/built-in-roles.md#contributor)och [läsare](../role-based-access-control/built-in-roles.md#reader). Azure-roller ger åtkomst över alla dina Azure-resurser, inklusive Log Analytics-arbetsytor och Azure Sentinel-resurser.

-   **Logganalysroller:** [Log Analytics-deltagare](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [Log Analytics-läsare](../role-based-access-control/built-in-roles.md#log-analytics-reader). Logganalysroller ger åtkomst över alla dina Log Analytics-arbetsytor. 

> [!NOTE]
> Log Analytics-roller ger också läsbehörighet för alla Azure-resurser, men tilldelar bara skrivbehörighet till Log Analytics-resurser.


En användare som till exempel har tilldelats med **Azure Sentinel-läsare** och **Azure-deltagare** (inte **Azure Sentinel-deltagare)** kan redigera data i Azure Sentinel, även om de bara har **Behörigheter för Sentinel-läsare.** Om du vill bevilja behörigheter till en endast i Azure Sentinel bör du därför noggrant ta bort den här användarens tidigare behörigheter och se till att du inte bryter någon nödvändig behörighetsroll för en annan resurs.

> [!NOTE]
>- Azure Sentinel använder spelböcker för automatisk hotrespons. Spelböcker utnyttjar Azure Logic Apps och är en separat Azure-resurs. Du kanske vill tilldela specifika medlemmar i säkerhetsoperationsteamet möjlighet att använda Logic Apps för säkerhetsorkestrering, automatisering och svar (SOAR). Du kan använda rollen [Logic App-deltagare](../role-based-access-control/built-in-roles.md#logic-app-contributor) eller operatorrollen [Logic App](../role-based-access-control/built-in-roles.md#logic-app-operator) för att tilldela explicit behörighet för att använda spelböcker.
>- Om du vill lägga till datakopplingar är de nödvändiga rollerna för varje koppling per kopplingstyp och listade på relevant kopplingssida. Dessutom måste du ha skrivbehörighet på Arbetsytan Azure Sentinel för att kunna ansluta en datakälla.



## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visas roller och tillåtna åtgärder i Azure Sentinel. Ett X anger att åtgärden är tillåten för den rollen.

| Roll | Skapa och köra spelböcker| Skapa och redigera instrumentpaneler, analytiska regler och andra Azure Sentinel-resurser | Hantera incidenter (avvisa, tilldela osv.) | Visa data, incidenter, instrumentpaneler och andra Azure Sentinel-resurser |
|--- |---|---|---|---|
| Azure Sentinel-läsare | -- | -- | -- | X |
| Azure Sentinel-responder|--|--| X | X |
| Azure Sentinel-medarbetare | -- | X | X | X |
| Azure Sentinel-deltagare + Logic App-medarbetare | X | X | X | X |


> [!NOTE]
> - Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Tilldela till exempel azure sentinel-deltagarrollen endast till användare som behöver skapa regler eller instrumentpaneler.
> - Vi rekommenderar att du anger behörigheter för Azure Sentinel i resursgruppomfånget, så att användaren kan få åtkomst till alla Azure Sentinel-arbetsytor i samma resursgrupp.
>
## <a name="building-custom-rbac-roles"></a>Skapa anpassade RBAC-roller

Förutom, eller i stället för, med hjälp av inbyggda RBAC-roller, kan du skapa anpassade RBAC-roller för Azure Sentinel. Anpassade RBAC-roller för Azure Sentinel skapas på samma sätt som du skapar andra [anpassade Azure RBAC-roller,](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) baserat på [specifika behörigheter till Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) och Azure Log [Analytics-resurser](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Avancerad RBAC för de data du lagrar i Azure Sentinel
  
Du kan använda den avancerade rollbaserade åtkomstkontrollen för Logganalys över data på din Azure Sentinel-arbetsyta. Detta inkluderar både rollbaserad åtkomstkontroll per datatyp och resurscentrerad rollbaserad åtkomstkontroll. Mer information om Logganalysroller finns [i Hantera loggdata och arbetsytor i Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig hur du arbetar med roller för Azure Sentinel-användare och vad varje roll gör det möjligt för användare att göra.

* [Azure Sentinel Blogg](https://aka.ms/azuresentinelblog). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
