---
title: Lägga till eller ta bort rolltilldelningar med RBAC och Azure-portalen
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänsthuvudnamn eller hanterade identiteter med hjälp av Azure Role-based Access Control (RBAC) och Azure-portalen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246362"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure-portalen

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]I den här artikeln beskrivs hur du tilldelar roller med Azure-portalen.

Om du behöver tilldela administratörsroller i Azure Active Directory läser du [Visa och tilldela administratörsroller i Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort rolltilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Åtkomstkontroll (IAM)

**Åtkomstkontroll (IAM)** är bladet som du använder för att tilldela roller för att bevilja åtkomst till Azure-resurser. Det är också känt som identitets- och åtkomsthantering och visas på flera platser i Azure-portalen. Följande visar ett exempel på bladet Åtkomstkontroll (IAM) för en prenumeration.

![IAM-blad (Access Control Control) för en prenumeration](./media/role-assignments-portal/access-control-subscription.png)

För att vara den mest effektiva med bladet Åtkomstkontroll (IAM) hjälper det om du kan svara på följande tre frågor när du försöker tilldela en roll:

1. **Vem behöver åtkomst?**

    Vem refererar till en användare, grupp, tjänsthuvudnamn eller hanterad identitet. Detta kallas också för *säkerhetsobjekt*.

1. **Vilken roll behöver de?**

    Behörigheter grupperas tillsammans i roller. Du kan välja från en lista med flera [inbyggda roller](built-in-roles.md) eller använda dina egna anpassade roller.

1. **Var behöver de åtkomst?**

    Var refererar till den uppsättning resurser som åtkomsten gäller för. Var kan vara en hanteringsgrupp, prenumeration, resursgrupp eller en enda resurs, till exempel ett lagringskonto. Detta kallas *scope*.

## <a name="add-a-role-assignment"></a>Lägg till en rolltilldelning

I Azure RBAC, för att bevilja åtkomst till en Azure-resurs, lägger du till en rolltilldelning. Följ dessa steg för att tilldela en roll.

1. I Azure-portalen klickar du på **Alla tjänster** och väljer sedan det scope som du vill bevilja åtkomst till. Du kan till exempel välja **Hanteringsgrupper,** **Prenumerationer,** **Resursgrupper**eller en resurs.

1. Klicka på den specifika resursen för det scopet.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Rolltilldelningar** om du vill visa rolltilldelningarna i det här scopet.

    ![Fliken Åtkomstkontroll (IAM) och Rolltilldelningar](./media/role-assignments-portal/role-assignments.png)

1. Klicka på **Lägg till** > **till rolltilldelning**.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. Välj en användare, grupp, tjänsthuvudnamn eller hanterad identitet i listan **Välj.** Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en stund tilldelas säkerhetsobjektet rollen i det valda scopet.

    ![Lägg till rolltilldelning sparad](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare som administratör för en prenumeration

Om du vill göra en användare till administratör för en Azure-prenumeration tilldelar du dem [ägarrollen](built-in-roles.md#owner) i prenumerationsomfånget. Ägarrollen ger användaren fullständig åtkomst till alla resurser i prenumerationen, inklusive behörighet att bevilja åtkomst till andra. De här stegen är desamma som för andra rolltilldelningar.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på prenumerationen du vill ge åtkomst till.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Rolltilldelningar** om du vill visa rolltilldelningarna för den här prenumerationen.

    ![Fliken Åtkomstkontroll (IAM) och Rolltilldelningar](./media/role-assignments-portal/role-assignments.png)

1. Klicka på **Lägg till** > **till rolltilldelning**.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

    Fönstret Lägg till rolltilldelning öppnas.

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du rollen **Ägare**.

1. Välj en användare i listan **Välj**. Om du inte ser användaren i listan kan du ange visningsnamn och e-postadresser i rutan **Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en stund tilldelas rollen Ägare till användaren i prenumerationsomfånget.

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I Azure RBAC tar du bort en rolltilldelning för att ta bort åtkomst från en Azure-resurs. Följ dessa steg för att ta bort en rolltilldelning.

1. Öppna **Åtkomstkontroll (IAM)** i ett scope, till exempel hanteringsgrupp, prenumeration, resursgrupp eller resurs, där du vill ta bort åtkomst.

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

    Om du ser ett meddelande om att ärvda rolltilldelningar inte kan tas bort försöker du ta bort en rolltilldelning i ett underordnat scope. Du bör öppna Access Control (IAM) i det omfång där rollen tilldelades och försök igen. Ett snabbt sätt att öppna Access-kontrollen (IAM) med rätt omfång är att titta på kolumnen **Scope** och klicka på länken **bredvid (Ärvd).**

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

- [Lista rolltilldelningar med Azure RBAC och Azure-portalen](role-assignments-list-portal.md)
- [Självstudiekurs: Bevilja en användare åtkomst till Azure-resurser med HJÄLP AV RBAC och Azure-portalen](quickstart-assign-role-user-portal.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)
- [Ordna dina resurser med Azure-hanteringsgrupper](../governance/management-groups/overview.md)
