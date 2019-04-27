---
title: Skapa ett lagringskonto för Azure premium-fil
description: I den här artikeln får du lära dig hur du skapar ett Azure premium-filen storage-konto och en premium-filresurs.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844567"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Så här skapar du en Azure premium-filresurs

Lagringskontotypen FileStorage (förhandsversion) representerar en ny nivå för Azure Files, så att du kan skapa filresurser med premium prestandaegenskaper. Dessa filresurser är utformat för hög prestanda och skalning av företagsprogram, ger konsekvent låg latens, hög IOPS och dataflöden resurser.

Den här artikeln visar hur du skapar det här nya kontot typ med [Azure-portalen](https://portal.azure.com/), Azure PowerShell och Azure CLI.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver en Azure-prenumeration för att få åtkomst till Azure Storage. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Skapa en premium-filresurs med Azure portal

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Skapa ett lagringskonto för FileStorage (förhandsversion)

Nu är du redo att skapa ditt lagringskonto.

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resursgrupp.

1. I Azure-portalen väljer du **Lagringskonton** på den vänstra menyn.

    ![Azure portal huvudsakliga sidan Välj lagringskonto](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
1. Välj den prenumeration där du vill skapa lagringskontot.
1. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resursgrupp som du ser i följande bild.

1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagringskonto eller använd standardplatsen.
1. För **prestanda** Välj **Premium**.
1. Välj **typ av konto** och välj **FileStorage (förhandsversion)**.
1. Lämna **replikering** inställt på standardvärdet för **lokalt redundant lagring (LRS)**.

    ![Så här skapar du ett premiumlagringskonto för filer](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
1. Välj **Skapa**.

När du har skapat din resursen för lagringskonton, navigera till den.

### <a name="create-a-premium-file-share"></a>Skapa en premium-filresurs

1. I den vänstra menyn för lagringskontot bläddrar du till den **Filtjänst** avsnittet och välj sedan **filer (förhandsversion)**.
1. Välj **+ filresurs** att skapa en filresurs för premium.
1. Ange ett namn och en önskad kvot för filresursen och välj sedan **skapa**.

> [!NOTE]
> Etablerade resursen storlekar anges av resurs-kvot filresurser debiteras storleken, referera till den [prissättningssidan](https://azure.microsoft.com/pricing/details/storage/files/) för mer information.

   ![Skapa en premium-filresurs](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa de resurser som skapats i den här artikeln kan du helt enkelt ta bort resursgruppen. Ta bort resursgruppen raderas även det kopplade lagringskontot samt andra resurser som är associerade med resursgruppen.

## <a name="create-a-premium-file-share-using-powershell"></a>Skapa en premium-filresurs med hjälp av PowerShell

### <a name="create-an-account-using-powershell"></a>Skapa ett lagringskonto med PowerShell

Installera först den senaste versionen av [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)-modulen.

Sedan kan uppgradera din powershell-modulen, logga in på Azure-prenumerationen, skapa en resursgrupp och sedan skapa ett lagringskonto.

### <a name="upgrade-your-powershell-module"></a>Uppgradera din PowerShell-modul

För att interagera med premium-filer med PowerShell, måste du installera den senaste Az.Storage-modulen.

Starta genom att öppna en PowerShell-session med förhöjd behörighet.

Installera modulen Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på Azure-prenumerationen

Använd kommandot `Login-AzAccount` och följ anvisningarna på skärmen för att autentisera.

```powershell
Login-AzAccount
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

### <a name="create-a-filestorage-preview-storage-account"></a>Skapa ett lagringskonto för filestorage (förhandsversion)

Du kan skapa ett lagringskonto för FileStorage (förhandsversion) från PowerShell med den [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) kommando:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Skapa en premium-filresurs

Nu när du har en FileStorage-konto kan skapa du en premium-filresurs. Använd den [New AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet för att skapa en.

> [!NOTE]
> Etablerade resursen storlekar anges av resurs-kvot filresurser debiteras storleken, referera till den [prissättningssidan](https://azure.microsoft.com/pricing/details/storage/files/) för mer information.

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

## <a name="create-a-premium-file-share-using-azure-cli"></a>Skapa en premium-filresurs med Azure CLI

Om du vill starta Azure Cloud Shell, logga in på den [Azure-portalen](https://portal.azure.com).

Logga in på den lokala installationen av CLI genom att köra inloggningskommandot:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Lägg till CLI-tillägg för Azure premium-filer

För att interagera med premium-filer med hjälp av CLI måste du lägga till ett tillägg till gränssnittet.

Det gör du genom att ange följande kommando i Cloud Shell eller ett lokalt gränssnitt: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du vill skapa en ny resursgrupp med Azure CLI använder du kommandot [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>Skapa ett lagringskonto för FileStorage (förhandsversion)

Du kan skapa ett lagringskonto för FileStorage (förhandsversion) från Azure CLI med den [az storage-konto skapar](/cli/azure/storage/account) kommando.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Hämta lagringskontonyckeln

Lagringskontonycklar styr åtkomsten till resurser i ett lagringskonto i den här artikeln kan vi använda nyckeln för att skapa en filresurs för premium. Nycklarna skapas automatiskt när du skapar ett lagringskonto. Du kan hämta lagringskontonycklarna till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Skapa en premium-filresurs

Nu när du har en FileStorage-konto kan skapa du en premium-filresurs. Använd den [az-lagringsresursen skapa](/cli/azure/storage/share) kommando för att skapa en.

> [!NOTE]
> Etablerade resursen storlekar anges av resurs-kvot filresurser debiteras storleken, referera till den [prissättningssidan](https://azure.microsoft.com/pricing/details/storage/files/) för mer information.

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

I den här artikeln har du skapat ett premiumlagringskonto för filer. Mer information om hur det här kontot erbjuder, fortsätter du till prestanda nivån avsnitt av Planeringsguiden.

> [!div class="nextstepaction"]
> [Filen prestandanivåer för resursen](storage-files-planning.md#file-share-performance-tiers)