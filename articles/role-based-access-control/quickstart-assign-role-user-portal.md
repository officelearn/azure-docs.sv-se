---
title: Snabbstart – Bevilja åtkomst för en användare med RBAC och Azure-portalen | Microsoft Docs
description: Använd rollbaserad åtkomstkontroll (RBAC) för att ge behörigheter till en användare genom att tilldela en roll i Azure-portalen.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 74ecca671409b6e163bc0db29d66167d240b645c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092530"
---
# <a name="quickstart-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Snabbstart: Bevilja åtkomst för en användare med RBAC och Azure-portalen

Rollbaserad åtkomstkontroll (RBAC) är sättet som du hanterar åtkomst till resurser i Azure. I den här snabbstarten ger du en användare behörighet att skapa och hantera virtuella datorer i en resursgrupp.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Visa **resursgrupper** i navigationslistan.

1. Välj **Lägg till** för att öppna bladet **Resursgrupp**.

   ![Lägga till en ny resursgrupp](./media/quickstart-assign-role-user-portal/resource-group.png)

1. För **Resursgruppsnamn** anger du **rbac-quickstart-resource-group**.

1. Välj en prenumeration och en plats.

1. Välj **Skapa** för att skapa resursgruppen.

1. Välj **Uppdatera** för att uppdatera listan över resursgrupper.

   Den nya resursgruppen visas i listan med resursgrupper.

   ![Lista med resursgrupper](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar.

1. I listan över **Resursgrupper** väljer du den nya resursgruppen **rbac-quickstart-resource-group**.

1. Välj **Åtkomstkontroll (IAM)** för att visa den aktuella listan med rolltilldelningar.

   ![Bladet Åtkomstkontroll (IAM) för resursgruppen](./media/quickstart-assign-role-user-portal/access-control.png)

1. Välj **Lägg till** för att öppna fönstret **Lägg till behörigheter**.

   Om du inte har behörighet att tilldela roller kan du inte se alternativet **Lägg till**.

   ![Fönstret Lägg till behörigheter](./media/quickstart-assign-role-user-portal/add-permissions.png)

1. I listrutan **Roll** väljer du **Virtuell datordeltagare**.

1. I listan **Välj** väljer du själv eller någon annan användare.

1. Välj **spara** för att skapa rolltilldelningen.

   Efter en liten stund tilldelas användaren rollen Virtuell datordeltagare för resursgruppsomfånget rbac-quickstart-resource-group.

   ![Tilldelning av rollen Virtuell datordeltagare](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst.

1. I listan över rolltilldelningar lägger du till en bockmarkering intill användaren med rollen Virtuell datordeltagare.

1. Välj **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. När du ser meddelandet om att ta bort rolltilldelningen väljer du **Ja**.

## <a name="clean-up"></a>Rensa

1. Visa **resursgrupper** i navigationslistan.

1. Välj **rbac-quickstart-resource-group** för att öppna resursgruppen.

1. Välj **Ta bort resursgrupp** för att ta bort resursgruppen.

   ![Ta bort resursgrupp](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. På bladet **Är du säker på att du vill ta bort** skriver du resursgruppsnamnet: **rbac-quickstart-resource-group**.

1. Välj **Ta bort** för att ta bort resursgruppen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Bevilja åtkomst för en användare med RBAC och PowerShell](tutorial-role-assignments-user-powershell.md)

