---
title: Tilldela roller för Azure-resurser med hjälp av Privileged Identity Management | Microsoft Docs
description: Beskriver hur du tilldelar roller i PIM.
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
ms.openlocfilehash: 501f063992d2f5c7769a5c9059b346aa2b5c2bb4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Tilldela roller för Azure-resurser med hjälp av Privileged Identity Management

## <a name="assign-roles"></a>Tilldela roller

Tilldela en användare eller grupp till en roll när du visar den **roller** rutan, Välj rollen och välj sedan **Lägg till användare**. 

![Fönstret med knappen ”Lägg till användare” i ”roller”](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Du kan också välja **Lägg till användare** från den **medlemmar** fönstret.

![”Medlem” rutan med knappen ”Lägg till användare”](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Om du vill lägga till en användare eller grupp från den **medlemmar** rutan som du behöver: 

1. Välj en roll från den **Välj en roll** fönstret innan du kan välja en användare eller grupp.

   ![”Välj en roll” fönstret](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Välj en användare eller grupp från katalogen.

3. Välj lämplig Tilldelningstypen nedrullningsbara menyn: 

   - **Precis i tid**: ger användaren eller gruppen medlemmar berättigade men inte beständig åtkomst till rollen för en angiven period eller på obestämd tid (om konfigurerad i rollinställningar). 
   - **Direkt**: kräver inte medlemmarna användare eller grupp för att aktivera rolltilldelning (kallas även permanent åtkomst). Vi rekommenderar att du använder direkttilldelning för kortvarig användning, där åtkomst inte är nödvändiga när uppgiften har slutförts. Exempel är SKIFT-samtal och tidskritiska aktiviteter.

4. Om tilldelningen ska vara permanent (permanent tillämplig för en tilldelning av just-in-time eller permanent aktiv för en tilldelning av direct), markera kryssrutan nedan i **Tilldelningstyp** rutan.

   ![”Medlem” inställningsfönstret med rutorna ”Tilldelningstyp” och relaterade](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Kryssrutan kanske unmodifiable om en annan administratör har angett maximalt tilldelning varaktighet för varje Tilldelningstyp av i rollinställningarna för.

   Avmarkera kryssrutan om du vill ange en viss tilldelning varaktighet och ändra start och/eller slutet datum och tid rutorna.

   ![”Medlem” inställningsfönstret med kryssrutorna för startdatum, starttid, slutdatum och sluttid](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Hantera rolltilldelningar

Administratörer kan hantera rolltilldelningar genom att välja antingen **roller** eller **medlemmar** i den vänstra rutan. Att välja **roller** kan administratörer att definiera omfattningen av sina administrativa uppgifter till en specifik roll. Att välja **medlemmar** visar alla användare och grupper rolltilldelningar för resursen.

![Fönstret ”roller”](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![”Medlem” fönstret](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Om du har en roll väntande aktivering visas en meddelandebanderoll överst i fönstret när du visar medlemskap.


## <a name="modify-existing-assignments"></a>Ändra befintliga tilldelningar

Om du vill ändra befintliga tilldelningar från detaljvy användare/grupp, Välj **ändra inställningar** från Åtgärdsfältet. Ändra Tilldelningstypen till **precis i tid** eller **direkt**.

![”Användare” informationsfönstret med knappen ”Inställningar”](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
