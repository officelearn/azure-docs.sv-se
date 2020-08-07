---
title: Skapa en Premium Azure-filresurs
description: I den här artikeln får du lära dig hur du skapar en Premium Azure-filresurs med hjälp av Azure Portal, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: adeb1635489441b30c15fee69922e3abef0a53f9
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903824"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Så här skapar du en Premium Azure-filresurs
Premium-filresurser erbjuds på lagrings mediet SSD (solid-state disk) och är användbara för i/o-intensiva arbets belastningar, inklusive värd databaser och HPC (data behandling med höga prestanda). Premium-filresurser finns i en särskild typ av lagrings konto, vilket kallas ett FileStorage-konto. Premium-filresurser är utformade för högpresterande program och företags skalnings program, vilket ger konsekvent låg latens, höga IOPS och höga data flödes resurser.

Den här artikeln visar hur du skapar den här nya konto typen med hjälp av [Azure Portal](https://portal.azure.com/), Azure PowerShell och Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att få åtkomst till Azure-resurser, inklusive Premium Azure-filresurser. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Skapa en Premium-filresurs med hjälp av Azure Portal

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Skapa ett FileStorage-lagrings konto

Nu är du redo att skapa ditt lagrings konto.

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resurs grupp.

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

### <a name="create-a-premium-file-share"></a>Skapa en premium-filresurs

1. På den vänstra menyn för lagrings kontot bläddrar du till avsnittet **fil tjänst** och väljer sedan **filer**.
1. Välj **fil resurs** för att skapa en Premium-filresurs.
1. Ange ett namn och en önskad kvot för fil resursen och välj sedan **skapa**.

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten. fil resurser debiteras enligt den etablerade storleken på [sidan med pris](https://azure.microsoft.com/pricing/details/storage/files/) information för mer information.

   ![Skapa en premium-filresurs](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som skapats i den här artikeln kan du helt enkelt ta bort resurs gruppen. Om du tar bort resurs gruppen tas även det associerade lagrings kontot bort och andra resurser som är kopplade till resurs gruppen tas bort.

## <a name="create-a-premium-file-share-using-powershell"></a>Skapa en Premium-filresurs med PowerShell

### <a name="create-an-account-using-powershell"></a>Skapa ett lagringskonto med PowerShell

Installera först den senaste versionen av [PowerShellGet](/powershell/scripting/gallery/installing-psget)-modulen.

Uppgradera sedan PowerShell-modulen, logga in på din Azure-prenumeration, skapa en resurs grupp och skapa ett lagrings konto.

### <a name="upgrade-your-powershell-module"></a>Uppgradera din PowerShell-modul

Om du vill interagera med en Premium-filresurs från med PowerShell måste du installera en AZ. Storage-modul version 1.4.0 eller den senaste AZ. Storage-modulen.

Starta genom att öppna en PowerShell-session med förhöjd behörighet.

Installera modulen AZ. Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

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

### <a name="create-a-premium-file-share"></a>Skapa en premium-filresurs

Nu när du har ett FileStorage-konto kan du skapa en Premium-filresurs. Använd cmdleten [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) för att skapa en.

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten. fil resurser debiteras enligt den etablerade storleken på [sidan med pris](https://azure.microsoft.com/pricing/details/storage/files/) information för mer information.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot så använder du kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Skapa en Premium-filresurs med hjälp av Azure CLI

Logga in på [Azure Portal](https://portal.azure.com)för att starta Azure Cloud Shell.

Om du vill logga in på den lokala installationen av CLI måste du först kontrol lera att du har den senaste versionen och sedan köra inloggnings kommandot:

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

### <a name="create-a-premium-file-share"></a>Skapa en premium-filresurs

Nu när du har ett FileStorage-konto kan du skapa en Premium-filresurs. Använd kommandot [AZ Storage Share Create](/cli/azure/storage/share) för att skapa ett.

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten. fil resurser debiteras enligt den etablerade storleken på [sidan med pris](https://azure.microsoft.com/pricing/details/storage/files/) information för mer information.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot, använder du kommandot [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en Premium-filresurs. Om du vill veta mer om de prestanda som det här kontot erbjuder fortsätter du till avsnittet prestanda nivå i planerings guiden.

> [!div class="nextstepaction"]
> [Fil resurs nivåer](storage-files-planning.md#storage-tiers)
