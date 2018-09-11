---
title: Hantera åtkomst med hjälp av RBAC och Azure Portal | Microsoft Docs
description: Lär dig mer om att hantera åtkomst för användare, grupper och program med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure Portal. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
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
ms.date: 09/05/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 14ac23e6b69302ac412aac3ecab06345e5d722fd
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296354"
---
# <a name="manage-access-using-rbac-and-the-azure-portal"></a>Hantera åtkomst med hjälp av RBAC och Azure Portal

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är sättet som du hantera åtkomst till resurser i Azure. Den här artikeln lär dig att hantera åtkomst för användare, grupper och program med hjälp av (RBAC) och Azure Portal.

## <a name="list-roles"></a>Visa roller

En rolldefinition är en uppsättning behörigheter som du använder för rolltilldelningar. Azure har över 70 [inbyggda roller](built-in-roles.md). Följ dessa steg om du vill visa roller i portalen.

1. Gå till Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)**.

   ![Rollalternativ](./media/role-assignments-portal/list-subscription-access-control.png)

1. Välj **Roller** att visa en lista med alla inbyggda och anpassade roller.

   ![Rollalternativ](./media/role-assignments-portal/roles-option.png)

   Du kan se hur många användare och grupper som är kopplade till varje roll.

   ![Lista över roller](./media/role-assignments-portal/roles-list.png)

## <a name="list-access"></a>Visar åtkomst

Vid hantering av åtkomst vill du veta vem som har åtkomst, deras behörigheter och på vilken nivå behörigheterna gäller. För att visa åtkomster skapar du en list med rolltilldelningar. Följ stegen nedan för att lista åtkomst för användare och lista åtkomst med olika omfång.

### <a name="list-role-assignments-for-a-user"></a>Visa rolltilldelningar för en användare

1. Välj **Azure Active Directory** i navigeringslistan.

1. Välj **Användare** för att öppna **Alla användare**.

   ![Bladet Alla användare i Azure Active Directory](./media/role-assignments-portal/aad-all-users.png)

1. Välj en enskild användare i listan.

1. I området **Hantera** väljer du **Azure-resurser**.

   ![Resurser för Azure Active Directory-användare](./media/role-assignments-portal/aad-user-azure-resources.png)

   På bladet Azure-resurser kan du se rolltilldelningar för den valda användaren och den valda prenumerationen. Den här listan innehåller endast rolltilldelningar för resurser som du har behörighet att läsa. Till exempel om användaren har också rolltilldelningar som inte kan läsa, visas de rolltilldelningar inte i listan.

1. Om du har flera prenumerationer kan du välja den **prenumeration** listrutan för att se rolltilldelningar i en annan prenumeration.

### <a name="list-role-assignments-for-a-resource-group"></a>Visa rolltilldelningar för en resursgrupp

1. Visa **resursgrupper** i navigationslistan.

1. Välj en resursgrupp och sedan **Åtkomstkontroll (IAM)**.

   Du kan se vem som har åtkomst till den här resursgruppen på åtkomstkontroll (IAM)-bladet, kallas även för identitets- och åtkomsthantering. Observera att vissa roller är begränsade till **den här resursen** medan andra är **(Ärvda)** från ett annat omfång. Åtkomst tilldelas antingen specifikt till resursgruppen eller ärvs från en tilldelning till den överordnade prenumerationen.

   ![Resursgrupper](./media/role-assignments-portal/resource-group-access-control.png)

### <a name="list-role-assignments-for-a-subscription"></a>Lista med rolltilldelningar för en prenumeration

1. Gå till Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)**.

    Du kan se vem som har åtkomst till den här prenumerationen och deras roll på bladet Access control (IAM).

    ![Åtkomstkontroll (IAM)-bladet för en prenumeration](./media/role-assignments-portal/subscription-access-control.png)

    Klassiska prenumerationsadministratörer och medadministratörer betraktas som ägare till prenumerationen i RBAC-modellen.

### <a name="list-role-assignments-for-a-management-group"></a>Lista rolltilldelningar för en hanteringsgrupp

1. I Azure-portalen väljer du **alla tjänster** och sedan **Hanteringsgrupper**.

1. Välj din hanteringsgrupp.

1. Välj **(detaljer)** för din valda hanteringsgrupp.

    ![Hanteringsgrupper](./media/role-assignments-portal/management-groups-list.png)

1. Välj **Åtkomstkontroll (IAM)**.

    Du kan se vem som har åtkomst till den här hanteringsgruppen och deras roll på bladet Access control (IAM).

    ![Åtkomstkontroll (IAM)-bladet för en hanteringsgrupp](./media/role-assignments-portal/management-groups-access-control.png)

## <a name="grant-access"></a>Bevilja åtkomst

I RBAC, om du vill bevilja åtkomst måste tilldela du en roll. Följ dessa steg om du vill bevilja åtkomst med olika omfång.

### <a name="assign-a-role-at-a-resource-group-scope"></a>Tilldela en roll en resursgruppomfånget

1. Visa **resursgrupper** i navigationslistan.

1. Välj en resursgrupp.

1. Välj **Åtkomstkontroll (IAM)** för att visa den aktuella listan med rolltilldelningar i omfånget för en resursgrupp.

   ![Åtkomstkontroll (IAM)-bladet för en resursgrupp](./media/role-assignments-portal/grant-resource-group-access-control.png)

