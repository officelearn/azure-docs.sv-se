---
title: Självstudiekurs - använd parameterfil för att distribuera mall
description: Använd parameterfiler som innehåller de värden som ska användas för att distribuera din Azure Resource Manager-mall.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b91041b96a3819dbace3898d92226f0351f0f973
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411510"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Självstudiekurs: Använda parameterfiler för att distribuera ARM-mallen

I den här självstudien får du lära dig hur du använder [parameterfiler](parameter-files.md) för att lagra de värden som du skickar in under distributionen. I föregående självstudier använde du infogade parametrar med distributionskommandot. Den här metoden arbetade för att testa din AZURE Resource Manager-mall (ARM), men när du automatiserar distributioner kan det vara enklare att skicka en uppsättning värden för din miljö. Parameterfiler gör det enklare att paketera parametervärden för en viss miljö. I den här självstudien ska du skapa parameterfiler för utvecklings- och produktionsmiljöer. Det tar cirka **12 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om taggar,](template-tutorial-add-tags.md)men det är inte nödvändigt.

Du måste ha Visual Studio-kod med tillägget Resource Manager Tools och antingen Azure PowerShell eller Azure CLI. Mer information finns i [mallverktyg](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Mall för granskning

Mallen har många parametrar som du kan tillhandahålla under distributionen. I slutet av föregående självstudie, din mall såg ut som:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Den här mallen fungerar bra, men nu vill du enkelt hantera de parametrar som du skickar in för mallen.

## <a name="add-parameter-files"></a>Lägga till parameterfiler

Parameterfiler är JSON-filer med en struktur som liknar mallen. I filen anger du de parametervärden som du vill skicka in under distributionen.

Skapa en ny fil med följande innehåll i VS-kod. Spara filen med namnet **azuredeploy.parameters.dev.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Den här filen är din parameterfil för utvecklingsmiljön. Observera att den använder Standard_LRS för lagringskontot, namnger resurser med ett **dev-prefix** och anger **miljötaggen** till **Dev**.

Skapa en ny fil med följande innehåll igen. Spara filen med namnet **azuredeploy.parameters.prod.json**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Den här filen är parameterfilen för produktionsmiljön. Observera att den använder Standard_GRS för lagringskontot, namnger resurser med ett **contoso-prefix** och anger **miljötaggen** till **Produktion**. I en verklig produktionsmiljö vill du också använda en apptjänst med en SKU annat än gratis, men vi fortsätter att använda den SKU för den här självstudien.

## <a name="deploy-template"></a>Distribuera mallen

Använd antingen Azure CLI eller Azure PowerShell för att distribuera mallen.

Som ett sista test av mallen ska vi skapa två nya resursgrupper. En för utvecklingsmiljön och en för produktionsmiljön.

Först distribuerar vi till utvecklingsmiljön.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Nu distribuerar vi till produktionsmiljön.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **felsökningsväxeln** med distributionskommandot för att visa felsökningsloggarna.  Du kan också använda **den utförliga** växeln för att visa de fullständiga felsökningsloggarna.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgrupperna från Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Resursgrupper**på den vänstra menyn .
1. Du ser de två nya resursgrupper som du har distribuerat i den här självstudien.
1. Välj antingen resursgrupp och visa de distribuerade resurserna. Observera att de matchar de värden som du har angett i parameterfilen för den miljön.

## <a name="clean-up-resources"></a>Rensa resurser

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**. Om du har slutfört den här serien har du tre resursgrupper att ta bort - myResourceGroup, myResourceGroupDev och myResourceGroupProd.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här introduktionen för att distribuera mallar till Azure. Låt oss veta om du har några kommentarer och förslag i feedbackavsnittet. Tack!

Nästa självstudieserie går in mer i detalj om hur du distribuerar mallar.

> [!div class="nextstepaction"]
> [Distribuera en lokal mall](./deployment-tutorial-local-template.md)
