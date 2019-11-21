---
title: Skapa ett Azure Data Lake Storage Gen2-lagringskonto | Microsoft Docs
description: Quickly learn to create a new storage account with access to Data Lake Storage Gen2 using the Azure portal, Azure PowerShell, or the Azure CLI.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 57350bd00a33c36b5aef3a0ccd3034b4db3d2c55
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227954"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Skapa ett Azure Data Lake Storage Gen2-lagringskonto

Azure Data Lake Storage Gen2 [supports a hierarchical namespace](data-lake-storage-introduction.md) which provides a native directory-based container tailored to work with the Hadoop Distributed File System (HDFS). Du kan komma åt Data Lake Storage Gen2-data från HDFS via [ABFS-drivrutinen](data-lake-storage-abfs-driver.md).

This article demonstrates how to create an account using the Azure portal, Azure PowerShell, or via the Azure CLI.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

|           | Krav |
|-----------|--------------|
|Portalen     | Inget         |
|PowerShell | This article requires the PowerShell module Az.Storage version **0.7** or later. Kör kommandot `Get-Module -ListAvailable Az.Storage` för att hitta din nuvarande version. If after you run this command, no results appear, or if a version lower than **0.7** appears, then you'll have to upgrade your powershell module. Se avsnittet [Uppgradera powershell-modulen](#upgrade-your-powershell-module) i den här guiden.
|CLI        | You can sign in to Azure and run Azure CLI commands in one of two ways: <ul><li>Du kan köra CLI-kommandon från Azure-portalen, i Azure Cloud Shell </li><li>Du kan installera CLI och köra CLI-kommandon lokalt</li></ul>|

När du arbetar på kommandoraden kan du välja om du vill köra Azure Cloud Shell eller installera CLI lokalt.

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Klicka på knappen **Cloud Shell** på menyn längst upp till höger i Azure-portalen:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

The button launches an interactive shell that you can use to run the steps in this article:

[![Skärmbild som visar fönstret Cloud Shell i portalen](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. This article requires that you are running the Azure CLI version 2.0.38 or later. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Skapa ett lagringskonto med Azure Data Lake Storage Gen2 aktiverat

An Azure storage account contains all of your Azure Storage data objects: blobs, files, queues, tables, and disks. The storage account provides a unique namespace for your Azure Storage data that is accessible from anywhere in the world over HTTP or HTTPS. Data in your Azure storage account is durable and highly available, secure, and massively scalable.

> [!NOTE]
> Du måste skapa nya lagringskonton av typen **StorageV2 (generell användning V2)** för att kunna utnyttja Data Lake Storage Gen2-funktionerna.  

Mer information om lagringskonton finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Skapa ett konto med hjälp av Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. This article shows how to create a new resource group.

Följ de här stegen för att skapa ett GPv2-konto för generell användning i Azure-portalen:

> [!NOTE]
> Den hierarkiska namnrymden är för närvarande tillgänglig i alla offentliga regioner.

1. Välj den prenumeration där du vill skapa lagringskontot.
2. In the Azure portal, choose the **Create a resource** button, then choose **Storage account**.
3. Under fältet **Resursgrupp** väljer du **Skapa ny**. Enter a name for your new resource group.
   
   En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp.

4. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
5. Välj en plats.
6. Make sure that **StorageV2 (general purpose v2)** appears as selected in the **Account kind** drop-down list.
7. Optionally change the values in each of these fields: **Performance**, **Replication**, **Access tier**. To learn more about these options, see [Introduction to Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. Choose the **Advanced** tab.
10. I avsnittet **Data Lake Storage Gen2** anger du **Hierarkisk namnrymd** till **Aktiverad**.
11. Skapa lagringskontot genom att klicka på **Granska + Skapa**.

Nu har ditt lagringskonto skapats via Portal.

### <a name="clean-up-resources"></a>Rensa resurser

Ta bort en resursgrupp med Azure-portalen:

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen **Mer** ( **...** ) till höger om listan.
3. Välj **Ta bort resursgrupp** och bekräfta.

## <a name="create-an-account-using-powershell"></a>Skapa ett lagringskonto med PowerShell

Installera först den senaste versionen av [PowerShellGet](/powershell/scripting/gallery/installing-psget)-modulen.

Then, upgrade your powershell module, sign in to your Azure subscription, create a resource group, and then create a storage account.

### <a name="upgrade-your-powershell-module"></a>Uppgradera din PowerShell-modul

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

För att interagera med Data Lake Storage Gen2 med hjälp av PowerShell måste du installera Az.Storage Modulversion **0.7** eller senare.

Starta genom att öppna en PowerShell-session med förhöjd behörighet.

Installera modulen Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Sign in to your Azure Subscription

Använd kommandot `Login-AzAccount` och följ anvisningarna på skärmen för att autentisera.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du vill skapa en resursgrupp med PowerShell använder du kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup): 

> [!NOTE]
> Den hierarkiska namnrymden är för närvarande tillgänglig i alla offentliga regioner.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Skapa ett v2-lagringskonto för generell användning

Om du vill skapa ett GPv2-lagringskonto för generell användning från PowerShell med lokalt redundant lagring (LRS) använder du kommandot [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount):

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot så använder du kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Skapa ett konto med Azure CLI

To start Azure Cloud Shell, sign in to the [Azure portal](https://portal.azure.com).

If you want to sign in to your local installation of the CLI, run the login command:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Lägg till CLI-tillägg för Azure Data Lake Gen 2

För att kunna interagera med Data Lake Storage Gen2 via CLI måste du lägga till tillägget för gränssnittet.

Det gör du genom att ange följande kommando i Cloud Shell eller ett lokalt gränssnitt: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Om du vill skapa en ny resursgrupp med Azure CLI använder du kommandot [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > Den hierarkiska namnrymden är för närvarande tillgänglig i alla offentliga regioner.

### <a name="create-a-general-purpose-v2-storage-account"></a>Skapa ett v2-lagringskonto för generell användning

Om du vill skapa ett allmänt GPv2-lagringskonto för generell användning från Azure CLI med lokalt redundant lagring använder du kommandot [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort resursgruppen och dess kopplade resurser, inklusive det nya lagringskontot, använder du kommandot [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

In this article, you've created a storage account with Data Lake Storage Gen2 capabilities. Om du vill lära dig hur du laddar upp och ned blobar till och från lagringskontot kan du läsa följande avsnitt.

* [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
