---
title: Distribuera resurser till Azure | Microsoft Docs
description: Distribuera resurser till Azure med Azure PowerShell eller Azure CLI. Resurserna definieras i en Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 3b3162c335be43c9bf2a3d1c14b86cdd9b5d46b9
ms.lasthandoff: 04/27/2017


---
# <a name="deploy-resources-to-azure"></a>Distribuera resurser till Azure

I det här avsnittet beskrivs hur du distribuerar resurser till en Azure-prenumeration. Du kan distribuera en Resource Manager-mall med hjälp av antingen Azure PowerShell eller Azure CLI. Mallen definierar infrastrukturen för din lösning.

En introduktion till Resource Manager finns i [Översikt över Azure Resource Manager](resource-group-overview.md).

## <a name="steps-for-deployment"></a>Distributionsanvisningar

Det här avsnittet förutsätter att du distribuerar [exempellagringsmallen](#example-storage-template) i det här avsnittet. Du kan använda en annan mall, men de parametrar som du anger kommer i så fall inte vara desamma som de som visas i det här avsnittet.

När du har skapat en mall distribuerar du den på följande sätt (allmänna anvisningar):

1. Logga in på ditt konto
2. Välj den prenumeration som du vill använda (krävs endast om du har flera prenumerationer och vill använda den som inte är standard)
3. Skapa en resursgrupp
4. Distribuera mallen
5. Kontrollera status för distributionen

I följande avsnitt visas hur du utför de här stegen med [PowerShell](#powershell) eller [Azure CLI](#azure-cli).

## <a name="powershell"></a>PowerShell

1. Om du vill installera Azure PowerShell kan du läsa [Komma igång med Azure PowerShell-cmdlets](/powershell/azure/overview).

2. Använd följande cmdlets för att snabbt komma igång med distributionen:

  ```powershell
  Login-AzureRmAccount
  Set-AzureRmContext -SubscriptionID {subscription-id}

  New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json 
  ```

  Cmdleten `Set-AzureRmContext` behövs bara om du vill använda en annan prenumeration än din standardprenumeration. Använd följande om du vill se alla dina prenumerationer och deras ID:

  ```powershell
  Get-AzureRmSubscription
  ```

3. Det kan ta några minuter att slutföra distributionen. När distributionen är klar visas ett meddelande som ser ut ungefär så här:

  ```powershell
  DeploymentName          : ExampleDeployment
  CorrelationId           : 07b3b233-8367-4a0b-b9bc-7aa189065665
  ResourceGroupName       : ExampleGroup
  ProvisioningState       : Succeeded
  ...
  ```

4. Använd följande för att kontrollera att din resursgrupp och ditt lagringskonto har distribuerats till din prenumeration:

  ```powershell
  Get-AzureRmResourceGroup -Name ExampleGroup
  Find-AzureRmResource -ResourceGroupNameEquals ExampleGroup
  ```

5. Du kan ange mallparametrar som PowerShell-parametrar när du distribuerar en mall. Eftersom det inte fanns några mallparametrar i det föregående exemplet användes i stället standardvärdena i mallen. Om du vill distribuera ett annat lagringskonto och ange parametervärden för lagringsnamnprefixet och SKU för lagringskontot använder du:

  ```powershell
  New-AzureRmResourceGroupDeployment -Name ExampleDeployment2 -ResourceGroupName ExampleGroup -TemplateFile c:\MyTemplates\azuredeploy.json -storageNamePrefix "contoso" -storageSKU "Standard_GRS"
  ```

  Nu har du två lagringskonton i resursgruppen. 

## <a name="azure-cli"></a>Azure CLI

1. Information om hur du installerar Azure CLI finns i [Installera Azure CLI 2.0](/cli/azure/install-az-cli2).

2. Använd följande kommandon för att snabbt komma igång med distributionen:

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create --name ExampleDeployment --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json
  ```

  Kommandot `az account set` behövs bara om du vill använda en annan prenumeration än din standardprenumeration. Använd följande om du vill se alla dina prenumerationer och deras ID:

  ```azurecli
  az account list
  ```

3. Det kan ta några minuter att slutföra distributionen. När distributionen är klar visas ett meddelande som ser ut ungefär så här:

  ```azurecli
  "provisioningState": "Succeeded",
  ```

4. Använd följande för att kontrollera att din resursgrupp och ditt lagringskonto har distribuerats till din prenumeration:

  ```azurecli
  az group show --name ExampleGroup
  az resource list --resource-group ExampleGroup
  ```

5. Du kan ange mallparametrar som PowerShell-parametrar när du distribuerar en mall. Eftersom det inte fanns några mallparametrar i det föregående exemplet användes i stället standardvärdena i mallen. Om du vill distribuera ett annat lagringskonto och ange parametervärden för lagringsnamnprefixet och SKU för lagringskontot använder du:

  ```azurecli
  az group deployment create --name ExampleDeployment2 --resource-group ExampleGroup --template-file c:\MyTemplates\azuredeploy.json --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  Nu har du två lagringskonton i resursgruppen. 

## <a name="example-storage-template"></a>Exempellagringsmall

Använd följande exempelmall för att distribuera ett lagringskonto till din prenumeration:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Nästa steg

* Detaljerad information om hur du distribuerar mallar med PowerShell finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](/azure/azure-resource-manager/resource-group-template-deploy).
* Detaljerad information om hur du distribuerar mallar med Azure CLI finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](/azure/azure-resource-manager/resource-group-template-deploy-cli).




