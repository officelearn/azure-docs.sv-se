---
title: Privileged Identity Management resurser för Azure - Aktivera roller | Microsoft Docs
description: Beskriver hur du aktiverar roller i PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Aktivera Privileged Identity Management - resursroller-
Aktivera roller för Azure-resurser introducerar en ny upplevelse som berättigade rollmedlemmar kan schemalägga aktivering för framtida datum/tid och välj en specifik aktivering varaktighet inom den Maxtid (konfigurerad av administratörer). Lär dig mer om [aktivera Azure AD-roller här](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Aktivera roller
Navigera till den min roller avsnittet i det vänstra navigeringsfältet. Klicka på ”Aktivera” för den roll som du vill aktivera i.
![](media/azure-pim-resource-rbac/rbac-roles.png)

Ange önskade startdatum och tidpunkt för att aktivera rollen från menyn aktiveringar. Du kan också minska varaktigheten aktivering (hur lång tid rollen är aktiv) och ange en motivering om det behövs; Klicka på Aktivera.

Om startdatum och starttid inte har ändrat, aktiveras rollen inom några sekunder. En roll i kö för aktivering Banderollmeddelandet på Mina rollsidan visas. Klicka på Uppdatera om du vill avmarkera det här meddelandet.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Om aktiveringen är schemalagd vid en senare tid, visas en väntande begäran på fliken väntande begäranden i den vänstra navigeringsmenyn. Om rollaktivering krävs inte längre, kan användaren avbryta begäran genom att klicka på knappen Avbryt till höger på sidan.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Bara tillräckligt med administration

Med din resurs rolltilldelningar bara tillräckligt med bästa praxis för administration (JEA) är enkelt med PIM för Azure-resurser. Användare och medlemmar i gruppen med tilldelningar i Azure-prenumerationer eller resursgrupper kan aktivera sina befintliga rolltilldelning i projektets omfattning. 

Hitta den underordnade resursen som du behöver hantera från sidan Sök efter.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Välj min roller från den vänstra navigeringsmenyn och väljer sedan rätt roll för att aktivera. Meddelande Tilldelningstypen ärvs eftersom tilldelades rollen på prenumerationen i stället för resursgrupp, enligt nedan.

![](media/azure-pim-resource-rbac/my-roles-02.png)
