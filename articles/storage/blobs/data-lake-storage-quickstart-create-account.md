---
title: Skapa ett Azure Data Lake Storage Gen2-lagringskonto | Microsoft Docs
description: Lär dig snabbt att skapa ett nytt lagrings konto med åtkomst till Data Lake Storage Gen2 med hjälp av Azure Portal, Azure PowerShell eller Azure CLI.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 675d1889fc74474a1d732cb5d4e9f46c638ce200
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467919"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Skapa ett Azure Data Lake Storage Gen2-lagringskonto

Azure Data Lake Storage Gen2 [stöder ett hierarkiskt namn område](data-lake-storage-introduction.md) som tillhandahåller en inbyggd, anpassad katalog baserad behållare som är anpassad för att fungera med HADOOP DISTRIBUTED File System (HDFS). Du kan komma åt Data Lake Storage Gen2-data från HDFS via [ABFS-drivrutinen](data-lake-storage-abfs-driver.md).

Den här artikeln visar hur du skapar ett konto med hjälp av Azure Portal, Azure PowerShell eller via Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

|           | Krav |
|-----------|--------------|
|Portal     | Ingen         |
|PowerShell | Den här artikeln kräver PowerShell-modulen AZ. Storage version **0,7** eller senare. Kör kommandot `Get-Module -ListAvailable Az.Storage` för att hitta din nuvarande version. Om du har kört det här kommandot visas inga resultat eller om en version som är lägre än **0,7** visas måste du uppgradera din PowerShell-modul. Se avsnittet [Uppgradera powershell-modulen](#upgrade-your-powershell-module) i den här guiden.
|CLI        | Du kan logga in på Azure och köra Azure CLI-kommandon på ett av två sätt: <ul><li>Du kan köra CLI-kommandon från Azure-portalen, i Azure Cloud Shell </li><li>Du kan installera CLI och köra CLI-kommandon lokalt</li></ul>|

När du arbetar på kommandoraden kan du välja om du vill köra Azure Cloud Shell eller installera CLI lokalt.

### <a name="use-azure-cloud-shell"></a>Använda Azure Cloud Shell

Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Klicka på knappen **Cloud Shell** på menyn längst upp till höger i Azure Portal:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Knappen startar ett interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln:

[![Skärmbild som visar fönstret Cloud Shell i portalen](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Installera CLI lokalt

Du kan även installera och använda Azure CLI lokalt. Den här artikeln kräver att du kör Azure CLI-version 2.0.38 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Skapa ett lagringskonto med Azure Data Lake Storage Gen2 aktiverat

Ett Azure Storage-konto innehåller alla dina Azure Storage data objekt: blobbar, filer, köer, tabeller och diskar. Lagrings kontot tillhandahåller ett unikt namn område för dina Azure Storage data som är tillgängliga från var som helst i världen via HTTP eller HTTPS. Data i ditt Azure Storage-konto är tåliga och mycket tillgängliga, säkra och enorma skalbara.

> [!NOTE]
> Du måste skapa nya lagringskonton av typen **StorageV2 (generell användning V2)** för att kunna utnyttja Data Lake Storage Gen2-funktionerna.  

Mer information om lagringskonton finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Skapa ett konto med hjälp av Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Varje lagringskonto måste tillhöra en Azure-resursgrupp. En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp. Den här artikeln visar hur du skapar en ny resurs grupp.

Följ de här stegen för att skapa ett GPv2-konto för generell användning i Azure Portal:

> [!NOTE]
> Den hierarkiska namnrymden är för närvarande tillgänglig i alla offentliga regioner.

1. Välj den prenumeration där du vill skapa lagringskontot.
2. I Azure Portal väljer du knappen **skapa en resurs** och väljer sedan **lagrings konto**.
3. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resurs grupp.
   
   En resursgrupp är en logisk container där Azure-resurserna grupperas. När du skapar ett lagringskonto kan du antingen skapa en ny resursgrupp eller använda en befintlig resursgrupp.

4. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
5. Välj en plats.
6. Se till att **StorageV2 (generell användning v2)** visas som markerat i list rutan **Kontotyp** .
7. Du kan också ändra värdena i vart och ett av dessa fält: **prestanda**, **replikering**, **åtkomst nivå**. Mer information om de här alternativen finns i [Introduktion till Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. Välj fliken **Avancerat** .
10. I avsnittet **Data Lake Storage Gen2** anger du **Hierarkisk namnrymd** till **Aktiverad**.
11. Skapa lagringskontot genom att klicka på **Granska + Skapa**.

Nu har ditt lagringskonto skapats via Portal.

### <a name="clean-up-resources"></a>Rensa resurser

Ta bort en resursgrupp med Azure Portal:

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen **Mer** ( **...** ) till höger om listan.
3. Välj **Ta bort resursgrupp** och bekräfta.

## <a name="create-an-account-using-powershell"></a>Skapa ett lagringskonto med PowerShell

Installera först den senaste versionen av [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)-modulen.

Uppgradera sedan PowerShell-modulen, logga in på din Azure-prenumeration, skapa en resurs grupp och skapa ett lagrings konto.

### <a name="upgrade-your-powershell-module"></a>Uppgradera din PowerShell-modul

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

För att interagera med Data Lake Storage Gen2 med hjälp av PowerShell måste du installera Az.Storage Modulversion **0.7** eller senare.

Starta genom att öppna en PowerShell-session med förhöjd behörighet.

Installera modulen Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

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

Logga in på [Azure Portal](https://portal.azure.com)för att starta Azure Cloud Shell.

Om du vill logga in på den lokala installationen av CLI kör du inloggnings kommandot:

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

I den här artikeln har du skapat ett lagrings konto med Data Lake Storage Gen2 funktioner. Om du vill lära dig hur du laddar upp och ned blobar till och från lagringskontot kan du läsa följande avsnitt.

* [AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
