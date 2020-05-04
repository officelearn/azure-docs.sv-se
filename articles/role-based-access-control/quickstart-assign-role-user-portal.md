---
title: 'Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal – Azure RBAC'
description: I den här självstudien får du lära dig att ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: e70a3a1eedbc56754ad46fd99dddc0c7a3a6cf2f
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735852"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure Portal

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](overview.md) är hur du hanterar åtkomst till Azure-resurser. I den här självstudien ger du en användare behörighet att skapa och hantera virtuella datorer i en resursgrupp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Bevilja åtkomst för en användare i ett resursgruppomfång
> * Tar bort åtkomst

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. I navigeringslistan klickar du på **Resursgrupper**.

1. Klicka på **Lägg till** för att öppna bladet **Resursgrupp**.

   ![Lägga till en ny resursgrupp](./media/quickstart-assign-role-user-portal/resource-group.png)

1. För **Resursgruppsnamn** anger du **rbac-resource-group**.

1. Välj en prenumeration och en plats.

1. Klicka på **Skapa** för att skapa resursgruppen.

1. Klicka på **Uppdatera** för att uppdatera listan över resursgrupper.

   Den nya resursgruppen visas i listan med resursgrupper.

   ![Lista med resursgrupper](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Bevilja åtkomst

I Azure RBAC för att bevilja åtkomst skapar du en roll tilldelning.

1. I listan över **Resursgrupper** klicka du på den nya resursgruppen **rbac-resource-group**.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Rolltilldelningar** för att visa den aktuella listan med rolltilldelningar.

   ![Bladet Åtkomstkontroll (IAM) för resursgruppen](./media/quickstart-assign-role-user-portal/access-control.png)

1. Klicka på **Lägg** > till**Lägg till roll tilldelning** för att öppna fönstret Lägg till roll tilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du **Virtuell datordeltagare**.

1. I listan **Välj** väljer du själv eller någon annan användare.

1. Klicka på **Spara** för att skapa rolltilldelningen.

   Efter en liten stund tilldelas användaren rollen Virtuell datordeltagare för resursgruppsomfånget rbac-resource-group.

   ![Tilldelning av rollen Virtuell datordeltagare](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Tar bort åtkomst

För att ta bort åtkomst i Azure RBAC tar du bort en roll tilldelning.

1. I listan över rolltilldelningar lägger du till en bockmarkering intill användaren med rollen Virtuell datordeltagare.

1. Klicka på **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. I meddelandet om att ta bort rolltilldelningen klickar du på **Ja**.

## <a name="clean-up"></a>Rensa

1. I navigeringslistan klickar du på **Resursgrupper**.

1. Klicka på **rbac-resource-group** för att öppna resursgruppen.

1. Klicka på **Ta bort resursgrupp** för att ta bort resursgruppen.

   ![Ta bort resursgrupp](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. På bladet **Är du säker på att du vill ta bort** skriver du resursgruppsnamnet: **rbac-resource-group**.

1. Klicka på **Ta bort** för att ta bort resursgruppen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure PowerShell](tutorial-role-assignments-user-powershell.md)
