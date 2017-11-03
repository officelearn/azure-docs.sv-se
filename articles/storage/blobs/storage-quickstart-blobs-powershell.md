---
title: "Azure Quickstart - objekt för överföring till/från Azure Blob storage med hjälp av PowerShell | Microsoft Docs"
description: "Lär dig snabbt att överföra objekt till och från Azure Blob storage med hjälp av PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: robinsh
ms.openlocfilehash: 1a9941b21b92c70dd0a46ce2e4c75142e1786650
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-azure-powershell"></a>Överför objekt till/från Azure Blob storage med hjälp av Azure PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här guiden information som använder PowerShell för att överföra filer mellan lokal disk och Azure Blob storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här snabbstarten kräver Azure PowerShell-modul version 3.6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Skapa en behållare

Blobbar överförs alltid till en behållare. På så sätt kan du ordna grupper med blobbar som du ordna dina filer på datorn i mappar.

Ange behållarnamnet på och sedan skapa behållaren med hjälp av [ny AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), ange behörigheter till blob för att tillåta offentliga åtkomsten till filerna. Behållarens namn i det här exemplet är *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Ladda upp blobbar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. VHD-filer som används för att säkerhetskopiera IaaS-VM är sidblobar. Lägg till blobbar som används för inloggning, till exempel när du vill skriva till en fil och sedan hålla att lägga till mer information. De flesta filer som lagras i Blob storage är blockblobbar. 

Hämta en referens för behållaren för att överföra en fil till en blockblobb, och sedan hämta en referens till blockblob i behållaren. När du har en blobbreferens kan du överföra data till den med hjälp av [Set AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Den här åtgärden skapas blobben om den inte redan finns eller skriver över den om den redan finns.

I följande exempel överför bild001.jpg och Image002.png från D:\\_TestImages mapp på den lokala disken till behållaren som du nyss skapade.

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

Hämta en lista över blobbar i behållaren med [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Det här exemplet visar bara namnen för de blobbar som har överförts.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Ladda ned blobbar

Hämta blobbarna till den lokala hårddisken. Ange namn och anrop för varje blob hämtas [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) att hämta blobben.

Det här exemplet hämtas den BLOB-objekt till D:\\_TestImages\Downloads på den lokala disken. 

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

Den [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -verktyget är ett annat alternativ för högpresterande skriptbara dataöverföring för Azure Storage. Du kan använda AzCopy för att överföra data till och från Blob-, fil- och Table storage.

Som ett enkelt exempel här är AzCopy-kommandot för att överföra en fil kallad *minfil.txt* till den *mystoragecontainer* behållare från inom ett PowerShell-fönster.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla resurser som du har skapat. Det enklaste sättet att göra detta är att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet den tar bort lagringskontot och resursgruppen sig själv.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguide du har lärt dig hur du överför filer mellan en lokal disk och Azure Blob storage. Mer information om hur du arbetar med Blob storage fortsätta till Blob storage anvisningar.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referens för Microsoft Azure PowerShell Storage-cmdlets
* [PowerShell-cmdlets för lagring](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.