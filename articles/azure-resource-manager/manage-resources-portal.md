---
title: Hantera Azure-resurser med hjälp av Azure portal | Microsoft Docs
description: Använda Azure-portalen och Azure Resource Manager för att hantera dina resurser.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550756"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Hantera Azure-resurser med hjälp av Azure portal

Lär dig hur du använder den [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](resource-group-overview.md) att hantera dina Azure-resurser. För att hantera resursgrupper, se [hantera Azure-resursgrupper med hjälp av Azure portal](./manage-resource-groups-portal.md).

Andra artiklar om hur du hanterar resurser:

- [Hantera Azure-resurser med hjälp av Azure CLI](./manage-resources-cli.md)
- [Hantera Azure-resurser med hjälp av Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resursgrupp

När du har skapat en Resource Manager-mall kan kan du använda Azure-portalen för att distribuera dina Azure-resurser. Skapa en mall finns i [snabbstarten: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](./resource-manager-quickstart-create-templates-use-the-portal.md). Distribuera en mall med hjälp av portalen finns i [distribuera resurser med Resource Manager-mallar och Azure-portalen](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Öppna resurser

Azure-resurser organiseras genom Azure-tjänster och resursgrupper. Följande procedurer visar hur du öppnar ett lagringskonto med namnet **mystorage0207**. Den virtuella datorn finns i en resursgrupp med namnet **mystorage0207rg**.

Öppna en resurs av typen service:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra rutan väljer du Azure-tjänsten. I det här fallet **lagringskonton**.  Om du inte ser tjänsten som anges, väljer **alla tjänster**, och välj sedan tjänsttypen.

    ![Öppna azure-resurs i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Välj den resurs som du vill öppna.

    ![Öppna azure-resurs i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Det ser ut som ett storage-konto:

    ![Öppna azure-resurs i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Öppna en resurs av resursgrupp:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I den vänstra rutan väljer **resursgrupper** att lista resursen i gruppen.
3. Välj den resurs som du vill öppna. 

## <a name="manage-resources"></a>Hantera resurser

När du visar en resurs i portalen kan se du alternativ för hantering av just den resursen.

![Hantera Azure-resurser](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Skärmbilden visar hanteringsalternativen för virtuella Azure-datorer. Du kan utföra åtgärder som till exempel starta, starta om och stoppa en virtuell dator.

## <a name="delete-resources"></a>Ta bort resurser

1. Öppna resursen i portalen. Anvisningar, finns i [öppna resurser](#open-resources).
2. Välj **Ta bort**. I följande skärmbild visas hanteringsalternativ för en virtuell dator.

    ![ta bort azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Skriv namnet på resursen att bekräfta borttagningen och välj sedan **ta bort**.

Mer information om hur Azure Resource Manager beställningar borttagningen av resurser finns i [borttagning av Azure Resource Manager resource](./resource-group-delete.md).

## <a name="move-resources"></a>Flytta resurser

1. Öppna resursen i portalen. Anvisningar, finns i [öppna resurser](#open-resources).
2. Välj **flytta**. I följande skärmbild visas hanteringsalternativ för ett lagringskonto.

    ![Flytta azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Välj **flytta till en annan resursgrupp** eller **Moeve till en annan prenumeration** beroende på dina behov.

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).

## <a name="lock-resources"></a>Lås resurser

Låsning förhindrar andra användare i din organisation av misstag tar bort eller ändrar kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

1. Öppna resursen i portalen. Anvisningar, finns i [öppna resurser](#open-resources).
2. Välj **låser**. I följande skärmbild visas hanteringsalternativ för ett lagringskonto.

    ![LOCK azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Välj **Lägg till**, och sedan ange egenskaperna för lås.

Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggar hjälper dig att organisera din resursgrupp och resurser logiskt. 

1. Öppna resursen i portalen. Anvisningar, finns i [öppna resurser](#open-resources).
2. Välj **Taggar**. I följande skärmbild visas hanteringsalternativ för ett lagringskonto.

    ![taggen azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Ange egenskaperna för taggen och välj sedan **spara**.

Mer information finns i [med taggar för att organisera Azure-resurser](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Övervaka resurser

När du öppnar en resurs, anger portalen standarddiagram och tabeller för att övervaka den resurstypen. Följande skärmbild visar diagram för en virtuell dator:

![övervaka azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Du kan välja fästikonen i det övre högra hörnet av diagrammen för att fästa diagrammet på instrumentpanelen. Läs om hur du arbetar med instrumentpaneler i [skapa och dela instrumentpaneler i Azure-portalen](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- Läs Azure Resource Manager i [översikt över Azure Resource Manager](./resource-group-overview.md).
- Läs Resource Manager-mallens syntax i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](./resource-group-authoring-templates.md).
- Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).
- Mallscheman för Azure Resource Manager-finns [mallreferensen](/azure/templates/).