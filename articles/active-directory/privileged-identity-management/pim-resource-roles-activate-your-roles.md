---
title: Aktivera roller för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du aktiverar roller i PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 483f30a6546cf21d93b96b08eede6afae828d9e4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548312"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Aktivera roller för Azure-resurser med hjälp av Privileged Identity Management
Privileged Identity Management (PIM) introducerar en ny upplevelse i Aktivera roller för Azure-resurser. Berättigade rollmedlemmar kan schemalägga aktivering för ett framtida datum och tid. De kan också välja en specifik aktiveringsvaraktighet inom den Maxtid (konfigureras av administratörer). Mer information finns i [aktivera eller inaktivera roller i Azure AD Privileged Identity Management](pim-how-to-activate-role.md).

## <a name="activate-roles"></a>Aktivera roller
Bläddra till den **Mina roller** avsnitt i den vänstra rutan. Välj **aktivera** för den roll som du vill aktivera.

![Fliken ”berättigade roller” den ”Mina roller”-rutan.](media/azure-pim-resource-rbac/rbac-roles.png)

Från den **aktiveringar** menyn, ange startdatum och tid för att aktivera rollen. Du kan också minska aktiveringens varaktighet (hur lång tid som rollen är aktiv) och ange en motivering vid behov. Välj **aktivera**.

Om inte ändrat startdatum och starttid, aktiveras rollen på några sekunder. I den **Mina roller** fönstret Banderollmeddelandet visar att en roll är i kö för aktivering. Välj Uppdatera-knappen för att ta bort det här meddelandet.

![”Mina roller” fönstret med Banderollmeddelandet och ett meddelande om en väntar på godkännande](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Om aktiveringen är schemalagd för ett framtida datum och tid, väntande begäran visas på den **väntande begäranden om** fliken i det vänstra fönstret. Om rollaktivering krävs inte längre, kan du avbryta begäran genom att välja den **Avbryt** knappen.

![Lista över väntande begäranden med ”Avbryt”-knappar](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Tillämpa Just Enough Administration-metoder

Med din resurs rolltilldelningar tillräckligt Administration JEA ()-metodtips är enkelt med PIM för Azure-resurser. Användare och medlemmar i gruppen med tilldelningar i Azure-prenumerationer eller resursgrupper kan aktivera sina befintliga rolltilldelning i projektets omfattning. 

Hitta den underordnade resursen som du behöver hantera från sidan Sök efter.

![Att välja en resurs](media/azure-pim-resource-rbac/azure-resources-02.png)

Välj **Mina roller** i den vänstra rutan och välj rätt roll för att aktivera. Tilldelningstypen är **ärvd** eftersom rollen har tilldelats på prenumerationen, i stället för vid resursgruppen.

![Lista över berättigade rolltilldelningar med Tilldelningstyp markerat](media/azure-pim-resource-rbac/my-roles-02.png)
