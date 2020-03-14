---
title: Distribuera säkert en mall med SAS-token
description: Distribuera resurser till Azure med en Azure Resource Manager-mall som skyddas av en SAS-token. Visar Azure PowerShell och Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d30e685c35f33b6fc5d3872b9287e45190ad5713
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273714"
---
# <a name="deploy-private-resource-manager-template-with-sas-token"></a>Distribuera en privat Resource Manager-mall med SAS-token

När din mall finns i ett lagrings konto kan du begränsa åtkomsten till mallen för att undvika att exponera den offentligt. Du kommer åt en skyddad mall genom att skapa en SAS-token (signatur för delad åtkomst) för mallen och tillhandahålla denna token under distributionen. Den här artikeln förklarar hur du använder Azure PowerShell eller Azure CLI för att distribuera en mall med en SAS-token.

## <a name="create-storage-account-with-secured-container"></a>Skapa lagrings konto med säker behållare

Följande skript skapar ett lagrings konto och en behållare med offentlig åtkomst inaktive rad.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Ladda upp mall till lagrings konto

Nu är du redo att ladda upp din mall till lagrings kontot. Ange sökvägen till den mall som du vill använda.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Ange SAS-token under distributionen

Om du vill distribuera en privat mall i ett lagrings konto genererar du en SAS-token och inkluderar den i URI: n för mallen. Ange förfallo tid för att tillåta tillräckligt med tid för att slutföra distributionen.

> [!IMPORTANT]
> Bloben som innehåller mallen är bara tillgänglig för kontots ägare. Men när du skapar en SAS-token för blobben, är blobben tillgänglig för alla med denna URI. Om en annan användare fångar upp URI: n, kan användaren komma åt mallen. En SAS-token är ett bra sätt att begränsa åtkomsten till dina mallar, men du bör inte inkludera känsliga data som lösen ord direkt i mallen.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Ett exempel på hur du använder en SAS-token med länkade mallar finns i [använda länkade mallar med Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Nästa steg
* En introduktion till hur du distribuerar mallar finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](deploy-powershell.md).
* Information om hur du definierar parametrar i en mall finns i [Redigera mallar](template-syntax.md#parameters).
