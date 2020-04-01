---
title: Ladda upp en virtuell hårddisk till Azure eller kopiera en disk mellan regioner - Azure PowerShell
description: Lär dig hur du laddar upp en virtuell hårddisk till en Azure-hanterad disk och kopierar en hanterad disk i olika regioner med Hjälp av Azure PowerShell via direktöverföring.
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 55606aeeb9f6445027f5da49821dbc4970764ade
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421049"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Ladda upp en virtuell hårddisk till Azure eller kopiera en hanterad disk till en annan region - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Krav

- Ladda ner den senaste [versionen av AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installera Azure PowerShell-modul](/powershell/azure/install-Az-ps).
- Om du tänker ladda upp en virtuell hårddisk från lokalt: En fast storlek VHD som [har förberetts för Azure](prepare-for-upload-vhd-image.md), lagras lokalt.
- Eller en hanterad disk i Azure, om du tänker utföra en kopieringsåtgärd.

## <a name="getting-started"></a>Komma igång

Om du föredrar att ladda upp diskar via ett GUI kan du göra det med Hjälp av Azure Storage Explorer. Mer information finns i: [Använda Azure Storage Explorer för att hantera Azure-hanterade diskar](disks-use-storage-explorer-managed-disks.md)

Om du vill överföra din virtuella hårddisk till Azure måste du skapa en tom hanterad disk som är konfigurerad för den här uppladdningsprocessen. Innan du skapar en finns det ytterligare information som du bör känna till om dessa diskar.

Den här typen av hanterad disk har två unika tillstånd:

- ReadToUpload, vilket innebär att disken är redo att ta emot en överföring men ingen [säker åtkomstsignatur](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) har genererats.
- ActiveUpload, vilket innebär att disken är redo att ta emot en uppladdning och SAS har genererats.

> [!NOTE]
> I något av dessa lägen faktureras den hanterade disken med standardpriser för [hårddiskar](https://azure.microsoft.com/pricing/details/managed-disks/), oavsett vilken typ av disk det är. Till exempel kommer en P10 att faktureras som en S10. Detta kommer att `revoke-access` vara sant tills anropas på den hanterade disken, vilket krävs för att koppla disken till en virtuell dator.

## <a name="create-an-empty-managed-disk"></a>Skapa en tom hanterad disk

Innan du kan skapa en tom standard hdd för uppladdning, behöver du filstorleken på den virtuella hårddisken du vill ladda upp, i byte. Exempelkoden får det åt dig, men för att `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`göra det själv kan du använda: . Det här värdet används när parametern **-UploadSizeInBytes** anges.

Nu, på ditt lokala skal, skapa en tom standard HDD för uppladdning genom att ange **upload-inställningen** i **parametern -CreateOption** samt **parametern -UploadSizeInBytes** i cmdleten [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Ring sedan [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) för att skapa disken.

Ersätt `<yourdiskname>` `<yourresourcegroupname>`och `<yourregion>` kör sedan följande kommandon:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Om du vill ladda upp antingen en premium SSD eller en vanlig SSD, ersätta **Standard_LRS** med antingen **Premium_LRS** eller **StandardSSD_LRS**. Ultra diskar stöds ännu inte.

Nu när du har skapat en tom hanterad disk som är konfigurerad för uppladdningsprocessen kan du ladda upp en virtuell hårddisk till den. Om du vill ladda upp en virtuell hårddisk till disken behöver du en skrivbar SAS, så att du kan referera till den som mål för din uppladdning.

Om du vill generera en skrivbar SAS `<yourdiskname>` `<yourresourcegroupname>`för den tomma hanterade disken ersätter du och använder sedan följande kommandon:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Ladda upp en virtuell hårddisk

Nu när du har en SAS för den tomma hanterade disken kan du använda den för att ange den hanterade disken som mål för ditt uppladdningskommando.

Använd AzCopy v10 för att ladda upp den lokala VHD-filen till en hanterad disk genom att ange den SAS URI som du har skapat.

Denna uppladdning har samma dataflöde som motsvarande [standard HDD](disks-types.md#standard-hdd). Om du till exempel har en storlek som motsvarar S4 har du ett dataflöde på upp till 60 MiB/s. Men om du har en storlek som motsvarar S70, kommer du att ha en genomströmning på upp till 500 MiB / s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

När överföringen är klar och du inte längre behöver skriva några fler data till disken återkallar du SAS.After the upload is complete, and you no longer need to write any more data to the disk, revoke the SAS. Om du återkallar SAS ändras tillståndet för den hanterade disken och du kan koppla disken till en virtuell dator.

Ersätt `<yourdiskname>` `<yourresourcegroupname>`och kör sedan följande kommando:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Direktuppladdning förenklar också processen för att kopiera en hanterad disk. Du kan antingen kopiera inom samma region eller kopiera den hanterade disken till en annan region.

Följande skript kommer att göra detta åt dig, processen liknar de steg som beskrivs tidigare, med vissa skillnader, eftersom du arbetar med en befintlig disk.

> [!IMPORTANT]
> Du måste lägga till en förskjutning på 512 när du anger diskstorleken i byte för en hanterad disk från Azure. Detta beror på att Azure utelämnar sidfoten när du returnerar diskstorleken. Kopian misslyckas om du inte gör detta. Följande skript gör redan detta åt dig.

Ersätt `<sourceResourceGroupHere>`, `<sourceDiskNameHere>` `<targetDiskNameHere>`, `<targetResourceGroupHere>` `<yourOSTypeHere>` , `<yourTargetLocationHere>` och (ett exempel på ett platsvärde skulle vara uswest2) med dina värden och kör sedan följande skript för att kopiera en hanterad disk.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Nästa steg

Nu när du har laddat upp en virtuell hårddisk till en hanterad disk kan du ansluta disken till en virtuell dator och börja använda den.

Mer information om hur du ansluter en datadisk till en virtuell dator finns i vår artikel i ämnet: [Bifoga en datadisk till en Windows VM med PowerShell](attach-disk-ps.md). Hur du använder disken som OS-disk finns i [Skapa en Virtuell Windows-dator från en specialiserad disk](create-vm-specialized.md#create-the-new-vm).
