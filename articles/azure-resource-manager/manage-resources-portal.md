---
title: Hantera Azure-resurser med hjälp av Azure Portal | Microsoft Docs
description: Använd Azure Portal och Azure Resource Manager för att hantera dina resurser. Visar hur du distribuerar och tar bort resurser.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c52d54ca02436f7224346a65048244a5ec8d104c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390400"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Hantera Azure-resurser med hjälp av Azure Portal

Lär dig hur du använder [Azure Portal](https://portal.azure.com) med [Azure Resource Manager](resource-group-overview.md) för att hantera dina Azure-resurser. Information om hur du hanterar resurs grupper finns i [Hantera Azure-resurs grupper med hjälp av Azure Portal](./manage-resource-groups-portal.md).

Andra artiklar om att hantera resurser:

- [Hantera Azure-resurser med hjälp av Azure CLI](./manage-resources-cli.md)
- [Hantera Azure-resurser med hjälp av Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resurs grupp

När du har skapat en Resource Manager-mall kan du använda Azure Portal för att distribuera dina Azure-resurser. Information om hur du skapar en mall finns i [snabb start: skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](./resource-manager-quickstart-create-templates-use-the-portal.md). Information om hur du distribuerar en mall med hjälp av portalen finns i [distribuera resurser med Resource Manager-mallar och Azure Portal](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Öppna resurser

Azure-resurser organiseras av Azure-tjänster och resurs grupper. Följande procedurer visar hur du öppnar ett lagrings konto med namnet **mystorage0207**. Den virtuella datorn finns i en resurs grupp med namnet **mystorage0207rg**.

Så här öppnar du en resurs med tjänst typen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I det vänstra fönstret väljer du Azure-tjänsten. I det här fallet **lagrings konton**.  Om tjänsten inte visas i listan väljer du **alla tjänster**och väljer sedan tjänst typen.

    ![öppna Azure-resursen i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Välj den resurs som du vill öppna.

    ![öppna Azure-resursen i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Ett lagrings konto ser ut så här:

    ![öppna Azure-resursen i portalen](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Så här öppnar du en resurs per resurs grupp:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I det vänstra fönstret väljer du **resurs grupper** för att Visa resursen i gruppen.
3. Välj den resurs som du vill öppna. 

## <a name="manage-resources"></a>Hantera resurser

När du visar en resurs i portalen ser du alternativen för att hantera den specifika resursen.

![Hantera Azure-resurser](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

Skärm bilden visar hanterings alternativen för en virtuell Azure-dator. Du kan utföra åtgärder som att starta, starta om och stoppa en virtuell dator.

## <a name="delete-resources"></a>Ta bort resurser

1. Öppna resursen i portalen. Anvisningar finns i [Öppna resurser](#open-resources).
2. Välj **Ta bort**. Följande skärm bild visar hanterings alternativen för en virtuell dator.

    ![ta bort Azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Skriv namnet på resursen för att bekräfta borttagningen och välj sedan **ta bort**.

Mer information om hur Azure Resource Manager beställer borttagningen av resurser finns i [Azure Resource Manager ta bort resurs grupp](./resource-group-delete.md).

## <a name="move-resources"></a>Flytta resurser

1. Öppna resursen i portalen. Anvisningar finns i [Öppna resurser](#open-resources).
2. Välj **Flytta**. Följande skärm bild visar hanterings alternativen för ett lagrings konto.

    ![flytta Azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Välj **Flytta till en annan resurs grupp** eller **Flytta till en annan prenumeration** beroende på dina behov.

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).

## <a name="lock-resources"></a>Lås resurser

Låsning förhindrar att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser, t. ex. Azure-prenumeration, resurs grupp eller resurs. 

1. Öppna resursen i portalen. Anvisningar finns i [Öppna resurser](#open-resources).
2. Välj **Lås**. Följande skärm bild visar hanterings alternativen för ett lagrings konto.

    ![Lås Azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Välj **Lägg till**och ange sedan Lås egenskaperna.

Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggning hjälper till att organisera resurs gruppen och resurserna logiskt. 

1. Öppna resursen i portalen. Anvisningar finns i [Öppna resurser](#open-resources).
2. Välj **Taggar**. Följande skärm bild visar hanterings alternativen för ett lagrings konto.

    ![Tagga Azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Ange taggens egenskaper och välj sedan **Spara**.

Mer information finns i [använda taggar för att ordna dina Azure-resurser](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Övervaka resurser

När du öppnar en resurs, visar portalen standard diagram och tabeller för övervakning av resurs typen. Följande skärm bild visar graferna för en virtuell dator:

![övervaka Azure-resurs](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Du kan välja ikonen fäst i det övre högra hörnet i graferna för att fästa grafen på instrument panelen. Information om hur du arbetar med instrument paneler finns [i skapa och dela instrument paneler i Azure Portal](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- Mer information Azure Resource Manager finns [Azure Resource Manager översikt](./resource-group-overview.md).
- Information om syntaxen för Resource Manager-mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](./resource-group-authoring-templates.md).
- Information om hur du utvecklar mallar finns i de [stegvisa självstudierna](/azure/azure-resource-manager/).
- Om du vill visa scheman för Azure Resource Manager mallar, se [referens för mallar](/azure/templates/).
