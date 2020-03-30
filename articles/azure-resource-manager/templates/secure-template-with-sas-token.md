---
title: Distribuera mallen med SÄKER distribution med SAS-token
description: Distribuera resurser till Azure med en Azure Resource Manager-mall som skyddas av en SAS-token. Visar Azure PowerShell och Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156403"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Distribuera privat ARM-mall med SAS-token

När din AZURE Resource Manager-mall (ARM) finns i ett lagringskonto kan du begränsa åtkomsten till mallen för att undvika att exponera den offentligt. Du öppnar en säker mall genom att skapa en SAS-token (Shared Access Signature) för mallen och tillhandahålla den token under distributionen. I den här artikeln beskrivs hur du använder Azure PowerShell eller Azure CLI för att distribuera en mall med en SAS-token.

## <a name="create-storage-account-with-secured-container"></a>Skapa lagringskonto med säker behållare

Följande skript skapar ett lagringskonto och en behållare med offentlig åtkomst inaktiverad.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

## <a name="upload-template-to-storage-account"></a>Ladda upp mall till lagringskonto

Nu är du redo att ladda upp mallen till lagringskontot. Ange sökvägen till den mall som du vill använda.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

Om du vill distribuera en privat mall i ett lagringskonto genererar du en SAS-token och inkluderar den i URI för mallen. Ställ in utgångstiden så att den ger tillräckligt med tid för att slutföra distributionen.

> [!IMPORTANT]
> Bloben som innehåller mallen är endast tillgänglig för kontoägaren. Men när du skapar en SAS-token för blobben är blobben tillgänglig för alla med den URI:n. Om en annan användare fångar upp URI:n kan användaren komma åt mallen. En SAS-token är ett bra sätt att begränsa åtkomsten till dina mallar, men du bör inte inkludera känsliga data som lösenord direkt i mallen.
>

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Ett exempel på hur du använder en SAS-token med länkade mallar finns i [Använda länkade mallar med Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Nästa steg
* En introduktion till distribution av mallar finns i [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md).
* Information om hur du definierar parametrar i mallar finns i [Redigeringsmallar](template-syntax.md#parameters).