1. Välj **Lägg till** för att öppna fönstret **Lägg till behörigheter**.

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till**.

   ![Fönstret Lägg till behörigheter](./media/role-assignments-portal/add-permissions.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** du tilldela rollen.

   Efter en liten stund tilldelas säkerhetsobjektet till rollen på resursgruppsomfång.

### <a name="assign-a-role-at-a-subscription-scope"></a>Tilldela en roll i omfånget för en prenumeration

1. Gå till Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)** för att visa den aktuella listan med rolltilldelningar i omfånget för en prenumeration.

   ![Åtkomstkontroll (IAM)-bladet för en prenumeration](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Välj **Lägg till** för att öppna fönstret **Lägg till behörigheter**.

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till**.

   ![Fönstret Lägg till behörigheter](./media/role-assignments-portal/add-permissions.png)

1. I listrutan **Roll** väljer du en roll, till exempel **Virtuell datordeltagare**.

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** du tilldela rollen.

   Efter en liten stund tilldelas säkerhetsobjektet till rollen på prenumerationsomfång.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Tilldela en användare som administratör för en prenumeration

Om du vill göra en administratör för en Azure-prenumeration för en användare, tilldela dem den [ägare](built-in-roles.md#owner) rollen prenumerationsområde. Ägarrollen ger användaren fullständig åtkomst till alla resurser i prenumerationen, inklusive rätten att delegera åtkomst till andra. De här stegen är desamma som alla andra rolltilldelning.

1. Gå till Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

1. Välj din prenumeration.

1. Välj **Åtkomstkontroll (IAM)** för att visa den aktuella listan med rolltilldelningar i omfånget för en prenumeration.

   ![Åtkomstkontroll (IAM)-bladet för en prenumeration](./media/role-assignments-portal/grant-subscription-access-control.png)

1. Välj **Lägg till** för att öppna fönstret **Lägg till behörigheter**.

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till**.

   ![Fönstret Lägg till behörigheter](./media/role-assignments-portal/add-permissions.png)

1. I den **rollen** listrutan, väljer den **ägare** roll.

1. I den **Välj** väljer du en användare. Om du inte ser användare i listan kan du skriva i den **Välj** om du vill söka i katalogen för visningsnamn och e-postadresser.

1. Välj **spara** du tilldela rollen.

   Efter en liten stund tilldelas användaren ägarrollen prenumerationsområde.

### <a name="assign-a-role-at-a-management-group-scope"></a>Tilldela en roll i ett omfång för hantering av grupp

1. I Azure-portalen väljer du **alla tjänster** och sedan **hanteringsgrupper**.

1. Välj din hanteringsgrupp.

1. Välj **(detaljer)** för din valda hanteringsgrupp.

    ![Hanteringsgrupper](./media/role-assignments-portal/management-groups-list.png)

1. Välj **Åtkomstkontroll (IAM)** för att visa den aktuella listan med rolltilldelningar i omfånget för en prenumeration.

   ![Åtkomstkontroll (IAM)-bladet för en hanteringsgrupp](./media/role-assignments-portal/grant-management-groups-access-control.png)

1. Välj **Lägg till** för att öppna fönstret **Lägg till behörigheter**.

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till**.

   ![Fönstret Lägg till behörigheter](./media/role-assignments-portal/add-permissions-management-groups.png)

1. I den **rollen** listrutan, väljer du en roll som **Management gruppen deltagare**.

    Information om åtgärderna som stöds på hanteringsgrupper för olika roller finns i [organisera dina resurser med Azure-hanteringsgrupper](../azure-resource-manager/management-groups-overview.md#management-group-access).

1. I listan **Välj** väljer du en användare, grupp eller ett program. Om du inte ser säkerhetsobjekt i listan kan du ange visningsnamn, e-postadresser och objektidentifierare i rutan**Välj** om du vill söka i katalogen.

1. Välj **spara** du tilldela rollen.

   Efter en liten stund tilldelas säkerhetsobjektet rollen på management-Gruppomfång.

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Följ dessa steg om du vill ta bort åtkomst.

### <a name="remove-a-role-assignment"></a>Ta bort en rolltilldelning

1. Öppna den **åtkomstkontroll (IAM)** bladet för hanteringsgruppen, prenumeration, resursgrupp eller resurs som har rolltilldelningen som du vill ta bort.

1. Lägg till en bock intill säkerhetsobjektet med rolltilldelningen som du vil ta bort i listan med rolltilldelningar.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Välj **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment.png)

1. När du ser meddelandet om att ta bort rolltilldelningen väljer du **Ja**.

    Ärvda tilldelningar kan inte tas bort. Om du behöver ta bort en ärvd tilldelning så måste du göra det i samma omfång som där rolltilldelningen skapades. I den **omfång** kolumn, bredvid **(ärvt)** det finns en länk som leder till området där den här rollen har tilldelats. Gå till omfånget som visas där om du vill ta bort rolltilldelningen.

   ![Ta bort rolltilldelningsmeddelande](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart – Bevilja åtkomst för en användare med RBAC och Azure Portal](quickstart-assign-role-user-portal.md)
* [Självstudiekurs – Bevilja åtkomst för en användare med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Inbyggda roller](built-in-roles.md)
* [Organisera dina resurser med Azure-hanteringsgrupper](../azure-resource-manager/management-groups-overview.md)
