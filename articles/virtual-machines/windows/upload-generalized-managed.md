---
title: Skapa en hanterad virtuell Azure-dator från en generaliserad lokal VHD | Microsoft Docs
description: Överför en generaliserad virtuell hård disk till Azure och Använd den för att skapa nya virtuella datorer i distributions modellen för Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: be3ccfd0c562763d0968398ddb042dc5f07dbdcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101567"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Överför en generaliserad virtuell hård disk och Använd den för att skapa nya virtuella datorer i Azure

Den här artikeln vägleder dig igenom hur du använder PowerShell för att ladda upp en virtuell hård disk för en generaliserad virtuell dator till Azure, skapa en avbildning från den virtuella hård disken och skapa en ny virtuell dator från avbildningen. Du kan ladda upp en virtuell hård disk som exporter ATS från ett lokalt Virtualization-verktyg eller från ett annat moln. Att använda [Managed disks](managed-disks-overview.md) för den nya virtuella datorn FÖREN klar VM-hanteringen och ger bättre tillgänglighet när den virtuella datorn placeras i en tillgänglighets uppsättning. 

Ett exempel skript finns i [exempel skript för att ladda upp en virtuell hård disk till Azure och skapa en ny virtuell dator](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Innan du börjar

- Innan du laddar upp en virtuell hård disk till Azure bör du följa [förbereda en virtuell Windows-VHD eller VHDX för uppladdning till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Granska [planen för migreringen till Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) innan du påbörjar migreringen till [Managed disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalisera den virtuella käll datorn med hjälp av Sysprep

Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i [Översikt över Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Kontrol lera att de Server roller som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för Server roller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Om du planerar att köra Sysprep innan du laddar upp din virtuella hård disk till Azure för första gången, måste du se till att du har för [berett den virtuella](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)datorn. 
> 
> 

1. Logga in på den virtuella Windows-datorn.
2. Öppna Kommandotolken som administratör. Ändra katalogen till%WINDIR%\system32\sysprep och kör `sysprep.exe`sedan.
3. I dialog rutan **system förberedelse verktyg** väljer du **Använd OOBE (system out-of-Box Experience)** och kontrollerar att kryss rutan generalize är aktive rad.
4. För **avslutnings alternativ**väljer du **Stäng**av.
5. Välj **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep har slutförts stängs den virtuella datorn av. Starta inte om den virtuella datorn.


## <a name="get-a-storage-account"></a>Hämta ett lagrings konto

Du behöver ett lagrings konto i Azure för att lagra den uppladdade VM-avbildningen. Du kan antingen använda ett befintligt lagrings konto eller skapa ett nytt. 

Om du ska använda den virtuella hård disken för att skapa en hanterad disk för en virtuell dator måste lagrings kontots plats vara samma plats som du skapar den virtuella datorn på.

Om du vill visa tillgängliga lagrings konton anger du:

```azurepowershell
Get-AzStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Ladda upp den virtuella hård disken till ditt lagrings konto

Använd cmdleten [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) för att ladda upp den virtuella hård disken till en behållare i ditt lagrings konto. I det här exemplet överförs filen *myVHD. VHD* från *C:\Users\Public\Documents\Virtual hård diskar\\*  till ett lagrings konto med namnet *mystorageaccount* i resurs gruppen *myResourceGroup* . Filen kommer att placeras i behållaren som heter behållaren och det nya fil namnet kommer att vara *myUploadedVHD. VHD*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om det lyckas får du ett svar som ser ut ungefär så här:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Beroende på din nätverks anslutning och storleken på VHD-filen kan det här kommandot Ta en stund att slutföra.

### <a name="other-options-for-uploading-a-vhd"></a>Andra alternativ för att ladda upp en virtuell hård disk
 
Du kan också ladda upp en virtuell hård disk till ditt lagrings konto med hjälp av något av följande:

- [AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage Copy BLOB API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure Storage Explorer uppladdning av blobbar](https://azurestorageexplorer.codeplex.com/)
- [REST API referens för lagrings import/export-tjänsten](https://msdn.microsoft.com/library/dn529096.aspx)
-   Vi rekommenderar att du använder import/export-tjänsten om uppskattad överförings tid är längre än sju dagar. Du kan använda [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) för att beräkna tiden från data storlek och överförings enhet. 
    Import/export kan användas för att kopiera till ett standard lagrings konto. Du måste kopiera från standard lagring till Premium Storage-kontot med hjälp av ett verktyg som AzCopy.

> [!IMPORTANT]
> Om du använder AzCopy för att ladda upp din virtuella hård disk till Azure måste du kontrol lera att du har ange [ **/BlobType: sida**](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs#upload-a-file) innan du kör uppladdnings skriptet. Om målet är en blob och det här alternativet inte anges, skapar AzCopy en Block-Blob som standard.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Skapa en hanterad avbildning från den överförda virtuella hård disken 

Skapa en hanterad avbildning från den generaliserade OS-VHD: n. Ersätt följande värden med din egen information.


Ange först några parametrar:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Skapa avbildningen med hjälp av den generaliserade OS-VHD: n.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Nu när du har en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. I det här exemplet skapas en virtuell dator med namnet *myVM* från *avbildningen*i *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Nästa steg

Logga in på den nya virtuella datorn. Mer information finns i [så här ansluter du och loggar in på en virtuell Azure-dator som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

