---
title: Aktivera roller för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du aktiverar roller i PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: d41e7e808177ffc3cb9ffd37c5aaba6d401f6bd8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699673"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Aktivera roller för Azure-resurser med hjälp av Privileged Identity Management
Privileged Identity Management (PIM) introducerar en ny upplevelse i Aktivera roller för Azure-resurser. Berättigad rollmedlemmar kan schemalägga aktivering för framtida datum och tid. De kan också välja en specifik aktivering varaktighet inom den Maxtid (konfigurerad av administratörer). Mer information finns i [så här aktiverar eller inaktiverar roller i Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Aktivera roller
Bläddra till den **min roller** avsnitt i den vänstra rutan. Välj **aktivera** för den roll som du vill aktivera.

![Fliken ”berättigade roller” i den ”min roller”-rutan.](media/azure-pim-resource-rbac/rbac-roles.png)

Från den **aktiveringar** -menyn, ange datumet och tid för att aktivera rollen. Du kan också minska varaktigheten aktivering (tidslängd som rollen är aktiv) och ange en motivering om det behövs. Markera **aktivera**.

Om inte ändrat startdatum och starttid, aktiveras rollen i sekunder. I den **min roller** fönstret Banderollmeddelandet visar att en roll i kö för aktivering. Klicka på Uppdatera om du vill ta bort det här meddelandet.

![”Min roller” fönstret med Banderollmeddelandet och ett meddelande om en väntar på godkännande](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Om aktiveringen är schemalagd för framtida datum och tid, väntande begäran visas på den **väntande begäranden** fliken i det vänstra fönstret. Om det krävs inte längre rollaktivering, kan du avbryta begäran genom att välja den **Avbryt** knappen.

![Lista över väntande begäranden med ”Avbryt” knappar](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Använd bara tillräckligt Administration praxis

Det är enkelt med PIM för Azure-resurser med din resurs rolltilldelningar bara tillräckligt Administration JEA () bästa praxis. Användare och medlemmar i gruppen med tilldelningar i Azure-prenumerationer eller resursgrupper kan aktivera sina befintliga rolltilldelning i projektets omfattning. 

Hitta den underordnade resursen som du behöver hantera från sidan Sök efter.

![Att välja en resurs](media/azure-pim-resource-rbac/azure-resources-02.png)

Välj **min roller** i den vänstra rutan och väljer sedan rätt roll för att aktivera. Tilldelningstypen är **ärvda** eftersom rollen tilldelades på prenumerationen i stället för vid resursgruppen.

![Lista över tillgängliga rolltilldelningar med Tilldelningstypen markerat](media/azure-pim-resource-rbac/my-roles-02.png)
