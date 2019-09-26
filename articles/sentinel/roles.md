---
title: Behörigheter i Azure Sentinel | Microsoft Docs
description: Den här artikeln förklarar hur Azure Sentinel använder rollbaserad åtkomst kontroll för att tilldela behörigheter till användare och identifierar tillåtna åtgärder för varje roll.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: 0bf95b499a7366dad1e7b78fa4298aa6a42bb5fe
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316790"
---
# <a name="permissions-in-azure-sentinel"></a>Behörigheter i Azure Sentinel

Azure Sentinel använder [rollbaserad Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md)för att tillhandahålla [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure.

Med RBAC kan du använda och skapa roller i din säkerhets åtgärds grupp för att ge lämplig åtkomst till Azure Sentinel. Baserat på rollerna har du detaljerad kontroll över vilka användare med åtkomst till Azure Sentinel som kan se. Du kan tilldela RBAC-roller i Azure Sentinel-arbetsytan direkt eller till en prenumeration eller resurs grupp som arbets ytan tillhör.

Det finns tre inbyggda Azure Sentinel-roller.  
**Alla inbyggda Azure Sentinel-roller ger Läs åtkomst till data i Azure Sentinel-arbetsytan.**
- **Azure Sentinel-läsare**: En användare som tilldelats rollen har visnings rättigheter till Azure Sentinel. Användaren kan visa incidenter och data, men kan inte göra ändringar.
- **Azure Sentinel-svarare**: En användare som tilldelats den här rollen kan läsa och utföra åtgärder för incidenter som tilldelning och allvarlighets grad ändringar.
- **Azure Sentinel-deltagare**: En användare som tilldelats den här rollen kan läsa och utföra åtgärder för incidenter och skapa och ta bort analys regler.

Förutom Azure Sentinel-dedikerade RBAC-roller finns det Azure och Log Analytics RBAC-roller som kan ge en bredare uppsättning behörigheter som inkluderar åtkomst till din Azure Sentinel-arbetsyta och andra resurser:

- **Azure-roller:** [Ägare](../role-based-access-control/built-in-roles.md#owner), [deltagare](../role-based-access-control/built-in-roles.md#contributor)och [läsare](../role-based-access-control/built-in-roles.md#reader). Azure-roller beviljar åtkomst över alla dina Azure-resurser, inklusive Log Analytics arbets ytor och Azure Sentinel-resurser.

-   **Log Analytics roller:** [Log Analytics läsaren](../role-based-access-control/built-in-roles.md#log-analytics-reader) [Log Analytics Contributor](../role-based-access-control/built-in-roles.md#log-analytics-contributor). Log Analytics roller beviljar åtkomst över alla dina Log Analytics arbets ytor. 

> [!NOTE]
> Log Analytics roller ger också Läs åtkomst för alla Azure-resurser, men tilldelar bara Skriv behörighet till Log Analytics resurser.


Till exempel kommer en användare som är tilldelad till rollerna **Azure Sentinel Reader** och **Azure Contributor** (inte **Azure Sentinel Contributor**) att kunna redigera data i Azure Sentinel, även om de bara har behörigheten **Sentinel Reader** . Om du vill ge behörighet till en enda i Azure Sentinel bör du därför noggrant ta bort den här användarens tidigare behörigheter och se till att du inte bryter alla nödvändiga behörighets roller för en annan resurs.

> [!NOTE]
>- Azure Sentinel använder spel böcker för automatiserat hot svar. Spel böcker utnyttjar Azure Logic Apps och är en separat Azure-resurs. Du kanske vill tilldela vissa medlemmar i din säkerhets åtgärds grupp med alternativet att använda Logic Apps för åtgärder för säkerhets dirigering, automation och respons (SOAR). Du kan använda [Logic app Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor) -rollen eller [Logic app-operatören](../role-based-access-control/built-in-roles.md#logic-app-operator) för att tilldela uttrycklig behörighet för att använda spel böcker.
>- Om du vill lägga till data anslutningar är de nödvändiga rollerna för varje koppling per kopplings typ och visas på den relevanta anslutnings sidan. För att kunna ansluta data källor måste du dessutom ha Skriv behörighet på Azure Sentinel-arbetsytan.



## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visas roller och tillåtna åtgärder i Azure Sentinel. Ett X visar att åtgärden tillåts för den rollen.

| Role | Skapa och kör spel böcker| Skapa och redigera instrument paneler, analys regler och andra Azure Sentinel-resurser | Hantera incidenter (Stäng, tilldela osv.) | Visa data, incidenter, instrument paneler och andra Azure Sentinel-resurser |
|--- |---|---|---|---|
| Azure Sentinel-läsare | -- | -- | -- | X |
| Azure Sentinel-svarare|--|--| X | X |
| Azure Sentinel-deltagare | -- | X | X | X |
| Azure Sentinel Contributor + Logic app-deltagare | X | X | X | X |


> [!NOTE]
> - Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Du kan till exempel tilldela rollen Azure Sentinel Contributor enbart till användare som behöver skapa regler eller instrument paneler.
> - Vi rekommenderar att du ställer in behörigheter för Azure Sentinel i resurs grupp omfånget, så att användaren kan ha åtkomst till alla Azure Sentinel-arbetsytor i samma resurs grupp.
>
## <a name="building-custom-rbac-roles"></a>Skapa anpassade RBAC-roller

Förutom, eller i stället för, med inbyggda RBAC-roller kan du skapa anpassade RBAC-roller för Azure Sentinel. Anpassade RBAC-roller för Azure Sentinel skapas på samma sätt som du skapar andra [anpassade Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) -roller, baserat på vissa behörigheter för Azure Sentinel-resurser.

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Avancerad RBAC för de data du lagrar i Azure Sentinel
  
Du kan använda Log Analytics avancerad rollbaserad åtkomst kontroll över data i Azure Sentinel-arbetsytan. Detta omfattar både rollbaserad åtkomst kontroll per datatyp och rollbaserad rollbaserad åtkomst kontroll. Mer information om Log Analytics-roller finns [i hantera loggdata och arbets ytor i Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du arbetar med roller för Azure Sentinel-användare och vad varje roll gör det möjligt för användare att göra.

* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
