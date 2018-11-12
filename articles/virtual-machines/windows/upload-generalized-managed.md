---
title: Skapa en hanterad Azure virtuell dator från en generaliserad lokala VHD | Microsoft Docs
description: Ladda upp en generaliserad virtuell Hårddisk till Azure och använda den för att skapa nya virtuella datorer, i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 93d5cbdb44d3014b547141d59ce96cf607276846
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234620"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Ladda upp en generaliserad virtuell Hårddisk och använda den för att skapa nya virtuella datorer i Azure

Den här artikeln visar hur du använder PowerShell för att ladda upp en VHD från en generaliserad virtuell dator till Azure, skapa en avbildning från den virtuella Hårddisken och skapa en ny virtuell dator från avbildningen. Du kan ladda upp en virtuell Hårddisk som exporteras från en lokal virtualisering verktyget eller ett annat moln. Med hjälp av [Managed Disks](managed-disks-overview.md) för den nya virtuella datorn förenklar hanteringen av virtuella datorer och ger bättre tillgänglighet när den virtuella datorn placeras i en tillgänglighetsuppsättning. 

Ett exempelskript finns i [exempel på skript för att ladda upp en virtuell Hårddisk till Azure och skapa en ny virtuell dator](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Innan du börjar

- Innan du laddar upp alla VHD till Azure, bör du följa [förbereda en Windows-VHD eller VHDX för att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Granska [planera för migrering till Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) innan du påbörjar migreringen till [Managed Disks](managed-disks-overview.md).
- Den här artikeln kräver AzureRM-modulen version 5.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM.Compute` att hitta din version. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalisera den Virtuella källdatorn med hjälp av Sysprep

Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i den [Sysprep översikt](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Se till att serverroller som körs på datorn som stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Om du planerar att köra Sysprep innan du laddar upp en virtuell Hårddisk till Azure för första gången, kontrollera att du har [förberett din virtuella dator](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Logga in på den virtuella datorn i Windows.
2. Öppna Kommandotolken som administratör. Ändra katalogen till % windir%\system32\sysprep och kör sedan `sysprep.exe`.
3. I den **systemförberedelseverktyget** dialogrutan **ange System Out-of-Box Experience (OOBE)**, och se till att den **Generalize** kryssrutan aktiveras.
4. För **Avslutningsalternativ**väljer **avstängning**.
5. Välj **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep har avslutats, stängs den virtuella datorn. Starta inte om den virtuella datorn.


## <a name="get-a-storage-account"></a>Hämta ett storage-konto

Du behöver ett lagringskonto i Azure för att lagra den uppladdade avbildningen. Du kan antingen använda ett befintligt lagringskonto eller skapa en ny. 

Om du ska använda den virtuella Hårddisken skapar en hanterad disk för en virtuell dator, måste platsen för lagringskontot vara på samma plats där du ska skapa den virtuella datorn.

Om du vill visa tillgängliga storage-konton, anger du:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Ladda upp den virtuella Hårddisken till ditt storage-konto

Använd den [Add-AzureRmVhd](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvhd) cmdlet för att ladda upp den virtuella Hårddisken till en behållare i ditt storage-konto. Det här exemplet överför filen *myVHD.vhd* från *C:\Users\Public\Documents\Virtual hårddiskar\\*  till ett lagringskonto med namnet *mystorageaccount* i den *myResourceGroup* resursgrupp. Filen ska placeras i behållaren med namnet *mycontainer* och det nya filnamnet blir *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om detta lyckas kan du få ett svar som ser ut ungefär så här:

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

Det här kommandot kan ta en stund att slutföra beroende på din nätverksanslutning och storleken på VHD-filen.

### <a name="other-options-for-uploading-a-vhd"></a>Andra alternativ för att ladda upp en virtuell Hårddisk
 
Du kan också ladda upp en virtuell Hårddisk till ditt lagringskonto med hjälp av något av följande:

- [AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage kopiering av Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure Storage Explorer ladda upp BLOB](https://azurestorageexplorer.codeplex.com/)
- [Storage Import/Export Service REST API-referens](https://msdn.microsoft.com/library/dn529096.aspx)
-   Vi rekommenderar att du använder Import/Export-tjänsten om Uppskattat laddar upp tiden är längre än sju dagar. Du kan använda [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) att uppskatta storlek och överför dataenheten tiden. 
    Import/Export kan användas för att kopiera till ett standardlagringskonto. Du behöver kopiera från standardlagring till premium storage-konto med hjälp av ett verktyg som AzCopy.

> [!IMPORTANT]
> Om du använder AzCopy för att överföra en virtuell Hårddisk till Azure, se till att du har angett [ **/BlobType:page** ](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) innan du kör skriptet ladda upp. Om målet är en blob och det här alternativet inte anges, skapar AzCopy en blockblob som standard.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Skapa en hanterad avbildning från den överförda virtuella Hårddisken 

Skapa en hanterad avbildning från din generaliserade OS-VHD. Ersätt följande värden med din egen information.


Innan du kan definiera vissa parametrar:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Skapa avbildningen med hjälp av din generaliserade OS-VHD.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Nu när du har en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. Det här exemplet skapar en virtuell dator med namnet *myVM* från *myImage*i *myResourceGroup*.


```powershell
New-AzureRmVm `
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

Logga in på din nya virtuella dator. Mer information finns i [hur du ansluter och logga in på Azure-datorer som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

