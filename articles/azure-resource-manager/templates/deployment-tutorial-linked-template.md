---
title: Självstudiekurs - Distribuera en länkad mall
description: Lär dig hur du distribuerar en länkad mall
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 177a994450b6ffe5489a8c95c3b484521fd9b77b
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672920"
---
# <a name="tutorial-deploy-a-linked-template"></a>Självstudiekurs: Distribuera en länkad mall

I [föregående självstudier](./deployment-tutorial-local-template.md)har du lärt dig hur du distribuerar en mall som lagras på den lokala datorn. Om du vill distribuera komplexa lösningar kan du dela upp en mall i många mallar och distribuera dessa mallar via en huvudmall. I den här självstudien får du lära dig hur du distribuerar en huvudmall som innehåller referensen till en länkad mall. När huvudmallen distribueras utlöser den distributionen av den länkade mallen. Du lär dig också hur du lagrar och skyddar den länkade mallen med hjälp av SAS-token. Det tar cirka **12 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför föregående självstudie, men det är inte nödvändigt.

## <a name="review-template"></a>Mall för granskning

I föregående självstudier distribuerar du en mall som skapar ett lagringskonto, App Service-plan och webbapp. Mallen som användes var:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Skapa en länkad mall

Du kan dela upp lagringskontoresursen i en länkad mall:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Följande mall är huvudmallen.  Det markerade **Microsoft.Resources/deployments-objektet** visar hur du anropar en länkad mall. Den länkade mallen kan inte lagras som en lokal fil eller en fil som bara är tillgänglig i det lokala nätverket. Du kan bara ange ett URI-värde som innehåller antingen *http* eller *https*. Resurshanteraren måste kunna komma åt mallen. Ett alternativ är att placera den länkade mallen i ett lagringskonto och använda URI för det objektet. URI skickas till mallen med hjälp av en parameter. Se den markerade parameterdefinitionen.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Spara en kopia av huvudmallen på den lokala datorn med tillägget .json, till exempel azuredeploy.json. Du behöver inte spara en kopia av den länkade mallen.  Den länkade mallen kopieras från en GitHub-databas till ett lagringskonto.

## <a name="store-the-linked-template"></a>Lagra den länkade mallen

Följande PowerShell-skript skapar ett lagringskonto, skapar en behållare och kopierar den länkade mallen från en GitHub-databas till behållaren. En kopia av den länkade mallen lagras i [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Välj Prova om du vill öppna Cloud **Shell,** välj **Kopiera** för att kopiera PowerShell-skriptet och högerklicka på skalfönstret för att klistra in skriptet:

> [!IMPORTANT]
> Namn på lagringskonto måste vara mellan 3 och 24 tecken långa och endast med siffror och gemener. Namnet måste vara unikt. I mallen är lagringskontonamnet projektnamnet med "store" bifogat och projektnamnet måste vara mellan 3 och 11 tecken. Så projektnamnet måste uppfylla kraven för lagringskontonamn och har färre än 11 tecken.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Distribuera mallen

Om du vill distribuera en privat mall i ett lagringskonto genererar du en SAS-token och inkluderar den i URI för mallen. Ställ in utgångstiden så att den ger tillräckligt med tid för att slutföra distributionen. Bloben som innehåller mallen är endast tillgänglig för kontoägaren. Men när du skapar en SAS-token för blobben är blobben tillgänglig för alla med den URI:n. Om en annan användare fångar upp URI:n kan användaren komma åt mallen. En SAS-token är ett bra sätt att begränsa åtkomsten till dina mallar, men du bör inte inkludera känsliga data som lösenord direkt i mallen.

Om du inte har skapat resursgruppen läser du [Skapa resursgrupp](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> I nedanstående Azure CLI-kod är datumparameter -d ett ogiltigt argument i macOS. Så macOS-användare, för att lägga till 2 timmar till aktuell tid i terminalen på macOS bör du använda -v+2H.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Välj **Resursgrupp** på den vänstra menyn på Azure-portalen.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den övre menyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar en länkad mall. I nästa självstudiekurs får du lära dig hur du skapar en DevOp-pipeline för att distribuera en mall.

> [!div class="nextstepaction"]
> [Skapa en pipeline](./deployment-tutorial-pipeline.md)
