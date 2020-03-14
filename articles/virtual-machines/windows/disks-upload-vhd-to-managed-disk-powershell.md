---
title: Ladda upp en virtuell hård disk till Azure med Azure PowerShell
description: Lär dig hur du laddar upp en virtuell hård disk till en Azure-hanterad disk och kopierar en hanterad disk över flera regioner med hjälp av Azure PowerShell via direkt uppladdning.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369564"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Ladda upp en virtuell hård disk till Azure med Azure PowerShell

Den här artikeln förklarar hur du laddar upp en virtuell hård disk från din lokala dator till en Azure-hanterad disk. Tidigare var du tvungen att följa en mer engagerad process som inkluderade mellanlagring av dina data i ett lagrings konto och hantering av lagrings kontot. Nu behöver du inte längre hantera ett lagrings konto eller mellanlagra data i det för att ladda upp en virtuell hård disk. I stället skapar du en tom hanterad disk och laddar upp en virtuell hård disk direkt till den. Detta fören klar överföringen av lokala virtuella datorer till Azure och gör att du kan ladda upp en virtuell hård disk på upp till 32 TiB direkt i en stor hanterad disk.

Om du tillhandahåller en säkerhets kopierings lösning för virtuella IaaS-datorer i Azure rekommenderar vi att du använder direkt uppladdning för att återställa kundens säkerhets kopior till hanterade diskar. Om du laddar upp en virtuell hård disk från en dator som är extern till Azure, är hastigheten med beroende av din lokala bandbredd. Om du använder en virtuell Azure-dator är bandbredden samma som standard hård diskar.

För närvarande stöds direkt uppladdning för standard hård diskar, standard SSD och Premium SSD-hanterade diskar. Det stöds ännu inte för Ultra SSD.

## <a name="prerequisites"></a>Förutsättningar

- Ladda ned den senaste [versionen av AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installera Azure PowerShell-modul](/powershell/azure/install-Az-ps).
- Om du tänker Ladda upp en virtuell hård disk från en lokal plats: en fast storleks-VHD som [har förberetts för Azure](prepare-for-upload-vhd-image.md), lagrad lokalt.
- Eller en hanterad disk i Azure om du vill utföra en kopierings åtgärd.

## <a name="create-an-empty-managed-disk"></a>Skapa en tom hanterad disk

Om du vill överföra din virtuella hård disk till Azure måste du skapa en tom hanterad disk som har kon figurer ATS för den här överförings processen. Innan du skapar en sådan finns det ytterligare information som du bör känna till om de här diskarna.

Den här typen av hanterade diskar har två unika tillstånd:

- ReadToUpload, vilket innebär att disken är redo att ta emot en uppladdning, men [att ingen säker åtkomst-signatur](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) har genererats.
- ActiveUpload, vilket innebär att disken är redo att ta emot en uppladdning och att SAS har genererats.

I något av dessa tillstånd debiteras den hanterade disken med [standard priset för hård](https://azure.microsoft.com/pricing/details/managed-disks/)diskar, oavsett vilken typ av disk som används. Till exempel kommer en P10 att faktureras som en S10. Detta är sant tills `revoke-access` anropas på den hanterade disken, vilket krävs för att ansluta disken till en virtuell dator.

Innan du skapar en tom standard hård disk för uppladdning behöver du fil storleken i byte på den virtuella hård disk som du vill ladda upp. Exempel koden kommer att ge dig, men för att göra det själv kan du använda: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Det här värdet används när du anger parametern **-UploadSizeInBytes** .

I det lokala gränssnittet skapar du en tom standard hård disk för uppladdning genom att ange **uppladdnings** inställningen i parametern **-CreateOption** och parametern **-UploadSizeInBytes** i cmdleten [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) . Anropa sedan [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) för att skapa disken:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Om du vill ladda upp antingen en Premium SSD eller en standard SSD ersätter du **Standard_LRS** med antingen **Premium_LRS** eller **StandardSSD_LRS**. Ultra SSD stöds ännu inte.

Nu har du skapat en tom hanterad disk som är konfigurerad för överförings processen. Om du vill ladda upp en virtuell hård disk till disken behöver du en skrivbar SAS, så att du kan referera till den som mål för överföringen.

Använd följande kommando för att generera en skrivbar SAS för din tomma hanterade disk:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Ladda upp VHD

Nu när du har en SAS för din tomma hanterade disk kan du använda den för att ange den hanterade disken som mål för ditt upload-kommando.

Använd AzCopy v10 för att ladda upp din lokala VHD-fil till en hanterad disk genom att ange den SAS-URI som du skapade.

Den här uppladdningen har samma data flöde som motsvarande [standard-hårddisk](disks-types.md#standard-hdd). Om du till exempel har en storlek som motsvarar S4, kommer du att ha ett data flöde på upp till 60 MiB/s. Men om du har en storlek som motsvarar S70 har du ett data flöde på upp till 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

När uppladdningen är klar och du inte längre behöver skriva mer data till disken ska du återkalla SAS. Att återkalla SAS ändrar statusen för den hanterade disken och låter dig ansluta disken till en virtuell dator.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Kopiera en hanterad disk

Direkt överföring fören klar också processen att kopiera en hanterad disk. Du kan antingen kopiera inom samma region eller mellan regioner (till en annan region).

Följ skriptet gör detta åt dig, processen liknar de steg som beskrivs ovan, med vissa skillnader eftersom du arbetar med en befintlig disk.

> [!IMPORTANT]
> Du måste lägga till en förskjutning på 512 när du tillhandahåller disk storleken i byte för en hanterad disk från Azure. Detta beror på att Azure utelämnar sidfoten när den returnerar disk storleken. Kopieringen Miss kommer om du inte gör det. Följande skript använder redan det här.

Ersätt `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` och `<yourTargetLocationHere>` (ett exempel på ett plats värde är uswest2) med dina värden och kör sedan följande skript för att kopiera en hanterad disk.

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

Nu när du har laddat upp en virtuell hård disk till en hanterad disk kan du ansluta disken till en virtuell dator och börja använda den.

Information om hur du ansluter en datadisk till en virtuell dator finns i vår artikel om ämnet: [koppla en datadisk till en virtuell Windows-dator med PowerShell](attach-disk-ps.md). Om du vill använda disken som operativ system disk, se [skapa en virtuell Windows-dator från en specialiserad disk](create-vm-specialized.md#create-the-new-vm).
