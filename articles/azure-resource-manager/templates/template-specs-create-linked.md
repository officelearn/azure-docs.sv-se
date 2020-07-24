---
title: Skapa en mall-specifikation med länkade mallar
description: Lär dig hur du skapar en mall-specifikation med länkade mallar.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: c2648cb8a71be709406f314d02a226ed097be6f0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099743"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Självstudie: skapa en mall-specifikation med länkade mallar (förhands granskning)

Lär dig hur du skapar en [mall-specifikation](template-specs.md) med en [länkad mall](linked-templates.md#linked-template). Du använder mall-specifikationer för att dela ARM-mallar med andra användare i din organisation. Den här artikeln visar hur du skapar en mall-specifikation för att paketera en huvud-mall och dess länkade mallar med hjälp av den nya `relativePath` egenskapen för [distributions resursen](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Du måste [Registrera dig för för hands versionen för](https://aka.ms/templateSpecOnboarding)att kunna använda den.

## <a name="create-linked-templates"></a>Skapa länkade mallar

Skapa huvud mal len och den länkade mallen.

Om du vill länka en mall lägger du till en [distributions resurs](/azure/templates/microsoft.resources/deployments) i huvud mal len. I `templateLink` egenskapen anger du den relativa sökvägen till den länkade mallen i enlighet med sökvägen till den överordnade mallen.

Den länkade mallen kallas **linkedTemplate.jspå**och lagras i en undermapp som kallas **artefakter** i sökvägen där huvud mal len lagras.  Du kan använda något av följande värden för relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

Om det finns en annan linkedTemplate2.jspå som anropas från linkedTemplate.jspå och linkedTemplate2.jspå lagras i undermappen för artefakter, är relativePath som anges i linkedTemplate.jspå **linkedTemplate2.jspå**.

1. Skapa huvud mal len med följande JSON. Spara huvud mal len som **azuredeploy.jspå** den lokala datorn. Den här självstudien förutsätter att du har sparat till en sökväg **c:\Templates\linkedTS\azuredeploy.js** men du kan använda valfri sökväg.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
          }
        }
      },
      "variables": {
        "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Web/serverfarms",
          "apiVersion": "2016-09-01",
          "name": "[variables('appServicePlanName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "B1",
            "tier": "Basic",
            "size": "B1",
            "family": "B",
            "capacity": 1
          },
          "kind": "linux",
          "properties": {
            "perSiteScaling": false,
            "reserved": true,
            "targetWorkerCount": 0,
            "targetWorkerSizeId": 0
          }
        },
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > API version `Microsoft.Resources/deployments` måste vara 2020-06-01 eller senare.

1. Skapa en katalog med namnet **artefakter** i mappen där huvud mal len sparas.
1. Skapa den länkade mallen med följande JSON:

    ```json
    {
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
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Spara mallen som **linkedTemplate.js** i mappen **artefakter** .

## <a name="create-template-spec"></a>Skapa mall-specifikation

Specifikationer för mallar lagras i resurs grupper.  Skapa en resurs grupp och skapa sedan en mall-specifikation med följande skript. Mallens Specifikations namn är **webspec**.

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

När du är färdig kan du Visa mallens specifikation från Azure Portal eller genom att använda följande cmdlet:

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

Nu kan du distribuera mallen för mallen. distributionen av mallen är precis som att distribuera mallen den innehåller, förutom att du skickar in resurs-ID: t för mallen. Du använder samma distributions kommandon och skickar vid behov parameter värden för mallen specifikation.

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en mall specifikation som en länkad mall finns i [självstudie: Distribuera en mall specifikation som en länkad mall](template-specs-deploy-linked-template.md).
