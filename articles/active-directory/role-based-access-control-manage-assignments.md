---
title: Visa Azure-resurs åtkomst tilldelningar | Microsoft Docs
description: Visa och hantera alla rollbaserad åtkomstkontroll tilldelningar för alla användare eller grupper i Azure-portalen
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: rolyon
ms.openlocfilehash: b53c758eb9cfd68e66a6192f4461de3d60f8c651
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2018
---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>Visa åtkomst-tilldelningar för användare och grupper i Azure-portalen
> [!div class="op_single_selector"]
> * [Hantera åtkomst enligt användare eller grupp](role-based-access-control-manage-assignments.md)
> * [Hantera åtkomst enligt resurs](role-based-access-control-configure.md)

Med rollbaserad åtkomstkontroll (RBAC) i Azure Active Directory (Azure AD), kan du hantera åtkomst till resurserna i Azure. 

Åtkomst som tilldelats med RBAC är detaljerad eftersom du kan begränsa behörigheterna på två sätt:

* **Omfattning:** RBAC rolltilldelningar är begränsade till en viss prenumeration, resursgrupp eller resurs. En användare får åtkomst till en enskild resurs kan inte komma åt andra resurser i samma prenumeration.
* **Roll:** inom omfånget för tilldelningen åtkomst minskar även ytterligare genom att tilldela en roll. Roller kan vara hög nivå som ägare eller specifika som virtuella läsare.

Roller kan bara tilldelas från inom prenumerationen, resursgruppen eller resursen som omfång för tilldelningen. Men du kan visa alla access-tilldelningar för en viss användare eller grupp i en enda plats. Du kan ha upp till 2000 rolltilldelningar i varje prenumeration. 

Mer information om hur du [använda rolltilldelningar för att hantera åtkomst till resurserna i Azure-prenumeration](role-based-access-control-configure.md).

## <a name="view-access-assignments"></a>Visa åtkomst tilldelningar
Om du vill leta upp access-tilldelningar för en enskild användare eller grupp som startar i Azure Active Directory i den [Azure-portalen](http://portal.azure.com).

1. Välj **Azure Active Directory**. Om det här alternativet inte visas i listan navigering väljer **alla tjänster** och rulla sedan hitta **Azure Active Directory**.
2. Välj **användare och grupper**, och sedan antingen **alla användare** eller **alla grupper**. I det här exemplet fokuserar vi på enskilda användare.
    ![Hantera användare och grupper i Azure Active Directory – skärmbild](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Sök efter användare efter namn eller användarnamn.
4. Välj **Azure-resurser** på bladet användare. Alla accesstilldelningar för användaren visas.

### <a name="read-permissions-to-view-assignments"></a>Läsbehörighet visa tilldelningar
Den här sidan visar endast de access tilldelningar som du har behörighet att läsa. Till exempel ha läsbehörighet till prenumerationen A och gå till bladet Azure-resurser för att kontrollera en användares tilldelningar. Du kan se sin åtkomst tilldelningar för prenumerationen A, men inte kan se att hon också har åtkomst tilldelningar för prenumerationen B.

## <a name="delete-access-assignments"></a>Ta bort åtkomst tilldelningar
Från det här bladet kan du ta bort åtkomst tilldelningar som har tilldelats direkt till en användare eller grupp. Om åtkomst tilldelningen ärvdes från en överordnad grupp, måste du gå till en resurs eller en prenumeration och hantera det tilldelningen.

1. Välj det du vill ta bort från listan över alla access-tilldelningar för en användare eller grupp.
2. Välj **ta bort** och sedan **Ja** att bekräfta.
    ![Ta bort åtkomst tilldelning – skärmbild](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="next-steps"></a>Nästa steg

* Kom igång med rollbaserad åtkomstkontroll till [använda rolltilldelningar för att hantera åtkomst till resurserna i Azure-prenumeration](role-based-access-control-configure.md)
* Gå till [Inbyggda RBAC-roller](role-based-access-built-in-roles.md)

