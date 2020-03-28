---
title: Skapa flera resursinstanser
description: Lär dig hur du skapar en Azure Resource Manager-mall för att distribuera flera resurser.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9ed14ce1af6421accccface1b66119057d1c5a30
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239300"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Självstudiekurs: Skapa flera resursinstanser med ARM-mallar

Lär dig hur du iterererar i din ARM-mall (Azure Resource Manager) för att skapa flera instanser av en Azure-resurs. I den här självstudien ändrar du en mall för att skapa tre lagringskontoinstanser.

![Azure Resource Manager skapar flera instanser diagram](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio-kod med resurshanterarens verktygstillägg. Se [Använda Visual Studio-kod för att skapa ARM-mallar](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

[Azure QuickStart-mallar](https://azure.microsoft.com/resources/templates/) är en databas för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Välj **Öppna**>**fil**i Visual Studio-kod .
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Välj **Öppna** för att öppna filen.
4. Det finns en resurs med namnet ”Microsoft.Storage/storageAccounts” definierad i mallen. Jämför mallen med [mallreferensen](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Det är bra att få viss grundläggande förståelse av mallen innan den anpassas.
5. Välj **Spara fil**>**som** om du vill spara filen som **azuredeploy.json** på den lokala datorn.

## <a name="edit-the-template"></a>Redigera mallen

Den befintliga mallen skapar ett lagringskonto. Du kan anpassa mallen för att skapa tre lagringskonton.

Från Visual Studio Code gör du följande fyra ändringar:

![Azure Resource Manager skapar flera instanser](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

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

Mer information om hur du skapar flera instanser finns i [Distribuera flera instanser av en resurs eller egenskap i ARM-mallar](./copy-resources.md)

## <a name="deploy-the-template"></a>Distribuera mallen

Mer information finns i avsnittet [Distribuera mallen](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) i Visual Studio Code-snabbstarten för distributionen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill visa alla tre lagringskonton utelämnar du parametern --name:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
```

---

Jämför lagringskontonamn med namndefinitionen i mallen.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

I de här självstudierna lärde du dig att skapa flera instanser av ett lagringskonto.  I nästa självstudie utvecklar du en mall med flera resurser och flera resurstyper. Några av resurserna har beroende resurser.

> [!div class="nextstepaction"]
> [Skapa beroende resurser](./template-tutorial-create-templates-with-dependent-resources.md)
