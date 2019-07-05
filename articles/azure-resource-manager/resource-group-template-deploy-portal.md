---
title: Distribuera Azure-resurser med hjälp av Azure portal | Microsoft Docs
description: Använd Azure-portalen och Azure Resource Manager för att distribuera dina resurser.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460334"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Distribuera resurser med Resource Manager-mallar och Azure Portal

Lär dig hur du använder den [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](resource-group-overview.md) att distribuera dina Azure-resurser. Läs om hur du hanterar dina resurser i [hantera Azure-resurser med hjälp av Azure portal](manage-resources-portal.md).

Distribuera Azure-resurser med hjälp av Azure-portalen vanligtvis omfattar två steg:

- Skapa en resursgrupp.
- Distribuera resurser till resursgruppen.

Du kan också distribuera en Azure Resource Manager-mall för att skapa Azure-resurser.

Den här artikeln visar båda metoderna.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Om du vill skapa en ny resursgrupp, Välj **resursgrupper** från den [Azure-portalen](https://portal.azure.com).

   ![Välj resursgrupper](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Välj under resursgrupper, **Lägg till**.

   ![Lägg till resursgrupp](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Välj eller ange följande värden:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resursgrupp**: Ge resursgruppen ett namn.
    - **Region**: Ange en Azure-plats. Det här är där resursgruppen lagrar metadata om resurserna. Av kompatibilitetsskäl, kanske du vill ange var metadatan ska lagras. I allmänhet rekommenderar vi att du anger en plats där de flesta av dina resurser ska placeras. Med hjälp av samma plats kan förenkla din mall.

   ![Ange gruppvärden](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Välj **Granska + skapa**.
1. Granska värdena och välj sedan **skapa**.
1. Välj **uppdatera** innan du kan se den nya resursgruppen i listan.

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resursgrupp

När du har skapat en resursgrupp kan du distribuera resurser till gruppen från Marketplace. Marketplace innehåller fördefinierade lösningar för vanliga scenarier.

1. Om du vill starta en distribution väljer **skapa en resurs** från den [Azure-portalen](https://portal.azure.com).

   ![Ny resurs](./media/resource-group-template-deploy-portal/new-resources.png)

1. Hitta typ av resurs som du vill distribuera. Resurserna är uppdelade i kategorier. Om du inte ser viss lösning som du vill distribuera, kan du söka på Marketplace efter den. Följande skärmbild visar att Ubuntu Server har valts.

   ![Välj resurstyp](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Beroende på vilken typ av markerade resursen har du en samling relevanta egenskaper att ställa in före distributionen. Du måste välja en mål-resursgrupp för alla typer. Följande bild visar hur du skapar en Linux-dator och distribuera den till den resursgrupp du skapade.

   ![Skapa resursgrupp](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Du kan också välja att skapa en resursgrupp när du distribuerar dina resurser. Välj **Skapa nytt** och ge resursgruppen ett namn.

1. Distributionen börjar. Distributionen kan ta flera minuter. Vissa resurser ta längre tid än andra resurser. När distributionen är klar visas ett meddelande. Välj **gå till resurs** att öppna

   ![Visa meddelande](./media/resource-group-template-deploy-portal/view-notification.png)

1. När du har distribuerat dina resurser, du kan lägga till fler resurser till resursgruppen genom att välja **Lägg till**.

   ![Lägg till resurs](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuera resurser från anpassad mall

Om du vill köra en distribution, men inte använda någon av mallarna i Marketplace, kan du skapa en anpassad mall som definierar infrastrukturen för lösningen. Läs om hur du skapar mallar i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).

> [!NOTE]
> Portalgränssnittet stöder inte refererar till en [från ett Key Vault](resource-manager-keyvault-parameter.md). Använd i stället [PowerShell](resource-group-template-deploy.md) eller [Azure CLI](resource-group-template-deploy-cli.md) att distribuera mallen lokalt eller från en extern URI.

1. Om du vill distribuera en anpassad mall via portalen, Välj **skapa en resurs**, Sök efter **mallen**. Välj sedan **malldistributionen**.

   ![Sök malldistribution](./media/resource-group-template-deploy-portal/search-template.png)

1. Välj **Skapa**.
1. Du ser flera alternativ för att skapa en mall:

    - **Skapa din egen mall i redigeraren**: skapa en mall med hjälp av portalen mallen redigeraren.  Redigeraren kan lägga till en resurs mallsschemat.
    - **Vanliga mallar**: Det finns fyra vanliga templatess för att skapa en Linux-dator, Windows-dator, ett webbprogram och en Azure SQL database.
    - **Läs in en snabbstartsmall för GitHub**: använda en befintlig [snabbstartsmallar](https://azure.microsoft.com/resources/templates/).

   ![Visa alternativ](./media/resource-group-template-deploy-portal/see-options.png)

    Den här självstudien innehåller instruktioner för att läsa in en snabbstartsmall.

1. Under **Läs in en snabbstartsmall för GitHub**anger eller väljer **101 – storage-konto – skapa**.

    Du kan välja mellan två alternativ:

    - **Välj mall**: distribuera mallen.
    - **Redigera mall**: redigera snabbstartsmallen innan du distribuerar den.

1. Välj **redigera mallen** att utforska mall-redigeraren. Mallen har lästs in i redigeraren. Observera att det finns två parametrar: **storageAccountType** och **plats**.

   ![Skapa mallen](./media/resource-group-template-deploy-portal/show-json.png)

1. Gör en mindre ändring för mallen. T.ex. uppdatera den **storageAccountName** variabeln:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Välj **Spara**. Nu kan du se gränssnittet mall för distribution. Lägg märke till de två parametrarna som du definierade i mallen.
1. Ange eller välj egenskapsvärdena:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resursgrupp**: Välj **Skapa nytt** och ge ett namn.
    - **Plats**: Välj en Azure-plats.
    - **Typ av lagringskonto**: Använd standardvärdet.
    - **Plats**: Använd standardvärdet.
    - **Jag godkänner villkoren ovan**: (välj)

1. Välj **Köp**.

## <a name="next-steps"></a>Nästa steg

- Om du vill visa granskningsloggar, se [granskningsåtgärder med Resource Manager](./resource-group-audit.md).
- Felsökning av vanliga fel beskrivs [visa distributionsåtgärder](./resource-manager-deployment-operations.md).
- Om du vill exportera en mall från en distribution eller en resursgrupp, se [exportera Azure Resource Manager-mallar](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Om du vill distribuera på ett säkert sätt din tjänst i flera regioner, se [Azure Deployment Manager](./deployment-manager-overview.md).
