---
title: Självstudiekurs - lägga till taggar i resurser i mallen
description: Lägg till taggar i resurser som du distribuerar i din Azure Resource Manager-mall. Med taggar kan du ordna resurser logiskt.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3e0deb53e57cd29cbfce4c37f2d6c6729f15bebd
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411711"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Självstudiekurs: Lägga till taggar i arm-mallen

I den här självstudien får du lära dig hur du lägger till taggar i resurser i din AZURE Resource Manager-mall (ARM). [Taggar](../management/tag-resources.md) hjälper dig att logiskt organisera dina resurser. Taggvärdena visas i kostnadsrapporter. Den här självstudien tar **8 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om Snabbstartsmallar,](template-tutorial-quickstart-template.md)men det krävs inte.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

Din tidigare mall har distribuerat ett lagringskonto, App Service-plan och webbapp.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

När du har distribuerat dessa resurser kan du behöva spåra kostnader och hitta resurser som tillhör en kategori. Du kan lägga till taggar för att lösa dessa problem.

## <a name="add-tags"></a>Lägga till taggar

Du taggar resurser för att lägga till värden som hjälper dig att identifiera deras användning. Du kan till exempel lägga till taggar som listar miljön och projektet. Du kan lägga till taggar som identifierar ett kostnadsställe eller det team som äger resursen. Lägg till alla värden som är meningsfulla för din organisation.

I följande exempel markeras ändringarna i mallen. Kopiera hela filen och ersätt mallen med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Distribuera mallen

Det är dags att distribuera mallen och titta på resultaten.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **variabeln templateFile** till sökvägen till mallfilen, vilket visas i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgruppen från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på den vänstra menyn .
1. Välj den resursgrupp som du har distribuerat till.
1. Välj en av resurserna, till exempel lagringskontoresursen. Du ser att det nu har taggar.

   ![Visa taggar](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudiekurs behöver du inte ta bort resursgruppen.

Om du slutar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till taggar i resurserna. I nästa självstudiekurs får du lära dig hur du använder parameterfiler för att förenkla överföring av värden till mallen.

> [!div class="nextstepaction"]
> [Använd parameterfil](template-tutorial-use-parameter-file.md)
