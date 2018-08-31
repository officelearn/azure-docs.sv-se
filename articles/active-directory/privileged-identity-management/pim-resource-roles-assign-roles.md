---
title: Tilldela Azure-resursroller i PIM | Microsoft Docs
description: Lär dig hur du tilldelar Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7019a6f97a9590d3b652584015f3077f4ed075af
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188928"
---
# <a name="assign-azure-resource-roles-in-pim"></a>Tilldela Azure-resursroller i PIM

Azure AD PIM kan hantera inbyggda Azure-resursroller, samt anpassade roller, inklusive (men inte begränsat till):

- Ägare
- Administratör för användaråtkomst
- Deltagare
- Säkerhetsadministratör
- Säkerhetshanteraren och mycket mer

>[!NOTE]
Användare eller medlemmar i en grupp som tilldelats ägare eller administratör för användaråtkomst roller och globala administratörer som aktiverar prenumerationshantering i Azure AD är resursen administratörer. Dessa administratörer kan tilldela roller, konfigurera inställningar för serverrollen och granska åtkomst med hjälp av PIM för Azure-resurser. Visa en lista över [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

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

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
- [Tilldela Azure AD-katalogroller i PIM](pim-how-to-add-role-to-user.md)
