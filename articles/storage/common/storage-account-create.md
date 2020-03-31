---
title: Skapa ett lagringskonto
titleSuffix: Azure Storage
description: Lär dig att skapa ett lagringskonto med Azure-portalen, Azure PowerShell eller Azure CLI. Ett Azure-lagringskonto innehåller ett unikt namnområde i Microsoft Azure för att lagra och komma åt dina data.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c2d1e8b4975be0657983192df00cc434da00a6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255410"
---
# <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Ett Azure-lagringskonto innehåller alla dina Azure Storage-dataobjekt: blobbar, filer, köer, tabeller och diskar. Lagringskontot tillhandahåller ett unikt namnområde för dina Azure Storage-data som är tillgängliga var som helst i världen via HTTP eller HTTPS. Data i ditt Azure-lagringskonto är hållbara och högtillgängliga, säkra och enormt skalbara.

I den här artikeln lär du dig att skapa ett lagringskonto med [Azure-portalen,](https://portal.azure.com/) [Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)eller en Azure [Resource Manager-mall](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Inga.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill skapa ett Azure-lagringskonto med PowerShell kontrollerar du att du har installerat Azure PowerShell-modulen Az version 0.7 eller senare. Mer information finns i [Introduktion till Azure PowerShell Az-modulen](/powershell/azure/new-azureps-module-az).

Så här hittar du den aktuella versionen:

```powershell
Get-InstalledModule -Name "Az"
```

Information om hur du installerar eller uppgraderar Azure PowerShell finns i [Installera Azure PowerShell-modul](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan logga in på Azure och köra Azure CLI-kommandon på ett av två sätt:

- Du kan köra CLI-kommandon från Azure-portalen i Azure Cloud Shell.
- Du kan installera CLI och köra CLI-kommandon lokalt.

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure Portal. Azure CLI är förinstallerat och konfigurerat för användning med ditt konto. Klicka på knappen **Cloud Shell** på menyn i den övre högra delen av Azure-portalen:

[![Moln skal](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Knappen startar ett interaktivt skal som du kan använda för att köra stegen i den här artikeln:

[![Skärmbild som visar cloud shell-fönstret i portalen](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. Den här how-to-artikeln kräver att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

# <a name="template"></a>[Mall](#tab/template)

Inga.

---

## <a name="sign-in-to-azure"></a>Logga in på Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Logga in på [Azure-portalen](https://portal.azure.com).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Logga in på din `Connect-AzAccount` Azure-prenumeration med kommandot och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill starta Azure Cloud Shell loggar du in på [Azure-portalen](https://portal.azure.com).

Om du vill logga in på din lokala installation av CLI kör du kommandot [az login:](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Mall](#tab/template)

Ej tillämpligt

---

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Nu är du redo att skapa ett lagringskonto.

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resursgrupp.

Ett **v2-lagringskonto för generell användning** ger åtkomst till alla Azure Storage-tjänster: blobar, filer, köer, tabeller och diskar. Stegen som beskrivs här skapar ett allmänt v2-lagringskonto, men stegen för att skapa alla typer av lagringskonto är liknande.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Skapa först en ny resursgrupp med PowerShell med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Om du inte vet vilken region du ska ange för parametern `-Location` så kan du hämta en lista över regioner som stöds för din prenumeration med kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
```

Skapa sedan ett allmänt v2-lagringskonto med skrivåtkomst geo-redundant lagring (RA-GRS) med kommandot [New-AzStorageAccount.](/powershell/module/az.storage/New-azStorageAccount) Kom ihåg att namnet på ditt lagringskonto måste vara unikt i Hela Azure, så ersätt platshållarvärdet inom parentes med ditt eget unika värde:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Om du planerar att använda Azure `-EnableHierarchicalNamespace $True` Data Lake [Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)inkluderas i den här listan med parametrar.

Om du vill skapa ett allmänt v2-lagringskonto med ett annat replikeringsalternativ ersätter du önskat värde i tabellen nedan för parametern **SkuName.**

|Replikeringsalternativ  |SkuName-parameter  |
|---------|---------|
|Lokalt redundant lagring (LRS)     |Standard_LRS         |
|Zonredundant lagring (ZRS)     |Standard_ZRS         |
|Geo-redundant lagring (GRS)     |Standard_GRS         |
|Read-Access Geo-Redundant Storage (GRS)     |Standard_RAGRS         |
|Geo-zon-redundant lagring (GZRS) (förhandsgranskning)    |Standard_GZRS         |
|Geo-zon redundant lagring (RA-GZRS) (förhandsversion)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

Skapa sedan ett allmänt v2-lagringskonto med geo redundant lagring med läsåtkomst med kommandot [az storage account create.](/cli/azure/storage/account#az_storage_account_create) Kom ihåg att namnet på ditt lagringskonto måste vara unikt i Hela Azure, så ersätt platshållarvärdet inom parentes med ditt eget unika värde:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Om du planerar att använda Azure `--enable-hierarchical-namespace true` Data Lake [Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)inkluderas i den här listan med parametrar. 

Om du vill skapa ett allmänt v2-lagringskonto med ett annat replikeringsalternativ ersätter du önskat värde i tabellen nedan för **parametern sku.**

|Replikeringsalternativ  |sku-parameter  |
|---------|---------|
|Lokalt redundant lagring (LRS)     |Standard_LRS         |
|Zonredundant lagring (ZRS)     |Standard_ZRS         |
|Geo-redundant lagring (GRS)     |Standard_GRS         |
|Read-Access Geo-Redundant Storage (GRS)     |Standard_RAGRS         |
|Geo-zon-redundant lagring (GZRS) (förhandsgranskning)    |Standard_GZRS         |
|Geo-zon redundant lagring (RA-GZRS) (förhandsversion)    |Standard_RAGZRS         |

# <a name="template"></a>[Mall](#tab/template)

Du kan använda antingen Azure Powershell eller Azure CLI för att distribuera en Resource Manager-mall för att skapa ett lagringskonto. Mallen som används i den här how-to-artikeln är från [snabbstartsmallar för Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Om du vill köra skripten väljer du **Prova det** för att öppna Azure Cloud-gränssnittet. Om du vill klistra in skriptet högerklickar du på skalet och väljer sedan **Klistra in**.

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

> [!NOTE]
> Den här mallen fungerar bara som ett exempel. Det finns många lagringskontoinställningar som inte är konfigurerade som en del av den här mallen. Om du till exempel vill använda [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)ändrar du den här mallen `isHnsEnabledad` genom att ange `StorageAccountPropertiesCreateParameters` objektets egenskap på `true`. 

Mer information om hur du ändrar den här mallen eller skapar nya finns i:

- [Azure Resource Manager-dokumentation](/azure/azure-resource-manager/).
- [Referens för lagringskontomall](/azure/templates/microsoft.storage/allversions).
- [Ytterligare exempel på lagringskontomallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Mer information om tillgängliga replikeringsalternativ finns i [Storage replication options](storage-redundancy.md) (Alternativ för lagringsreplikering).

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto

Om du tar bort ett lagringskonto tas hela kontot bort, inklusive alla data i kontot, och det går inte att ångra.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigera till lagringskontot i [Azure-portalen](https://portal.azure.com).
1. Klicka på **Ta bort**.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill ta bort lagringskontot använder du kommandot [Ta bort AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort lagringskontot använder du kommandot [ta bort az-lagringskonto:](/cli/azure/storage/account#az-storage-account-delete)

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Mall](#tab/template)

Om du vill ta bort lagringskontot använder du antingen Azure PowerShell eller Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternativt kan du ta bort resursgruppen, som tar bort lagringskontot och andra resurser i resursgruppen. Mer information om hur du tar bort en resursgrupp finns i [Ta bort resursgrupp och resurser](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Det går inte att återställa ett borttaget lagringskonto eller att hämta innehåll som det innehöll före borttagningen. Var noga med att säkerhetskopiera allt som du vill spara innan du tar bort kontot. Detta gäller även alla resurser i kontot. När du tar bort en blobb, tabell, kö eller fil tas den bort permanent.
>
> Om du försöker ta bort ett lagringskonto som är kopplat till en virtuell Azure-dator kan du få ett felmeddelande om att lagringskontot fortfarande används. Information om felsökning av det här felet finns [i Felsöka fel när du tar bort lagringskonton](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett allmänt v2-standardlagringskonto. Om du vill veta hur du laddar upp och laddar ned blobbar till och från ditt lagringskonto fortsätter du till en av snabbstarterna för Blob-lagring.

# <a name="portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure-portalen](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Arbeta med blobar med PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Mall](#tab/template)

> [!div class="nextstepaction"]
> [Arbeta med blobar med Azure-portalen](../blobs/storage-quickstart-blobs-portal.md)

---
