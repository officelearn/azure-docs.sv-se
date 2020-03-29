---
title: Skapa en virtuell dator från en uppladdad generaliserad virtuell hårddisk
description: Ladda upp en generaliserad virtuell hårddisk till Azure och använd den för att skapa nya virtuella datorer i Resurshanterarens distributionsmodell.
services: virtual-machines-windows
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 3c482caf2407c89ffdb6c55c9184c31e2e3197c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464951"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Ladda upp en generaliserad virtuell hårddisk och använda den för att skapa nya virtuella datorer i Azure

I den här artikeln får du hjälp med PowerShell för att överföra en virtuell hårddisk för en generaliserad virtuell dator till Azure, skapa en avbildning från den virtuella hårddisken och skapa en ny virtuell dator från den avbildningen. Du kan ladda upp en virtuell hårddisk som exporteras från ett lokalt virtualiseringsverktyg eller från ett annat moln. Om du använder [hanterade diskar](managed-disks-overview.md) för den nya virtuella datorn förenklas hanteringen av den virtuella datorn och ger bättre tillgänglighet när den virtuella datorn placeras i en tillgänglighetsuppsättning. 

Ett exempelskript finns i [Exempelskript för att ladda upp en virtuell hårddisk till Azure och skapa en ny virtuell dator](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Innan du börjar

- Innan du överför en virtuell hårddisk till Azure bör du följa [Förbered en Windows VHD eller VHDX för att ladda upp till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Granska [plan för migreringen till Hanterade diskar](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) innan du startar migreringen till [Hanterade diskar](managed-disks-overview.md).

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalisera källdatorn med hjälp av Sysprep

Om du inte redan har gjort det måste du sysprep den virtuella datorn innan du överför den virtuella hårddisken till Azure. Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i [Översikt över Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Kontrollera att serverrollerna som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Om du planerar att köra Sysprep innan du laddar upp din virtuella hårddisk till Azure för första gången kontrollerar du att du har [förberett din virtuella dator](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Logga in på den virtuella Windows-datorn.
2. Öppna Kommandotolken som administratör. Ändra katalogen till %windir%\system32\sysprep `sysprep.exe`och kör sedan .
3. I dialogrutan **Systemförberedelseverktyg** väljer du **Ange oobe (System out-of-Box Experience)** och kontrollerar att kryssrutan **Generalize** är aktiverad.
4. För **avstängningsalternativ**väljer du **Avstängning**.
5. Välj **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep är klar stängs den av den virtuella datorn. Starta inte om den virtuella datorn.


## <a name="upload-the-vhd"></a>Ladda upp den virtuella hårddisken 

Du kan nu ladda upp en virtuell hårddisk direkt till en hanterad disk. Instruktioner finns i [Ladda upp en virtuell hårddisk till Azure med Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).



När den virtuella hårddisken har överförts till den hanterade disken måste du använda [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk) för att hämta den hanterade disken.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Skapa avbildningen
Skapa en hanterad avbildning från den allmänt hanterade disken. Ersätt följande värden med din egen information.

Ange först några variabler:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Skapa avbildningen med den hanterade disken.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Skapa avbildningen.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Nu när du har en avbildning kan du skapa en eller flera nya virtuella datorer från avbildningen. I det här exemplet skapas en virtuell dator med namnet *myVM* från *myImage*i *myResourceGroup*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Nästa steg

Logga in på din nya virtuella dator. Mer information finns i [Så här ansluter och loggar du in på en virtuell Azure-dator som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

