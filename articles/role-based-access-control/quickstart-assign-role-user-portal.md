---
title: Självstudie – Bevilja åtkomst för en användare till Azure-resurser med hjälp av RBAC och Azure-portalen | Microsoft Docs
description: Lär dig att bevilja användaråtkomst till Azure-resurser med hjälp av rollbaserad åtkomstkontroll (RBAC) i Azure-portalen.
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
ms.openlocfilehash: 9352130eeeb68a87947450cb5616aa59e8e99ae9
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807379"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Självstudier: Bevilja åtkomst för en användare till Azure-resurser med hjälp av RBAC och Azure-portalen

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. I den här självstudien ger du en användare behörighet att skapa och hantera virtuella datorer i en resursgrupp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Bevilja åtkomst för en användare i ett resursgruppomfång
> * Tar bort åtkomst

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

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

För att skapa åtkomst i RBAC skapar du rolltilldelningar.

1. I listan över **Resursgrupper** klicka du på den nya resursgruppen **rbac-resource-group**.

1. Klicka på **Åtkomstkontroll (IAM)**.

1. Klicka på fliken **Rolltilldelningar** för att visa den aktuella listan med rolltilldelningar.

   ![Bladet Åtkomstkontroll (IAM) för resursgruppen](./media/quickstart-assign-role-user-portal/access-control.png)

1. Klicka på **Lägg till** > **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Menyn Lägg till](./media/role-assignments-portal/add-menu.png)

   ![Fönsterrutan Lägg till rolltilldelning](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du **Virtuell datordeltagare**.

1. I listan **Välj** väljer du själv eller någon annan användare.

1. Klicka på **Spara** för att skapa rolltilldelningen.

   Efter en liten stund tilldelas användaren rollen Virtuell datordeltagare för resursgruppsomfånget rbac-resource-group.

   ![Tilldelning av rollen Virtuell datordeltagare](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst.

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
> [Självstudier: Ge en användare åtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)

