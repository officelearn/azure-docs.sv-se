---
title: Skapa och distribuera mall-specifikation
description: Lär dig hur du skapar en mall-specifikation från ARM-mallen. Distribuera sedan mallen till en resurs grupp i din prenumeration.
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518965"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Snabb start: skapa och distribuera mall specifikation (för hands version)

Den här snabb starten visar hur du paketerar en Azure Resource Manager-mall (ARM-mall) i en [mall-specifikation](template-specs.md) och sedan distribuerar mallen. Din Template-specifikation innehåller en ARM-mall som distribuerar ett lagrings konto.

## <a name="prerequisites"></a>Krav

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Om du vill använda den med Azure PowerShell måste du installera [version 5.0.0 eller senare](/powershell/azure/install-az-ps). Använd [version 2.14.2 eller senare](/cli/azure/install-azure-cli)för att använda den med Azure CLI.

## <a name="create-template-spec"></a>Skapa mall-specifikation

Mallens specifikation är en resurs typ med namnet **Microsoft. Resources/templateSpecs**. Om du vill skapa din mall specifikation kan du använda Azure Portal, Azure PowerShell, Azure CLI eller en ARM-mall. I alla alternativ behöver du en ARM-mall som är paketerad i mallen specifikation.

Med PowerShell och CLI skickas ARM-mallen som en parameter till kommandot. Med ARM-mall är ARM-mallen som ska paketeras i specifikationen för mallen inbäddad i definitionen av mallen definition.

Dessa alternativ visas nedan.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Längst upp på skärmen, i **Sök resurser, tjänster och dokument**, anger du **specifikationer för mallar** och väljer sedan **specifikationer för mallar**.
1. Välj **Skapa mall-specifikation**.
1. Välj eller ange följande värden:

    - **Namn**: Ange ett namn för mall-specifikationen.  Till exempel **storageSpec**
    - **Prenumeration**: Välj en Azure-prenumeration som används för att skapa mallen specifikation.
    - **Resurs grupp**: Välj **Skapa ny** och ange sedan ett nytt resurs grupp namn.  Till exempel **templateSpecRG**.
    - **Plats**: Välj en plats för resursgruppen. Till exempel  **USA, västra 2**.
    - **Version**: Ange en version för mall-specifikationen. Till exempel **1,0** eller **v 1.0**.

1. Välj **Nästa: Redigera mall**.
1. Ersätt mal linne hållet med följande JSON:

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    Detta är den mall som kommer att paketeras i specifikationen för mallen.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. När du skapar en mall-specifikation med PowerShell kan du skicka in en lokal mall. Kopiera följande mall och spara den lokalt i en fil med namnet **azuredeploy.jspå**. Den här snabb starten förutsätter att du har sparat till en sökväg **c:\Templates\azuredeploy.js** men du kan använda valfri sökväg.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Skapa en ny resurs grupp som innehåller mallens specifikation.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Skapa sedan mallen specifikation i den resurs gruppen. Du ger den nya mallen specifikation av namnet **storageSpec**.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. När du skapar en mall-specifikation med CLI kan du skicka i en lokal mall. Kopiera följande mall och spara den lokalt i en fil med namnet **azuredeploy.jspå**. Den här snabb starten förutsätter att du har sparat till en sökväg **c:\Templates\azuredeploy.js** men du kan använda valfri sökväg.

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. Skapa en ny resurs grupp som innehåller mallens specifikation.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Skapa sedan mallen specifikation i den resurs gruppen. Du ger den nya mallen specifikation av namnet **storageSpec**.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[ARM-mall](#tab/azure-resource-manager)

1. När du använder en ARM-mall för att skapa en mall, bäddas mallen in i resurs definitionen. Kopiera följande mall och spara den lokalt som **azuredeploy.jspå**. Den här snabb starten förutsätter att du har sparat till en sökväg **c:\Templates\azuredeploy.js** men du kan använda valfri sökväg.

    > [!NOTE]
    > I den inbäddade mallen måste alla [Template-uttryck](template-expressions.md) föregås av en andra vänster hak paren tes. Använd `"[[` i stället för `"[` . JSON-matriser använder fortfarande en enda vänster hak paren tes.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Använd Azure CLI eller PowerShell för att skapa en ny resurs grupp.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Distribuera din mall med Azure CLI eller PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

Nu kan du distribuera mallens specifikation. Att distribuera mallens specifikation är precis som att distribuera mallen den innehåller, förutom att du skickar in resurs-ID för mallens specifikation i Azure PowerShell eller Azure CLI. Du använder samma distributions kommandon och skickar vid behov parameter värden för mallen specifikation.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Öppna resurs gruppen som du skapade i den senaste proceduren från Azure Portal.  Till exempel **templateSpecRG**.
1. Välj den mall specifikation som du skapade. Till exempel **storageSpec**.
1. Välj **Distribuera**.
1. Välj eller ange följande värden:

    - **Prenumeration**: Välj en Azure-prenumeration för att skapa resursen.
    - **Resurs grupp**: Välj **Skapa ny** och ange sedan **storageRG**.
    - **Typ av lagrings konto**: Välj **Standard_GRS**.

    Du skapar en ny resurs grupp och distribuerar mallen i mallen specifikation till den nya resurs gruppen.

1. Välj **Granska + skapa**.
1. Välj **Skapa**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Skapa en resurs grupp som innehåller det nya lagrings kontot.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Hämta resurs-ID för mallens specifikation.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Distribuera mallen specifikation.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Du anger parametrar exakt som för en ARM-mall. Distribuera om mallen specifikation med en parameter för lagrings konto typen.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Skapa en resurs grupp som innehåller det nya lagrings kontot.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Hämta resurs-ID för mallens specifikation.

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Det finns ett känt problem med att hämta ett Specifikations-ID för mallen och tilldela det till en variabel i Windows PowerShell.

1. Distribuera mallen specifikation.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Du anger parametrar exakt som för en ARM-mall. Distribuera om mallen specifikation med en parameter för lagrings konto typen.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[ARM-mall](#tab/azure-resource-manager)

1. Kopiera följande mall och spara den lokalt i en fil med namnet **storage.jspå**.

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Använd Azure CLI eller PowerShell för att skapa en ny resurs grupp för lagrings kontot.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Distribuera din mall med Azure CLI eller PowerShell.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Bevilja åtkomst

Om du vill låta andra användare i din organisation distribuera din malls specifikation måste du ge dem Läs behörighet. Du kan tilldela rollen läsare till en Azure AD-grupp för den resurs grupp som innehåller de mall-specifikationer som du vill dela. Mer information finns i [Självstudier: bevilja en grupp åtkomst till Azure-resurser med hjälp av Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort båda resurs grupper som du har skapat för att rensa den resurs som du har distribuerat i den här snabb starten.

1. Från Azure-portalen väljer du Resursgrupp från den vänstra menyn.

1. Ange resurs gruppens namn (templateSpecRG och storageRG) i fältet filtrera efter namn.

1. Välj resursgruppens namn.

1. Välj Ta bort resursgrupp från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en mall-specifikation som innehåller länkade mallar finns i [skapa en mall specifikation av en länkad mall](template-specs-create-linked.md).
