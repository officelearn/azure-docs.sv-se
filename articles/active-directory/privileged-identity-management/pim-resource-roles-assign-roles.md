---
title: Tilldela roller för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du tilldelar roller i PIM.
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
ms.openlocfilehash: da5a0e41c476a75f230e2d2645e7f5befd644a93
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441438"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Tilldela roller för Azure-resurser med hjälp av Privileged Identity Management

## <a name="assign-roles"></a>Tilldela roller

Tilldela en användare eller grupp till en roll när du visar den **roller** , markerar du rollen och välj sedan **Lägg till användare**. 

![Fönstret ”roller” med knappen ”Lägg till användare”](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Du kan också välja **Lägg till användare** från den **medlemmar** fönstret.

![”Medlemmar” fönstret med knappen ”Lägg till användare”](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Om du lägger till en användare eller grupp från den **medlemmar** fönstret måste du: 

1. Välj en roll från den **Välj en roll** fönstret innan du kan välja en användare eller grupp.

   ![Fönstret ”Välj en roll”](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Välj en användare eller grupp från katalogen.

3. Välj lämplig Tilldelningstyp från den nedrullningsbara menyn: 

   - **Just-in-time**: ger användaren eller gruppen medlemmarna med berättigade men inte beständig åtkomst till rollen för en angiven tidsperiod eller på obestämd tid (om konfigurerad i rollinställningar). 
   - **Direkt**: kräver inte de användare eller grupp medlemmarna att aktivera rolltilldelningen (kallas beständig åtkomst). Vi rekommenderar att du använder direkttilldelning för kortvarig användning, där åtkomst inte är obligatoriska när aktiviteten har slutförts. Exempel är på anrop SKIFT och tidskänsliga aktiviteter.

4. Om tilldelningen ska vara permanent (permanent berättigad för en just-in-time-tilldelning eller permanent aktiv för en direkttilldelning), markerar du kryssrutan nedan den **Tilldelningstyp** box.

   ![”Medlemskap” inställningsfönstret med rutorna ”Tilldelningstyp” och relaterade](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Kryssrutan kanske unmodifiable om en annan administratör har angett maximalt tilldelningsvaraktighet för varje tilldelning i rollinställningarna för.

   Avmarkera kryssrutan om du vill ange en viss tilldelningsvaraktighet, och ändra start/slut eller datum och tid rutorna.

   ![”Medlemskap” inställningsfönstret med rutorna för startdatum, starttid, slutdatum och sluttid](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Hantera rolltilldelningar

Administratörer kan hantera rolltilldelningar genom att välja antingen **roller** eller **medlemmar** i den vänstra rutan. Att välja **roller** kan administratörer att definiera omfattningen av deras administrativa uppgifter till en viss roll. Att välja **medlemmar** visar alla användare och grupper rolltilldelningar för resursen.

![Fönstret ”roller”](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![Fönstret ”medlemmar”](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Om du har en roll som väntar på aktivering är en meddelandebanderoll visas överst i fönstret när du visar medlemskap.


## <a name="modify-existing-assignments"></a>Ändra befintliga tilldelningar

Om du vill ändra befintliga tilldelningar från detaljvy användare/grupp, Välj **ändra inställningarna för** från Åtgärdsfältet. Ändra Tilldelningstyp till **Just-in-time** eller **direkt**.

![”Användare” informationsfönstret med knappen ”Inställningar”](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
