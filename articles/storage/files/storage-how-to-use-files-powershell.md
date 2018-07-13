---
title: Hantera Azure-filresurser med Azure PowerShell
description: Läs hur du hanterar Azure-filresurser med Azure PowerShell.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 8fcc654b7f5068037ec0818e19d8dff09bc07537
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635096"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Hantera Azure-filresurser med Azure PowerShell 
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Windows, Linux och macOS. Den här guiden går igenom grunderna med att arbeta med Azure-filresurser med PowerShell. I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa en resursgrupp och ett lagringskonto
> * Skapa en Azure-filresurs 
> * Skapa en katalog
> * Överför en fil 
> * Hämta en fil
> * Skapa och använda en ögonblicksbild av en resurs

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du vill installera och använda PowerShell lokalt krävs Azure PowerShell-modul version 5.1.1 eller senare i den här självstudien. Om du vill ta reda på vilken version av Azure PowerShell-modulen som du kör, kör du `Get-Module -ListAvailable AzureRM`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Om du inte redan har en Azure-resursgrupp, kan du skapa en ny med cmdleten [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Följande exempel skapar en resursgrupp med namnet *myResourceGroup* i regionen USA, östra:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Ett lagringskonto är en delad lagringspool som du kan använda för att distribuera Azure-filresurser eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagringskontots kapacitetsgräns.

Det här exemplet skapar ett lagringskonto med namnet `mystorageacct<random number>` och placerar en referens till det lagringskontot i variabeln **$storageAcct**. Lagringskontonamn måste vara unika så använd `Get-Random` för att lägga till ett tal till namnet som gör det unikt. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Nu kan du skapa din första Azure-filresurs. Du kan skapa en filresurs med hjälp av cmdleten [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare). I det här exemplet skapar vi en resurs som heter `myshare`.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck, men får inte inledas med bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Arbeta med innehållet i Azure-filresursen
Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB på [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Du kan även arbeta med Azure-filresursen i Azure PowerShell-modulen. Detta innebär stora fördelar jämfört med att montera filresursen med SMB, eftersom alla begäranden som görs med PowerShell görs med File REST API:et så att du kan skapa, ändra och ta bort filer och kataloger i filresursen från:

- PowerShell Cloud Shell (som inte kan montera filresurser över SMB).
- Klienter som inte kan montera SMB-resurser, t.ex. lokala klienter som inte har port 445 avblockerad.
- Serverlösa scenarier, t.ex [Azure Functions](../../azure-functions/functions-overview.md). 


### <a name="create-directory"></a>Skapa katalog
Om du vill skapa en ny katalog med namnet *myDirectory* i roten av din Azure-filresurs, använder du cmdleten [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory).


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>Överför en fil
För att visa dig hur du överför en fil med hjälp av cmdleten [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent), måste vi först skapa en fil på din tillfälliga PowerShell Cloud Shell-enhet att ladda upp. 

Det här exemplet placerar aktuellt datum och tid i en ny fil på din tillfälliga enhet och överför sedan filen till filresursen.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Om du kör PowerShell lokalt, kan du ersätta `C:\Users\ContainerAdministrator\CloudDrive\` med en sökväg som finns på din dator.

När du har överfört filen kan du använda cmdleten [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) för att kontrollera att filen överfördes till din Azure-filresurs. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>Hämta en fil
Du kan hämta en kopia av filen du laddade upp till den tillfälliga Cloud Shell-enheten genom att använda cmdleten [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent).

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

När du hämtat filen, kan du använda `Get-ChildItem` för att se att filen har hämtats till din tillfälliga PowerShell Cloud Shell-enhet.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>Kopiera filer
En gemensam uppgift är att kopiera filer från en filresurs till en annan eller till/från en Azure Blob Storage-behållare. Om du vill demonstrera den här funktionen kan du skapa en ny resurs och kopiera filen du överförde till denna nya resurs med cmdleten [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy). 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Du bör nu se den kopierade filen om du visar filerna i den nya resursen.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Även om cmdleten `Start-AzureStorageFileCopy` är praktiskt för ad hoc-filförflyttningar mellan Azure-filresurser och Azure Blob Storage-behållare så rekommenderar vi AzCopy för större flyttar (sett till antalet filer som flyttas eller hur stora filer som flyttas). Läs mer om [AzCopy för Windows](../common/storage-use-azcopy.md) och [AzCopy för Linux](../common/storage-use-azcopy-linux.md). AzCopy måste installeras lokalt – det är inte tillgängligt i Cloud Shell. 

## <a name="create-and-modify-share-snapshots"></a>Skapa och ändra resursögonblicksbilder
Ytterligare en användbar uppgift som du kan göra med en Azure-filresurs är att skapa resursögonblicksbilder. En ögonblicksbild bevarar en tidpunkt för en Azure-filresurs. Ögonblicksbilder av resurser liknar de operativsystemtekniker som du kanske redan är bekant med såsom:
- [Tjänsten Volume Shadow Copy(VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) för Windows-filsystem, till exempel NTFS och ReFS
- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) ögonblicksbilder för Linux-system
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS. 

Du kan skapa en ögonblicksbild av en resurs genom att använda metoden `Snapshot` på PowerShell-objektet för en filresurs, vilken hämtas med cmdleten [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare). 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Bläddra resursögonblicksbilder
Du kan söka i innehållet på resursögonblicksbilden genom att skicka ögonblicksbildreferensen (`$snapshot`) till parametern `-Share` för cmdleten `Get-AzureStorageFile`.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder
Du kan se listan över ögonblicksbilder som du har tagit för din resurs med följande kommando.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
Du kan återställa en fil med hjälp av kommandot `Start-AzureStorageFileCopy` som vi använde tidigare. För den här snabbstarten, tar vi först bort vår `SampleUpload.txt`-fil som överförde tidigare så att vi kan återställa den från ögonblicksbilden.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
Du kan ta bort en resursögonblicksbild med cmdleten [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare), med variabeln som innehåller `$snapshot`-referensen till `-Share`-parametern.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Rensa resurser
När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla relaterade resurser. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Du kan också ta bort resurserna en efter en:

- Ta bort de Azure-filresurser som vi skapade för den här snabbstarten.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Ta bort själva lagringskontot (vilket medför att de Azure-filresurser som vi skapat liksom andra lagringsresurser som du kan ha skapat, t.ex. en Azure Blob Storage-behållare också tas bort).
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>Nästa steg
- [Hantera filresurser med Azure Portal](storage-how-to-use-files-portal.md)
- [Hantera filresurser med Azure-CLI](storage-how-to-use-files-cli.md)
- [Hantera filresurser med Storage Explorer](storage-how-to-use-files-storage-explorer.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)
