---
title: Skapa en hanterad avbildning i Azure
description: Skapa en hanterad avbildning av en generaliserad virtuell dator eller virtuell hårddisk i Azure. Avbildningar kan användas för att skapa flera virtuella datorer som använder hanterade diskar.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 01619027ddc79530dc9541584efa9a3e518f5136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74842066"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Skapa en hanterad avbildning av en generaliserad virtuell dator i Azure

Du kan skapa en hanterad avbildningsresurs från en generaliserad virtuell dator (VM) som lagras antingen som en hanterad disk eller en ohanterad disk i ett lagringskonto. Avbildningen kan sedan användas till att skapa flera virtuella datorer. Information om hur hanterade avbildningar faktureras finns i [Priser för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera den virtuella Windows-datorn med hjälp av Sysprep

Sysprep tar bort all personlig konto- och säkerhetsinformation och förbereder sedan datorn som ska användas som en avbildning. Information om Sysprep finns i [Översikt över Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Kontrollera att serverrollerna som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) och [scenarier som inte stöds](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> När du har kört Sysprep på en virtuell dator anses den virtuella datorn vara *generaliserad* och kan inte startas om. Det går inte att ångra processen för att generalisera en virtuell dator. Om du behöver behålla den ursprungliga virtuella datorn fungerar bör du skapa en [kopia av den virtuella datorn](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) och generalisera dess kopia. 
>
> Om du planerar att köra Sysprep innan du laddar upp den virtuella hårddisken (VHD) till Azure för första gången kontrollerar du att du har [förberett den virtuella datorn](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Så här generaliserar du den virtuella datorn i Windows:

1. Logga in på din virtuella Windows-dator.
   
2. Öppna ett kommandotolksfönster som administratör. Ändra katalogen till %windir%\system32\sysprep `sysprep.exe`och kör sedan .
   
3. I dialogrutan **Systemförberedelseverktyg** väljer du **Ange oobe (System out-of-Box Experience)** och markerar kryssrutan **Generalisera.**
   
4. För **avstängningsalternativ**väljer du **Avstängning**.
   
5. Välj **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. När Sysprep är klart stängs den av den virtuella datorn. Starta inte om den virtuella datorn.

> [!TIP]
> **Valfritt** Använd [DISM](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) för att optimera avbildningen och minska den virtuella datorns första starttid.
>
> Om du vill optimera avbildningen monterar du den virtuella hårddisken genom att `/optimize-image` dubbelklicka på den i Utforskaren och kör sedan DISM med parametern.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Där D: är den monterade virtuella hårddiskens väg.
>
> Att `DISM /optimize-image` köra bör vara den sista ändringen du gör i din virtuella hårddisk. Om du gör några ändringar i din virtuella hårddisk före `DISM /optimize-image` distributionen måste du köra igen.

## <a name="create-a-managed-image-in-the-portal"></a>Skapa en hanterad avbildning i portalen 

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera vm-avbildningen. Sök efter och välj **Virtuella datorer**.

2. Välj din virtuella dator i listan.

3. På sidan **Virtuell dator** för den virtuella datorn väljer du **Fånga**på den övre menyn .

   Sidan **Skapa bild** visas.

4. För **Namn**accepterar du antingen det förifyllda namnet eller anger ett namn som du vill använda för bilden.

5. För **resursgrupp**väljer du antingen **Skapa nytt** och anger ett namn eller väljer en resursgrupp som ska användas i listrutan.

6. Om du vill ta bort källdatorns virtuella dator efter att avbildningen har skapats väljer du **Ta bort den virtuella datorn automatiskt när avbildningen har skapats**.

7. Om du vill använda bilden i en [tillgänglighetszon](../../availability-zones/az-overview.md)väljer du **På** för **zonåtersåterhet**.

8. Välj **Skapa** om du vill skapa bilden.

När bilden har skapats kan du hitta den som en **bildresurs** i listan över resurser i resursgruppen.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Skapa en bild av en virtuell dator med Powershell

 

Genom att skapa en avbildning direkt från den virtuella datorn säkerställs att avbildningen innehåller alla diskar som är associerade med den virtuella datorn, inklusive OS-disken och eventuella datadiskar. Det här exemplet visar hur du skapar en hanterad avbildning från en virtuell dator som använder hanterade diskar.

Innan du börjar kontrollerar du att du har den senaste versionen av Azure PowerShell-modulen. Om du vill `Get-Module -ListAvailable Az` hitta versionen kör du i PowerShell. Om du behöver uppgradera läser du [Installera Azure PowerShell i Windows med PowerShellGet](/powershell/azure/install-az-ps). Om du kör PowerShell lokalt `Connect-AzAccount` kan du köra för att skapa en anslutning med Azure.


> [!NOTE]
> Om du vill lagra avbildningen i zonupptredande lagring måste du skapa den `-ZoneResilient` i en region`New-AzImageConfig` som stöder [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera parametern i avbildningskonfigurationen (kommandot).

Så här skapar du en vm-avbildning:

1. Skapa några variabler.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Kontrollera att den virtuella datorn har tagits ut.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ange status för den virtuella datorn till **Generaliserad**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Hämta den virtuella datorn. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Skapa avbildningskonfigurationen.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Skapa en avbildning från en hanterad disk med PowerShell

Om du vill skapa en avbildning av endast OS-disken anger du det hanterade disk-ID:a-disken:

    
1. Skapa några variabler. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Hämta den virtuella datorn.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Hämta ID:t för den hanterade disken.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Skapa avbildningskonfigurationen.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Skapa en bild från en ögonblicksbild med Powershell

Du kan skapa en hanterad avbildning från en ögonblicksbild av en generaliserad virtuell dator genom att följa följande steg:

    
1. Skapa några variabler. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Hämta ögonblicksbilden.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Skapa avbildningskonfigurationen.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Skapa en avbildning från en virtuell dator som använder ett lagringskonto

Om du vill skapa en hanterad avbildning från en virtuell dator som inte använder hanterade diskar behöver du URI för virtuell hårddisk för operativsystemet i lagringskontot i följande format: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. I det här exemplet är den virtuella hårddisken i *mystorageaccount*, i en behållare med namnet *vhdcontainer*och VHD-filnamnet är *vhdfilename.vhd*.


1.  Skapa några variabler.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Stoppa/deallocate den virtuella datorn.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Markera den virtuella datorn som generaliserad.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Skapa avbildningen med hjälp av den allmänna virtuella hårddisken för OS.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Nästa steg
- [Skapa en virtuell dator från en hanterad avbildning](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

