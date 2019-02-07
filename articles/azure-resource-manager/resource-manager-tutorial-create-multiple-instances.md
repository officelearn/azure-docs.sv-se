---
title: Skapa flera resursinstanser med Azure Resource Manager | Microsoft Docs
description: Lär dig hur du skapar en Azure Resource Manager-mall för att distribuera flera resurser.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8541d66e106656f122809eec2cd547259f0ae4d7
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55495980"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>Självstudier: Skapa flera resursinstanser med Resource Manager-mallar

Lär dig hur du upprepar en Azure Resource Manager-mall för att distribuera flera instanser av en Azure-resurs. I den här självstudien ändrar du en mall för att skapa tre lagringskontoinstanser.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa stegen i den här artikeln behöver du:

* [Visual Studio Code](https://code.visualstudio.com/) med [verktygstillägget för Resource Manager](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

[Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/) är en lagringsplats för Resource Manager-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Från Visual Studio Code väljer du **Arkiv**>**Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Det finns en resurs med namnet ”Microsoft.Storage/storageAccounts” definierad i mallen. Jämför mallen med [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.
5. Välj **Arkiv**>**Spara som** för att spara filen som **azuredeploy.json** till den lokala datorn.

## <a name="edit-the-template"></a>Redigera mallen

Den befintliga mallen skapar ett lagringskonto. Du kan anpassa mallen för att skapa tre lagringskonton.  

Från Visual Studio Code gör du följande fyra ändringar:

![Azure Resource Manager skapar flera instanser](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Lägg till elementet `copy` i resursdefinitionen för lagringskontot. I kopieringselementet anger du antalet iterationer och en variabel för den här loopen. Värdet för antal måste vara ett positivt heltal och får inte överskrida 800.
2. Funktionen `copyIndex()` returnerar den aktuella iterationen i loopen. Du kan använda indexet som namnprefix. `copyIndex()` är nollbaserat. Om du vill åsidosätta indexvärdet kan du skicka ett värde i funktionen copyIndex(). Till exempel *copyIndex(1)*.
3. Ta bort elementet **variabler** eftersom det inte används längre.
4. Ta bort element **Utdata**. Det behövs inte längre.

Den färdiga mallen ser ut så här:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

Läs mer om hur du skapar flera instanser i [distribuera flera instanser av en resurs eller egenskap i Azure Resource Manager-mallar](./resource-group-create-multiple.md)

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information finns i avsnittet [Distribuera mallen](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) i Visual Studio Code-snabbstarten för distributionen.

Om du vill visa alla tre lagringskonton utelämnar du parametern --name:

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Jämför lagringskontonamn med namndefinitionen i mallen.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure-portalen väljer du **Resursgrupp** från den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** från menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I de här självstudierna lärde du dig att skapa flera instanser av ett lagringskonto. I nästa självstudie lär du dig hur du flyttar en resurs från en resursgrupp till en annan resursgrupp.

> [!div class="nextstepaction"]
> [Flytta resurser](./resource-manager-tutorial-move-resources.md)
