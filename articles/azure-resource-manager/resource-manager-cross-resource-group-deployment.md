---
title: Distribuera Azure-resurser till flera resursgrupper | Microsoft Docs
description: "Visar hur du kan arbeta mer än en Azure-resursgrupp under distributionen."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: tomfitz
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>Distribuera Azure-resurser till mer än en resursgrupp

Normalt distribuerar du alla resurser i mallen som en enskild resursgrupp. Det finns emellertid scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper. Exempelvis kanske du vill distribuera den säkerhetskopiera virtuella för Azure Site Recovery till en separat resursgrupp och plats. Resource Manager kan du använda kapslade mallar för olika resursgrupper än den resursgrupp som används för den överordnade mallen.

Resursgruppen är en behållare för livscykeln för programmet och dess mängd resurser. Du skapar resursgruppen utanför mallen och ange resursgrupp som mål under distributionen. En introduktion till resursgrupper finns [översikt över Azure Resource Manager](resource-group-overview.md).

## <a name="example-template"></a>Exempelmall

Om du vill ange en annan resurs, måste du använda en mall för kapslade eller länkade under distributionen. Den `Microsoft.Resources/deployments` resurstypen ger en `resourceGroup` parameter som kan du ange en annan resursgrupp för den kapslade distributionen. Alla resursgrupper måste finnas innan du kör distributionen. I följande exempel distribuerar två lagringskonton - en i resursgruppen som anges under distributionen, och en i en resursgrupp med namnet `crossResourceGroupDeployment`:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

Om du ställer in `resourceGroup` till namnet på en resursgrupp som inte finns misslyckas distributionen. Om du inte anger ett värde för `resourceGroup`, Resource Manager använder överordnade resursgruppen.  

## <a name="deploy-the-template"></a>Distribuera mallen

Du kan använda portalen, Azure PowerShell eller Azure CLI för att distribuera mallen exempel. För Azure PowerShell eller Azure CLI, måste du använda en Versionspost från maj 2017 eller senare. I exemplen antar vi att du har sparat mallen lokalt som en fil med namnet **crossrgdeployment.json**.

För PowerShell:

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

För Azure CLI:

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

När distributionen är klar kan du se två resursgrupper. Varje resursgrupp innehåller ett lagringskonto.

## <a name="use-resourcegroup-function"></a>Funktionen resourceGroup()

Mellan distributionen av resursgrupper, för den [resouceGroup() funktionen](resource-group-template-functions-resource.md#resourcegroup) matchar på olika sätt beroende på hur du anger den kapslade mallen. 

Om du bäddar in en mall i en annan mall löser resouceGroup() i den kapslade mallen till den överordnade resursgruppen. Ett inbäddat mallen använder följande format:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Om du länkar till en separat mall löser resouceGroup() i mallen länkade till den kapslade resursgruppen. En länkad mall används följande format:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du definierar parametrar i mallen finns [förstå struktur och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns [distribuera privata mallar med SAS-token](resource-manager-powershell-sas-token.md).
