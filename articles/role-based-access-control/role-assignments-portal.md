---
title: Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och Azure Portal
description: Lär dig hur du beviljar åtkomst till Azure-resurser för användare, grupper, tjänstens huvud namn eller hanterade identiteter med hjälp av rollbaserad åtkomst kontroll i Azure (RBAC) och Azure Portal.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 17a325e96e9709b60da2f23d1dc68e3300fde80c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707870"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] den här artikeln beskriver hur du tilldelar roller med hjälp av Azure Portal.

Om du behöver tilldela administratörs roller i Azure Active Directory, se [Visa och tilldela administratörs roller i Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Krav

Om du vill lägga till eller ta bort roll tilldelningar måste du ha:

- `Microsoft.Authorization/roleAssignments/write`-och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Översikt över åtkomst kontroll (IAM)

**Åtkomst kontroll (IAM)** är det blad som du använder för att tilldela roller. Det kallas även identitets-och åtkomst hantering och visas på flera platser i Azure Portal. Följande visar ett exempel på bladet åtkomst kontroll (IAM) för en prenumeration.

![Åtkomst kontroll (IAM) bladet för en prenumeration](./media/role-assignments-portal/access-control-subscription.png)

För att bli mest effektiv med åtkomst kontroll bladet (IAM), hjälper det om du kan besvara följande tre frågor när du försöker tilldela en roll:

1. **Vem behöver åtkomst?**

    Som refererar till en användare, grupp, tjänstens huvud namn eller en hanterad identitet. Detta kallas även för ett *säkerhets objekt*.

1. **Vilken roll behöver de?**

    Behörigheter grupperas tillsammans i roller. Du kan välja från en lista över flera [inbyggda roller](built-in-roles.md) eller använda dina egna anpassade roller.

1. **Var behöver de ha åtkomst?**

    Där refererar till den uppsättning resurser som åtkomsten gäller för. Var kan vara en hanterings grupp, en prenumeration, en resurs grupp eller en enskild resurs, till exempel ett lagrings konto. Detta kallas för *omfånget*.

## <a name="add-a-role-assignment"></a>Lägg till en roll tilldelning

Följ dessa steg om du vill tilldela en roll i olika scope.

1. I Azure Portal klickar du på **alla tjänster** och väljer sedan omfånget. Du kan till exempel välja **hanterings grupper**, **prenumerationer**, **resurs grupper**eller en resurs.

1. Klicka på den aktuella resursen.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **roll tilldelningar** för att visa alla roll tilldelningar i det här omfånget.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. Välj en användare, grupp, tjänstens huvud namn eller hanterad identitet i listan **Välj** . Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en liten stund tilldelas säkerhets objekt rollen i det valda omfånget.

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare som administratör för en prenumeration

Om du vill göra en användare till en administratör för en Azure-prenumeration tilldelar du den till [ägar](built-in-roles.md#owner) rollen i prenumerations omfånget. Ägar rollen ger användaren fullständig åtkomst till alla resurser i prenumerationen, inklusive behörighet att bevilja åtkomst till andra. De här stegen är desamma som för andra rolltilldelningar.

1. I Azure-portalen klickar du på **Alla tjänster** och sedan **Prenumerationer**.

1. Klicka på den prenumeration där du vill lägga till en roll tilldelning.

1. Klicka på **Åtkomstkontroll (IAM)** .

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/role-assignments-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du rollen **Ägare**.

1. Välj en användare i listan **Välj**. Om du inte ser användaren i listan kan du ange visningsnamn och e-postadresser i rutan **Välj** om du vill söka i katalogen.

1. Klicka på **Spara** för att tilldela rollen.

   Efter en stund tilldelas rollen Ägare till användaren i prenumerationsomfånget.

## <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Följ dessa steg om du vill ta bort en roll tilldelning.

1. Öppna **åtkomst kontroll (IAM)** i ett omfång, till exempel hanterings grupp, prenumeration, resurs grupp eller resurs, där du vill ta bort åtkomsten.

1. Klicka på fliken **Rolltilldelningar** så att du ser alla rolltilldelningar för prenumerationen.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

    Ärvda tilldelningar kan inte tas bort. Om du behöver ta bort en ärvd tilldelning så måste du göra det i samma omfång som där rolltilldelningen skapades. I kolumnen **omfattning** bredvid **(ärvd)** finns en länk som tar dig till den omfattning där rollen tilldelades. Gå till omfånget som visas där om du vill ta bort rolltilldelningen.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

- [Lista roll tilldelningar med hjälp av Azure RBAC och Azure Portal](role-assignments-list-portal.md)
- [Självstudie: ge en användare åtkomst till Azure-resurser med RBAC och Azure Portal](quickstart-assign-role-user-portal.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)
- [Ordna resurser med hanteringsgrupper i Azure](../governance/management-groups/overview.md)
