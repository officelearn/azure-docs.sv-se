---
title: 'Snabbstart: Skapa ett lagringskonto – Azure Storage'
description: I den här snabbstarten lär du dig att skapa ett lagringskonto med Azure-portalen, Azure PowerShell eller Azure CLI. Ett Azure Storage-konto tillhandahåller ett unikt namnområde i Microsoft Azure där du kan lagra och få åtkomst till de dataobjekt som du skapar i Azure Storage.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 5086c0758e7c535b65c877917dc790dafa46f763
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994733"
---
# <a name="create-a-storage-account"></a>skapar ett lagringskonto

I den här snabbstarten lär du dig att skapa ett lagringskonto med [Azure-portalen](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) eller [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Ingen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Den här snabbstarten kräver Azure PowerShell-modulen Az version 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan logga in på Azure och köra Azure CLI-kommandon på något av två sätt:

- Du kan köra CLI-kommandon från Azure-portalen, i Azure Cloud Shell 
- Du kan installera CLI och köra CLI-kommandon lokalt  

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Klicka på knappen **Cloud Shell** på menyn längst upp till höger i Azure-portalen:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra stegen i den här snabbstarten:

[![Skärmbild som visar fönstret Cloud Shell i portalen](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. För den här snabbstarten krävs det att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="log-in-to-azure"></a>Logga in på Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Logga in på [Azure-portalen](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Logga in på Azure-prenumerationen med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Starta Azure Cloud Shell genom att logga in på [Azure-portalen](https://portal.azure.com).

Logga in på den lokala installationen av CLI genom att köra inloggningskommandot:

```cli
az login
```

---

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Nu är du redo att skapa ditt lagringskonto.

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. I den här snabbstarten ser du hur du skapar en ny resursgrupp. 

Ett **v2-lagringskonto för generell användning** ger åtkomst till alla Azure Storage-tjänster: blobar, filer, köer, tabeller och diskar. Snabbstarten skapar ett v2-lagringskonto för generell användning, men stegen för att skapa alla typer av lagringskonton liknar dessa.   

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa först en ny resursgrupp med PowerShell med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup): 

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location 
```

Om du inte vet vilken region du ska ange för parametern `-Location` så kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location 
$location = "westus"
```

Skapa sedan ett v2-lagringskonto för generell användning med lokalt redundant lagring (LRS). Använd kommandot [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount): 

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
```

Du kan skapa ett GPv2-lagringskonto for generell användning med zonredundant lagring (ZRS) (förhandsversion), geo-redundant lagring (GRS) eller read-access geo-redundant storage (RA-GRS) genom att ersätta **SkuName**-parametern med det önskade värdet i tabellen nedan. 

|Replikeringsalternativ  |SkuName-parameter  |
|---------|---------|
|Lokalt redundant lagring (LRS)     |Standard_LRS         |
|Zonredundant lagring (ZRS)     |Standard_ZRS         |
|Geo-redundant lagring (GRS)     |Standard_GRS         |
|Read-Access Geo-Redundant Storage (GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Skapa först en ny resursgrupp med Azure CLI med kommandot [az group create](/cli/azure/group#az_group_create). 

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus
```

Om du inte vet vilken region du ska ange för parametern `--location` kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Skapa sedan ett v2-lagringskonto för generell användning med lokalt redundant lagring. Använd kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create):

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus \
    --sku Standard_LRS \
    --kind StorageV2
```

Du kan skapa ett GPv2-lagringskonto for generell användning med zonredundant lagring (ZRS förhandsversion), geo-redundant lagring (GRS) eller read-access geo-redundant storage (RA-GRS) genom att ersätta det önskade värdet i tabellen nedan med parametern **sku**-parametern. 

|Replikeringsalternativ  |sku-parameter  |
|---------|---------|
|Lokalt redundant lagring (LRS)     |Standard_LRS         |
|Zonredundant lagring (ZRS)     |Standard_ZRS         |
|Geo-redundant lagring (GRS)     |Standard_GRS         |
|Read-Access Geo-Redundant Storage (GRS)     |Standard_RAGRS         |

---

Mer information om tillgängliga replikeringsalternativ finns i [Storage replication options](storage-redundancy.md) (Alternativ för lagringsreplikering).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som har skapats med den här snabbstarten kan du helt enkelt ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade lagringskontot och eventuella andra resurser som är kopplade till resursgruppen.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Ta bort en resursgrupp med Azure-portalen:

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen **Mer** (**...**) till höger om listan.
3. Välj **Ta bort resursgrupp** och bekräfta.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot så använder du kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot, använder du kommandot [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-quickstart-resource-group
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett allmänt standardlagringskonto v2. Om du vill lära dig hur du laddar upp och ned blobar till och från lagringskontot kan du fortsätta med snabbstarten om Blob Storage.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure-portalen](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Arbeta med blobar med PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

---
