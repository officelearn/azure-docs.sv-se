---
title: Självstudiekurs - Distribuera en lokal Azure Resource Manager-mall
description: Lär dig hur du distribuerar en Azure Resource Manager-mall från den lokala datorn
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c8e3eb62fa52caeaa63808b6b9ea199bdff5c4da
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082260"
---
# <a name="tutorial-deploy-a-local-azure-resource-manager-template"></a>Självstudiekurs: Distribuera en lokal Azure Resource Manager-mall

Lär dig hur du distribuerar en Azure Resource Manager-mall från den lokala datorn. Det tar ca **8 minuter** att slutföra.

Den här självstudien är den första i en serie. När du går igenom serien, du modularisera mallen genom att skapa en länkad mall, du lagrar den länkade mallen i ett lagringskonto och säkra den länkade mallen med hjälp av SAS-token, och du lär dig hur du skapar en DevOp-pipeline för att distribuera mallar. Den här serien fokuserar på malldistribution.  Om du vill lära dig mall utveckling, se [nybörjare tutorials](./template-tutorial-create-first-template.md).

## <a name="get-tools"></a>Hämta verktyg

Låt oss börja med att se till att du har de verktyg du behöver för att distribuera mallar.

### <a name="command-line-deployment"></a>Distribution av kommandorad

Du behöver antingen Azure PowerShell eller Azure CLI för att distribuera mallen. Instruktioner för installationen finns i:

- [Installera Azure PowerShell](/powershell/azure/install-az-ps)
- [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
- [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)

När du har installerat Azure PowerShell eller Azure CLI kontrollerar du att du loggar in för första gången. Hjälp finns [i Logga in - PowerShell](/powershell/azure/install-az-ps#sign-in) eller [Logga in - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

### <a name="editor-optional"></a>Redigerare (valfritt)

Mallar är JSON-filer. Om du vill granska/redigera mallar behöver du en bra JSON-redigerare. Vi rekommenderar Visual Studio-kod med tillägget Resource Manager Tools. Om du behöver installera dessa verktyg läser du [Använda Visual Studio-kod för att skapa Azure Resource Manager-mallar](use-vs-code-to-create-template.md).

## <a name="review-template"></a>Mall för granskning

Mallen som används i den här självstudien liknar den mall som används i [självstudien om snabbstartsmallar](template-tutorial-quickstart-template.md). Om du är intresserad av att skapa mallen kan du gå igenom den självstudien. Men det är inte nödvändigt för att slutföra den här guiden.

Mallen distribuerar ett lagringskonto, apptjänstplan och webbapp.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

> [!IMPORTANT]
> Namn på lagringskonto måste vara mellan 3 och 24 tecken långa och endast med siffror och gemener. Namnet måste vara unikt. I mallen är lagringskontonamnet projektnamnet med "store" bifogat och projektnamnet måste vara mellan 3 och 11 tecken. Så projektnamnet måste uppfylla kraven för lagringskontonamn och har färre än 11 tecken.

Spara en kopia av mallen på den lokala datorn med tillägget .json, till exempel azuredeploy.json. Du distribuerar den här mallen senare i självstudien.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du vill börja arbeta med Azure PowerShell/Azure CLI för att distribuera en mall loggar du in med dina Azure-autentiseringsuppgifter.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Om du har flera Azure-prenumerationer väljer du den prenumeration du vill använda:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Select-AzSubscription [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Skapa resursgrupp

När du distribuerar en mall anger du en resursgrupp som ska innehålla resurserna. Innan du kör distributionskommandot skapar du resursgruppen med antingen Azure CLI eller Azure PowerShell. Välj flikarna i följande kodavsnitt för att välja mellan Azure PowerShell och Azure CLI. CLI-exemplen i den här artikeln är skrivna för Bash-skalet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource and resource group names"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup `
  -Name $resourceGroupName `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource and resource group names:"
read projectName
resourceGroupName="${projectName}rg"

az group create \
  --name $resourceGroupName \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Distribuera mallen

Använd ett eller båda distributionsalternativen för att distribuera mallen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroupDeployment `
  -Name DeployLocalTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -verbose
```

Mer information om hur du distribuerar mall med hjälp av Azure PowerShell finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](./deploy-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter the same project name:"
read projectName
echo "Enter the template file path and file name:"
read templateFile
resourceGroupName="${projectName}rg"

az deployment group create \
  --name DeployLocalTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName \
  --verbose
```

Mer information om hur du distribuerar mall med hjälp av Azure CLI finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](./deploy-cli.md).

---

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar en lokal mall. I nästa självstudiekurs delar du upp mallen i en huvudmall och en länkad mall och lär dig hur du lagrar och säkrar den länkade mallen.

> [!div class="nextstepaction"]
> [Distribuera en länkad mall](./deployment-tutorial-linked-template.md)
