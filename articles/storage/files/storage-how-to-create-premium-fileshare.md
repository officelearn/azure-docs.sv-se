---
title: Skapa en Premium Azure-filresurs
description: Lär dig hur du skapar en Azure Premium-filresurs med hjälp av Azure Portal, Azure PowerShell modulen eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626800"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Så här skapar du en Azure Premium-filresurs

Premium-filresurser erbjuds på lagrings mediet SSD (solid-state disk) och är användbara för i/o-intensiva arbets belastningar, inklusive värd databaser och HPC (data behandling med höga prestanda). Premium-filresurser finns i en särskild typ av lagrings konto, vilket kallas ett FileStorage-konto. Premium-filresurser är utformade för högpresterande program och företags skalnings program, vilket ger konsekvent låg latens, höga IOPS och höga data flödes resurser.

Den här artikeln visar hur du skapar den här nya konto typen med hjälp av [Azure Portal](https://portal.azure.com/), Azure PowerShell modulen och Azure CLI.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Om du tänker använda Azure CLI [installerar du den senaste versionen](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Om du tänker använda Azure PowerShell-modulen [installerar du den senaste versionen](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Skapa ett FileStorage-lagrings konto

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Premium fil resurser kräver ett FileStorage-konto.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).

Nu är du redo att skapa ditt lagrings konto.

1. I Azure Portal väljer du **lagrings konton** på den vänstra menyn.

    ![Azure Portal huvud sida Välj lagrings konto](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
1. Välj den prenumeration där du vill skapa lagringskontot.
1. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resursgrupp som du ser i följande bild.

1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagringskonto eller använd standardplatsen.
1. För **prestanda** väljer du **Premium**.

    Du måste välja **Premium** för att **FileStorage** ska vara ett tillgängligt alternativ i list rutan **typ av konto** .

1. Välj **typ av konto** och välj **FileStorage**.
1. Lämna **replikeringsuppsättningen** till standardvärdet för **lokalt REDUNDANT lagring (LRS)**.

    ![Så här skapar du ett lagrings konto för en Premium-filresurs](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
1. Välj **Skapa**.

När lagrings konto resursen har skapats navigerar du till den.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Logga in på Azure

Använd kommandot `Connect-AzAccount` och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du vill skapa en resursgrupp med PowerShell använder du kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Skapa ett FileStorage-lagrings konto

Om du vill skapa ett FileStorage lagrings konto från PowerShell använder du kommandot [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) :

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Logga in på [Azure Portal](https://portal.azure.com)för att starta Azure Cloud Shell.

Om du vill logga in på den lokala installationen av CLI kontrollerar du att du har den senaste versionen och loggar sedan in:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du vill skapa en ny resursgrupp med Azure CLI använder du kommandot [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Skapa ett FileStorage-lagrings konto

Om du vill skapa ett FileStorage lagrings konto från Azure CLI använder du kommandot [AZ Storage Account Create](/cli/azure/storage/account) .

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Hämta lagringskontonyckeln

Lagrings konto nycklar styr åtkomsten till resurser i ett lagrings konto. i den här artikeln använder vi nyckeln för att skapa en Premium-fil resurs. Nycklarna skapas automatiskt när du skapar ett lagringskonto. Du kan hämta lagringskontonycklarna till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>Skapa en premium-filresurs

Nu när du har skapat ett FileStorage-konto kan du skapa en Premium-filresurs inom det lagrings kontot.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. På den vänstra menyn för lagrings kontot bläddrar du till avsnittet **fil tjänst** och väljer sedan **filer**.
1. Välj **fil resurs** för att skapa en Premium-filresurs.
1. Ange ett namn och en önskad kvot för fil resursen och välj sedan **skapa**.

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten och fil resurserna debiteras enligt den allokerade storleken. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Skapa en premium-filresurs](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill skapa en Premium-filresurs med Azure PowerShell-modulen använder du cmdleten [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) .

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten och fil resurserna debiteras enligt den allokerade storleken. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa en Premium-filresurs med Azure CLI använder du kommandot [AZ Storage Share Create](/cli/azure/storage/share) .

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten och fil resurserna debiteras enligt den allokerade storleken. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Rensa resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ta bort resurs gruppen om du vill rensa resurserna som du skapade i den här artikeln. Om du tar bort resurs gruppen tas även det associerade lagrings kontot bort och andra resurser som är kopplade till resurs gruppen tas bort.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ta bort resurs gruppen om du vill rensa resurserna som du skapade i den här artikeln. Om du tar bort resurs gruppen tas även det associerade lagrings kontot bort och andra resurser som är kopplade till resurs gruppen tas bort.

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot så använder du kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta bort resurs gruppen om du vill rensa resurserna som du skapade i den här artikeln. Om du tar bort resurs gruppen tas även det associerade lagrings kontot bort och andra resurser som är kopplade till resurs gruppen tas bort.

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot, använder du kommandot [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en Premium-filresurs. Om du vill veta mer om de prestanda som det här kontot erbjuder fortsätter du till avsnittet prestanda nivå i planerings guiden.

> [!div class="nextstepaction"]
> [Fil resurs nivåer](storage-files-planning.md#storage-tiers)