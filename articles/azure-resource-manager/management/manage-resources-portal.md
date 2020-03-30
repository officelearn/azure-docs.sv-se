---
title: Hantera resurser - Azure-portal
description: Använd Azure-portalen och Azure Resource Manager för att hantera dina resurser. Visar hur du distribuerar och tar bort resurser.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 00d260a74807774d5bf226c3ec00a6b84f93b8d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248351"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Hantera Azure-resurser med hjälp av Azure-portalen

Lär dig hur du använder [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](overview.md) för att hantera dina Azure-resurser. Information om hur du hanterar resursgrupper finns i [Hantera Azure-resursgrupper med hjälp av Azure-portalen](manage-resource-groups-portal.md).

Andra artiklar om hantering av resurser:

- [Hantera Azure-resurser med hjälp av Azure CLI](manage-resources-cli.md)
- [Hantera Azure-resurser med hjälp av Azure PowerShell](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resursgrupp

När du har skapat en Resource Manager-mall kan du använda Azure-portalen för att distribuera dina Azure-resurser. Information om hur du skapar en mall finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../templates/quickstart-create-templates-use-the-portal.md). Information om hur du distribuerar en mall med hjälp av portalen finns i [Distribuera resurser med Resource Manager-mallar och Azure-portal](../templates/deploy-portal.md).

## <a name="open-resources"></a>Öppna resurser

Azure-resurser ordnas efter Azure-tjänster och av resursgrupper. Följande procedurer visar hur du öppnar ett lagringskonto som heter **mystorage0207**. Den virtuella datorn finns i en resursgrupp som kallas **mystorage0207rg**.

Så här öppnar du en resurs efter tjänsttypen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj Azure-tjänsten i den vänstra rutan. I det här fallet **lagringskonton**.  Om du inte ser tjänsten i listan väljer du **Alla tjänster**och väljer sedan tjänsttypen.

    ![öppna azure-resurs i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Markera den resurs som du vill öppna.

    ![öppna azure-resurs i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Ett lagringskonto ser ut som:

    ![öppna azure-resurs i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Så här öppnar du en resurs efter resursgrupp:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I den vänstra rutan väljer du **Resursgrupper** som ska lista resursen i gruppen.
3. Markera den resurs som du vill öppna. 

## <a name="manage-resources"></a>Hantera resurser

När du visar en resurs i portalen visas alternativen för att hantera just den resursen.

![hantera Azure-resurser](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Skärmbilden visar hanteringsalternativen för en virtuell Azure-dator. Du kan utföra åtgärder som att starta, starta om och stoppa en virtuell dator.

## <a name="delete-resources"></a>Ta bort resurser

1. Öppna resursen i portalen. Steg för stegen finns i [Öppna resurser](#open-resources).
2. Välj **Ta bort**. Följande skärmbild visar hanteringsalternativen för en virtuell dator.

    ![ta bort azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Skriv namnet på resursen för att bekräfta borttagningen och välj sedan **Ta bort**.

Mer information om hur Azure Resource Manager beställer borttagning av resurser finns i [Azure Resource Manager-resursgruppborttagning](delete-resource-group.md).

## <a name="move-resources"></a>Flytta resurser

1. Öppna resursen i portalen. Steg för stegen finns i [Öppna resurser](#open-resources).
2. Välj **Flytta**. Följande skärmbild visar hanteringsalternativen för ett lagringskonto.

    ![flytta azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Välj **Flytta till en annan resursgrupp** eller Flytta till en annan **prenumeration** beroende på dina behov.

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Lås resurser

Låsning hindrar andra användare i organisationen från att oavsiktligt ta bort eller ändra kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

1. Öppna resursen i portalen. Steg för stegen finns i [Öppna resurser](#open-resources).
2. Välj **Lås**. Följande skärmbild visar hanteringsalternativen för ett lagringskonto.

    ![låsa azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Välj **Lägg till**och ange sedan låsegenskaperna.

Mer information finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggning hjälper till att organisera resursgruppen och resurserna logiskt. 

1. Öppna resursen i portalen. Steg för stegen finns i [Öppna resurser](#open-resources).
2. Välj **Taggar**. Följande skärmbild visar hanteringsalternativen för ett lagringskonto.

    ![tagga azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Ange taggegenskaperna och välj sedan **Spara**.

Information finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md#portal).

## <a name="monitor-resources"></a>Övervaka resurser

När du öppnar en resurs visar portalen standarddiagram och tabeller för övervakning av den resurstypen. Följande skärmbild visar diagrammen för en virtuell dator:

![övervaka azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Du kan välja pinikonen längst upp till höger i diagrammen för att fästa diagrammet på instrumentpanelen. Mer information om hur du arbetar med instrumentpaneler finns [i Skapa och dela instrumentpaneler i Azure-portalen](../../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) är det sätt som du hanterar åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- Information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](overview.md).
- Information om hur du lär dig mallsyntaxen för Resurshanteraren finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../templates/template-syntax.md).
- Mer information om hur du utvecklar mallar finns i [steg-för-steg-självstudier](/azure/azure-resource-manager/).
- Information om hur du visar mallscheman för Azure Resource Manager finns i [mallreferens](/azure/templates/).
