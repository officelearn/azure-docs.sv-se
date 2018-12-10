---
title: Självstudie – Bevilja åtkomst för en användare med hjälp av RBAC och Azure-portalen | Microsoft Docs
description: Använd rollbaserad åtkomstkontroll (RBAC) för att ge behörigheter till en användare genom att tilldela en roll i Azure-portalen.
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
ms.date: 11/30/2018
ms.author: rolyon
ms.openlocfilehash: 8caa5c3b33ac1b483429251e0c1256636c4ece1a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634480"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Självstudie: Bevilja åtkomst för en användare med hjälp av RBAC och Azure-portalen

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är sättet som du hantera åtkomst till resurser i Azure. I den här självstudien ger du en användare behörighet att skapa och hantera virtuella datorer i en resursgrupp.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Bevilja åtkomst för en användare i ett resursgruppomfång
> * Tar bort åtkomst

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Visa **resursgrupper** i navigationslistan.

1. Välj **Lägg till** för att öppna bladet **Resursgrupp**.

   ![Lägga till en ny resursgrupp](./media/quickstart-assign-role-user-portal/resource-group.png)

1. För **Resursgruppsnamn** anger du **rbac-resource-group**.

1. Välj en prenumeration och en plats.

1. Välj **Skapa** för att skapa resursgruppen.

1. Välj **Uppdatera** för att uppdatera listan över resursgrupper.

   Den nya resursgruppen visas i listan med resursgrupper.

   ![Lista med resursgrupper](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar.

1. I listan över **Resursgrupper** väljer du den nya resursgruppen **rbac-resource-group**.

1. Välj **Åtkomstkontroll (IAM)**.

1. Välj fliken **Rolltilldelningar** för att visa den aktuella listan med rolltilldelningar.

   ![Bladet Åtkomstkontroll (IAM) för resursgruppen](./media/quickstart-assign-role-user-portal/access-control.png)

1. Välj **Lägg till rolltilldelning** för att öppna fönsterrutan Lägg till rolltilldelning.

   Om du inte har behörighet att tilldela roller är alternativet Lägg till rolltilldelning inaktiverat.

   ![Fönsterrutan Lägg till rolltilldelning](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. I listrutan **Roll** väljer du **Virtuell datordeltagare**.

1. I listan **Välj** väljer du själv eller någon annan användare.

1. Välj **spara** för att skapa rolltilldelningen.

   Efter en liten stund tilldelas användaren rollen Virtuell datordeltagare för resursgruppsomfånget rbac-resource-group.

   ![Tilldelning av rollen Virtuell datordeltagare](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst.

1. I listan över rolltilldelningar lägger du till en bockmarkering intill användaren med rollen Virtuell datordeltagare.

1. Välj **Ta bort**.

   ![Ta bort rolltilldelningsmeddelande](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. När du ser meddelandet om att ta bort rolltilldelningen väljer du **Ja**.

## <a name="clean-up"></a>Rensa

1. Visa **resursgrupper** i navigationslistan.

1. Välj **rbac-resource-group** för att öppna resursgruppen.

1. Välj **Ta bort resursgrupp** för att ta bort resursgruppen.

   ![Ta bort resursgrupp](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. På bladet **Är du säker på att du vill ta bort** skriver du resursgruppsnamnet: **rbac-resource-group**.

1. Välj **Ta bort** för att ta bort resursgruppen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Bevilja åtkomst för en användare med RBAC och PowerShell](tutorial-role-assignments-user-powershell.md)

