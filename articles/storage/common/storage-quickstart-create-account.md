---
title: Skapa ett lagringskonto – Azure Storage
description: I den här artikeln du lär dig att skapa ett lagringskonto med Azure-portalen, Azure PowerShell eller Azure CLI. Ett Azure Storage-konto tillhandahåller ett unikt namnområde i Microsoft Azure där du kan lagra och få åtkomst till de dataobjekt som du skapar i Azure Storage.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234167"
---
# <a name="create-a-storage-account"></a>skapar ett lagringskonto

Ett Azure storage-konto innehåller alla dina Azure Storage-dataobjekt: blobar, filer, köer, tabeller och diskar. Storage-konto tillhandahåller ett unikt namnområde för dina Azure Storage-data som nås från var som helst i världen via HTTP eller HTTPS. Data i Azure storage-kontot är hållbar och högtillgänglig, säker och mycket skalbar.

I den här artikeln du lär dig att skapa ett storage-konto med den [Azure-portalen](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), eller en [Azure Resource Manager mallen](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Ingen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

I den här artikeln kräver Azure PowerShell-modulen Az 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan logga in på Azure och köra Azure CLI-kommandon i ett av två sätt:

- Du kan köra CLI-kommandon från Azure-portalen i Azure Cloud Shell.
- Du kan installera CLI och köra CLI-kommandon lokalt.

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Azure CLI förinstallerat och konfigureras för användning med ditt konto. Klicka på den **Cloud Shell** -knappen på menyn i avsnittet längst upp till höger i Azure portal:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra de steg som beskrivs i den här artikeln:

[![Skärmbild som visar fönstret Cloud Shell i portalen](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. I den här artikeln kräver att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Mall](#tab/template)

Ingen.

---

## <a name="sign-in-to-azure"></a>Logga in till Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Logga in på [Azure Portal](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Logga in på Azure-prenumerationen med den `Connect-AzAccount` och följer den på skärmen att autentisera.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill starta Azure Cloud Shell, logga in på den [Azure-portalen](https://portal.azure.com).

Logga in på den lokala installationen av CLI, kör den [az-inloggning](/cli/azure/reference-index#az-login) kommando:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Mall](#tab/template)

Gäller inte

---

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Nu är du redo att skapa ditt lagringskonto.

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resursgrupp.

Ett **v2-lagringskonto för generell användning** ger åtkomst till alla Azure Storage-tjänster: blobar, filer, köer, tabeller och diskar. De steg som beskrivs här skapa ett gpv2-konto, men stegen för att skapa alla typer av storage-konto är liknande.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa först en ny resursgrupp med PowerShell med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Om du inte vet vilken region du ska ange för parametern `-Location` så kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Skapa sedan ett gpv2-lagringskonto med read-access geo-redundant lagring (RA-GRS) med hjälp av den [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) kommando. Kom ihåg att namnet på ditt lagringskonto måste vara unikt inom Azure, så ersätter platshållarvärdet inom hakparentes med ditt eget unika värde:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Skapa ett gpv2-konto med en annan replikeringsalternativ genom att ersätta det önskade värdet i tabellen nedan för den **SkuName** parametern.

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
    --name storage-resource-group \
    --location westus
```

Om du inte vet vilken region du ska ange för parametern `--location` kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Skapa sedan ett gpv2-lagringskonto med läsåtkomst till geografiskt redundant lagring med hjälp av den [az storage-konto skapar](/cli/azure/storage/account#az_storage_account_create) kommando. Kom ihåg att namnet på ditt lagringskonto måste vara unikt inom Azure, så ersätter platshållarvärdet inom hakparentes med ditt eget unika värde:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Skapa ett gpv2-konto med en annan replikeringsalternativ genom att ersätta det önskade värdet i tabellen nedan för den **sku** parametern.

|Replikeringsalternativ  |sku-parameter  |
|---------|---------|
|Lokalt redundant lagring (LRS)     |Standard_LRS         |
|Zonredundant lagring (ZRS)     |Standard_ZRS         |
|Geo-redundant lagring (GRS)     |Standard_GRS         |
|Read-Access Geo-Redundant Storage (GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Mall](#tab/template)

Du kan använda Azure Powershell eller Azure CLI för att distribuera en Resource Manager-mall för att skapa ett lagringskonto. Mallen som används i den här artikeln är från [Azure Resource Manager-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Om du vill köra skripten **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Information om hur du skapar mallar finns:

- [Dokumentation om Azure Resource Manager](/azure/azure-resource-manager/).
- [Mallreferensen för Storage-konto](/azure/templates/microsoft.storage/allversions).
- [Ytterligare lagringsutrymme konto-mallexempel](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Mer information om tillgängliga replikeringsalternativ finns i [Storage replication options](storage-redundancy.md) (Alternativ för lagringsreplikering).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som skapats av den här artikeln kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade lagringskontot och eventuella andra resurser som är kopplade till resursgruppen.

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
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Mall](#tab/template)

Använd Azure PowerShell eller Azure CLI för att ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en generell användning v2 standard storage-konto. Om du vill lära dig mer om att ladda upp och ned blobar till och från ditt lagringskonto, fortsätter du till ett Blob storage-snabbstarter.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure-portalen](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Arbeta med blobar med PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Mall](#tab/template)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure-portalen](../blobs/storage-quickstart-blobs-portal.md)

---
