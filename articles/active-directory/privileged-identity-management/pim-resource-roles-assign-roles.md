---
title: Privileged Identity Management resurser för Azure - tilldela roller | Microsoft Docs
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
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - resursroller - tilldela

## <a name="assign-roles"></a>Tilldela roller

Om du vill tilldela en användare eller grupp till en roll, Välj rollen (om visar roller) 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

eller klicka på Lägg till från Åtgärdsfältet (på vyn medlemmar).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Om att lägga till en användare eller grupp från fliken medlemmar, måste du: 

1. Välj en roll på menyn Lägg till innan du kan välja en användare eller grupp.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Välj en användare eller grupp från katalogen.

3. Välj lämplig Tilldelningstypen från listrutan. 

    - **Precis i Tidstilldelning:** den ger användare eller grupp medlemmar berättigade men inte beständig åtkomst till rollen under en viss tid eller på obestämd tid (om konfigurerad i rollinställningar). 
    - **Direkttilldelning:** kräver inte medlemmarna användare eller grupp för att aktivera rolltilldelning (kallas även permanent åtkomst). Microsoft rekommenderar att du använder direkttilldelning för kortvarig användning, till exempel på anrop SKIFT eller tid känsliga aktiviteter, där åtkomst inte är nödvändiga när uppgiften har slutförts.

En kryssruta under listrutan för tilldelning av typen kan du ange om tilldelningen vara permanenta (permanent berättigad att aktivera precis i tid tilldelning/permanent active för Direkttilldelning).

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>Kryssrutan kanske unmodifiable om en annan administratör har angett maximalt tilldelning varaktighet för varje Tilldelningstyp av i rollinställningarna för.

 Avmarkera kryssrutan om du vill ange en viss tilldelning varaktighet och ändrar start och avsluta datum och tid.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Hantera rolltilldelningar

Administratörer kan hantera rolltilldelningar genom att välja roller eller medlemmar i det vänstra navigeringsfönstret. Att välja roller kan administratörer att definiera omfattningen av sina administrativa uppgifter till en specifik roll, medan medlemmar visas alla användare och grupper rolltilldelningar för resursen.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Om du har en roll väntande aktivering visas en meddelandebanderoll överst på sidan när du visar medlemskap.


## <a name="modify-existing-assignments"></a>Ändra befintliga tilldelningar

Välj Åtgärdsfältet överst på sidan Ändra inställningar för att ändra befintliga tilldelningar från detaljvy användare/grupp. Ändra tilldelning till bara i tid-tilldelning eller Direkttilldelning.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
