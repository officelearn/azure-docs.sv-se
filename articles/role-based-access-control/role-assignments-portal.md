---
title: Hantera åtkomst med hjälp av RBAC och Azure Portal | Microsoft Docs
description: Lär dig mer om att hantera åtkomst för användare, grupper och program med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure Portal. Detta inkluderar att visa åtkomst, bevilja åtkomst och ta bort åtkomst.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8f2c77a366c96455016894c042868d080551bc6a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295872"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Hantera åtkomst med hjälp av RBAC och Azure Portal

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är sättet som du hantera åtkomst till resurser i Azure. Den här artikeln lär dig att hantera åtkomst för användare, grupper och program med hjälp av (RBAC) och Azure Portal.

## <a name="list-roles"></a>Visa roller

En rolldefinition är en uppsättning behörigheter som du använder för rolltilldelningar. Azure har över 60 [inbyggda roller](built-in-roles.md).

1. Gå till Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)**.

   ![Rollalternativ](./media/role-assignments-portal/list-subscription-access-control.png)

1. Välj **Roller** att visa en lista med alla inbyggda och anpassade roller.

   ![Rollalternativ](./media/role-assignments-portal/roles-option.png)

   Du kan se hur många användare och grupper som är kopplade till varje roll.

   ![Lista över roller](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Visar åtkomst

Vid hantering av åtkomst vill du veta vem som har åtkomst, deras behörigheter och på vilken nivå behörigheterna gäller. För att visa åtkomster skapar du en list med rolltilldelningar.

### <a name="list-role-assignments-for-a-subscription"></a>Lista med rolltilldelningar för en prenumeration

1. Gå till Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)**.

    Du kan se vem som har åtkomst till den här prenumerationen och deras roll i bladet åtkomstkontroll (IAM), vilket även kallas identitets- och åtkomsthantering.

    ![Bladet åtkomstkontroll (IAM)](./media/role-assignments-portal/subscription-access-control.png)

    Klassiska prenumerationsadministratörer och medadministratörer betraktas som ägare till prenumerationen i RBAC-modellen.


### <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

1. Visa **resursgrupper** i navigationslistan.

1. Välj en resursgrupp och sedan **Åtkomstkontroll (IAM)**.

   På bladet Åtkomstkontroll (IAM) kan se du vem som har åtkomst till den här resursgruppen. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas antingen specifikt till resursgruppen eller ärvs från en tilldelning till den överordnade prenumerationen.

   ![Resursgrupper](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

1. Välj **Azure Active Directory** i navigeringslistan.

1. Välj **Användare** för att öppna **Alla användare**.

   ![Bladet Alla användare i Azure Active Directory](./media/role-assignments-portal/aad-all-users.png)

1. Välj en enskild användare i listan.

1. I området **Hantera** väljer du **Azure-resurser**.

   ![Resurser för Azure Active Directory-användare](./media/role-assignments-portal/aad-user-azure-resources.png)

   På bladet Azure-resurser kan se du rolltilldelningar för den valda användaren. Listan innehåller endast rolltilldelningar för resurser som du har behörighet att läsa. Till exempel om användaren har också rolltilldelningar i en annan prenumeration som du inte kan läsa, visas dessa rolltilldelningar inte i listan.

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar.

### <a name="create-a-role-assignment-at-a-subscription-scope"></a>Skapa en rolltilldelning i omfånget för en prenumeration

1. Gå till Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)** för att visa den aktuella listan med rolltilldelningar i omfånget för en prenumeration.

   ![Bladet Åtkomstkontroll (IAM) för resursgruppen](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Välj **Lägg till** för att öppna fönstret **Lägg till behörigheter**.

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till**.

   ![Fönstret Lägg till behörigheter](./media/role-assignments-portal/add-permissions.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** för att skapa rolltilldelningen.

   Efter en liten stund tilldelas säkerhetsobjektet till rollen på prenumerationsomfång.

### <a name="create-a-role-assignment-at-a-resource-group-scope"></a>Skapa en rolltilldelning på resursgruppomfång

1. Visa **resursgrupper** i navigationslistan.

1. Välj en resursgrupp.

1. Välj **Åtkomstkontroll (IAM)** för att visa den aktuella listan med rolltilldelningar i omfånget för en resursgrupp.

   ![Bladet Åtkomstkontroll (IAM) för resursgruppen](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Välj **Lägg till** för att öppna fönstret **Lägg till behörigheter**.

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till**.

   ![Fönstret Lägg till behörigheter](./media/role-assignments-portal/add-permissions.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** för att skapa rolltilldelningen.

   Efter en liten stund tilldelas säkerhetsobjektet till rollen på resursgruppsomfång.

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst.

### <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

1. Öppna bladet **Åtkomstkontroll (IAM)** för prenumerationen, resursgruppen eller resursen som innehåller rolltilldelningen som du vill ta bort.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Välj **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. När du ser meddelandet om att ta bort rolltilldelningen väljer du **Ja**.

Ärvda tilldelningar kan inte tas bort. Om du behöver ta bort en ärvd tilldelning så måste du göra det i samma omfång som där rolltilldelningen skapades. I kolumnen **Omfång** bredvid **Ärvd** finns en länk som leder till resurserna där den här rollen har tilldelats. Gå till omfånget som visas där om du vill ta bort rolltilldelningen.

## <a name="other-tools-to-manage-access"></a>Andra verktyg för att hantera åtkomst

Du kan tilldela roller och hantera åtkomst med Azure RBAC-kommandon i andra verktyg än Azure-portalen. Mer information finns här på följande länkar:

* [Azure PowerShell](role-assignments-powershell.md)
* [Azure CLI](role-assignments-cli.md)
* [REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart – Bevilja åtkomst för en användare med RBAC och Azure Portal](quickstart-assign-role-user-portal.md)
* [Självstudiekurs – Bevilja åtkomst för en användare med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Inbyggda roller](built-in-roles.md)
