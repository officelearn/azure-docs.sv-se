---
title: Azure-snabbstart – Ladda upp, ladda ned och lista blobar i Azure Storage med Azure PowerShell | Microsoft Docs
description: I den här snabbstarten använder du Azure PowerShell för att skapa ett lagringskonto och en behållare. Sedan använder du PowerShell och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: rogarana
ms.openlocfilehash: 29eb69f95680babfc1004d483a6a1c2bc44edda8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. I den här guiden beskriver vi hur du använder PowerShell för att överföra filer mellan en lokal disk och Azure Blob Storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Skapa en behållare

Blobar laddas alltid upp till en behållare. Du kan ordna grupper av blobar på samma sätt som du ordnar filer i mappar på datorn.

Ange behållarnamnet på och sedan skapa behållaren med hjälp av [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Ange behörigheterna till blob för att tillåta offentlig åtkomst till filerna. Behållarens namn i det här exemplet är *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. VHD-filer som används för att backa upp virtuella datorer i IaaS är sidblobar. Tilläggsblobar används för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. De flesta filer som lagras i Blob Storage är blockblobar. 

Om du vill ladda upp en fil till en blockblob ska du hämta en referens för behållaren och sedan hämta en referens för blockbloben i den behållaren. När du har en blobreferensen kan du ladda upp data till den med hjälp av [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Den här åtgärden skapar bloben om den inte redan finns, eller skriver över den om den finns.

I följande exempel laddas Image001.jpg och Image002.png upp från mappen D:\\_TestImages på den lokala disken till behållaren som du skapade.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Ladda upp så många filer som du vill innan du fortsätter.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Hämta en lista över blobar i behållaren med hjälp av [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Det här exemplet visar bara namnen på de blobar som har laddats upp.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobarna till den lokala disken. Ladda ned bloben genom att för varje blob ange namnet och anropa [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent).

I det här exemplet laddas blobarna ned till D:\\_TestImages\Downloads på den lokala disken. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Dataöverföring med AzCopy

Verktyget [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är ett annat alternativ för högpresterande, skriptbar dataöverföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob, File och Table Storage.

Som ett enkelt exempel ser du här AzCopy-kommandot för att ladda upp en fil med namnet *myfile.txt* till behållaren *mystoragecontainer* inifrån ett PowerShell-fönster.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat. Det enklaste sättet är att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar du bort lagringskontot och själva resursgruppen.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage. Om du vill veta mer om att arbeta med Blob Storage kan du fortsätta till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referens för Microsoft Azure PowerShell Storage-cmdletar
* [Storage PowerShell cmdletar](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.