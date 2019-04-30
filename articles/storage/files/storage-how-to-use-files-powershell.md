---
title: Snabbstart för hantering av Azure-filresurser med Azure PowerShell
description: Använd den här snabbstarten för att lära dig hur du hanterar Azure-filresurser med Azure PowerShell.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 439a1c60942b1540328bf9972d74d7dd4d573a65
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764149"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Snabbstart: Skapa och hantera en Azure-filresurs med Azure PowerShell 
Den här guiden går igenom grunderna med att arbeta med [Azure-filresurser](storage-files-introduction.md) med PowerShell. Azure-filresurser är precis som andra filresurser men lagras i molnet och täcks av Azure-plattformen. Azure-filresurser stöder SMB-protokollet, som är branschstandard och möjliggör fildelning på olika datorer, program och instanser. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du vill installera och använda PowerShell lokalt krävs Azure PowerShell-modul Az version 0.7 eller senare i den här självstudien. Om du vill ta reda på vilken version av Azure PowerShell-modulen som du kör, kör du `Get-Module -ListAvailable Az`. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du även köra `Login-AzAccount` för att logga in på ditt Azure account.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Om du inte redan har en Azure-resursgrupp, kan du skapa en ny med cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Följande exempel skapar en resursgrupp med namnet *myResourceGroup* i regionen USA, östra:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Ett lagringskonto är en delad lagringspool som du kan använda för att distribuera Azure-filresurser eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagringskontots kapacitetsgräns.

I det här exemplet skapas ett lagringskonto med cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Lagringskontot har namnet *mystorageaccount\<slumptal >* och en referens till det lagringskontot lagras i variabeln **$storageAcct**. Lagringskontonamn måste vara unika så använd `Get-Random` för att lägga till ett tal till namnet som gör det unikt. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Nu kan du skapa din första Azure-filresurs. Du kan skapa en filresurs med hjälp av cmdleten [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare). I det här exemplet skapar vi en resurs som heter `myshare`.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck, men får inte inledas med bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Använda Azure-filresursen
Azure Files har två metoder för att arbeta med filer och mappar i din Azure-filresurs: branschstandardprotokollen [SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) och [fil-REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Om du vill montera en filresurs med SMB läser du följande dokument baserat på ditt operativsystem:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Använda en Azure-filresurs med fil-REST-protokollet 
Det är möjligt att arbeta direkt med fil-REST-protokollet (dvs. att själv skapa REST HTTP-anrop manuellt) men det vanligaste sättet att använda fil-REST-protokollet är att använda Azure PowerShell-modulen, [Azure CLI](storage-how-to-use-files-cli.md) eller ett Azure Storage-SDK. Alla ger en bra omslutning runt fil-REST-protokollet i valfritt skript-/programmeringsspråk.  

I de flesta fall använder du Azure-filresursen via SMB-protokollet, eftersom du då kan använda de befintliga program och verktyg som du förväntar dig kunna använda, men det finns flera anledningar till varför det är fördelaktigt att använda fil-REST-API:et istället för SMB, till exempel:

- Du bläddrar efter filresursen från PowerShell Cloud Shell (som inte kan montera filresurser via SMB).
- Du måste köra ett skript eller program från en klient som inte kan montera en SMB-resurs, till exempel lokala klienter, som inte har port 445 avblockerad.
- Du utnyttjar serverlösa resurser såsom [Azure Functions](../../azure-functions/functions-overview.md). 

I följande exempel visas hur du använder Azure PowerShell-modulen till att ändra din Azure-filresurs med fil-REST-protokollet. 

#### <a name="create-directory"></a>Skapa katalog
Om du vill skapa en ny katalog med namnet *myDirectory* i roten av din Azure-filresurs, använder du cmdleten [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Överför en fil
För att visa dig hur du överför en fil med hjälp av cmdleten [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), måste vi först skapa en fil på din tillfälliga PowerShell Cloud Shell-enhet att ladda upp. 

Det här exemplet placerar aktuellt datum och tid i en ny fil på din tillfälliga enhet och överför sedan filen till filresursen.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Om du kör PowerShell lokalt, kan du ersätta `C:\Users\ContainerAdministrator\CloudDrive\` med en sökväg som finns på din dator.

När du har överfört filen kan du använda cmdleten [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) för att kontrollera att filen överfördes till din Azure-filresurs. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Hämta en fil
Du kan hämta en kopia av filen du laddade upp till den tillfälliga Cloud Shell-enheten genom att använda cmdleten [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent).

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

När du hämtat filen, kan du använda `Get-ChildItem` för att se att filen har hämtats till din tillfälliga PowerShell Cloud Shell-enhet.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Kopiera filer
En gemensam uppgift är att kopiera filer från en filresurs till en annan eller till/från en Azure Blob Storage-container. Om du vill demonstrera den här funktionen kan du skapa en ny resurs och kopiera filen du överförde till denna nya resurs med cmdleten [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy). 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Du bör nu se den kopierade filen om du visar filerna i den nya resursen.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Medan den `Start-AzStorageFileCopy` cmdlet är praktiskt för ad hoc-filförflyttningar mellan Azure-filresurser och Azure Blob storage-behållare, rekommenderar vi AzCopy för större flyttar (sett till antal eller storleken på filer som flyttas). Läs mer om [AzCopy för Windows](../common/storage-use-azcopy.md) och [AzCopy för Linux](../common/storage-use-azcopy-linux.md). AzCopy måste installeras lokalt – det är inte tillgängligt i Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Skapa och hantera resursögonblicksbilder
Ytterligare en användbar uppgift som du kan göra med en Azure-filresurs är att skapa resursögonblicksbilder. En ögonblicksbild bevarar en tidpunkt för en Azure-filresurs. Ögonblicksbilder av resurser liknar de operativsystemtekniker som du kanske redan är bekant med såsom:
- [Tjänsten Volume Shadow Copy(VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) för Windows-filsystem, till exempel NTFS och ReFS
- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) ögonblicksbilder för Linux-system
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS. 
 Du kan skapa en ögonblicksbild av en resurs genom att använda metoden `Snapshot` på PowerShell-objektet för en filresurs, vilken hämtas med cmdleten [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Bläddra resursögonblicksbilder
Du kan söka i innehållet på resursögonblicksbilden genom att skicka ögonblicksbildreferensen (`$snapshot`) till parametern `-Share` för cmdleten `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder
Du kan se listan över ögonblicksbilder som du har tagit för din resurs med följande kommando.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
Du kan återställa en fil med hjälp av kommandot `Start-AzStorageFileCopy` som vi använde tidigare. För den här snabbstarten, tar vi först bort vår `SampleUpload.txt`-fil som överförde tidigare så att vi kan återställa den från ögonblicksbilden.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
Du kan ta bort en resursögonblicksbild med cmdleten [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare), med variabeln som innehåller `$snapshot`-referensen till `-Share`-parametern.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Rensa resurser
När resursgruppen inte längre behövs kan du använda kommandot [Remove-AzRmResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och alla relaterade resurser. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Du kan också ta bort resurserna en efter en:

- Ta bort de Azure-filresurser som vi skapade för den här snabbstarten.

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Ta bort själva lagringskontot (vilket medför att de Azure-filresurser som vi skapat liksom andra lagringsresurser som du kan ha skapat, t.ex. en Azure Blob Storage-container, också tas bort).

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är Azure Files?](storage-files-introduction.md)
