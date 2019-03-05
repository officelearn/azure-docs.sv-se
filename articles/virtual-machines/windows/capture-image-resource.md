---
title: Skapa en hanterad avbildning i Azure | Microsoft Docs
description: Skapa en hanterad avbildning av en generaliserad virtuell dator eller virtuell Hårddisk i Azure. Bilder kan användas för att skapa flera virtuella datorer som använder hanterade diskar.
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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: b0dc587035509606059e3620201b2061bbe4d6a0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341773"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Skapa en hanterad avbildning av en generaliserad virtuell dator i Azure

En hanterad avbildningsresurs kan skapas från en generaliserad virtuell dator (VM) som lagras som en hanterad disk eller en ohanterad disk i ett lagringskonto. Avbildningen kan sedan användas för att skapa flera virtuella datorer. Information om hur hanterade avbildningar faktureras, se [priser för Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera den virtuella Windows-datorn med hjälp av Sysprep

Sysprep tar bort all personligt konto och säkerhetsinformation och förbereder datorn som ska användas som en bild. Information om Sysprep finns i [Sysprep översikt](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Se till att serverroller som körs på datorn som stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> När du har kört Sysprep på en virtuell dator, den virtuella datorn anses *generaliserad* och kan inte startas om. Det går inte att ångra processen för att generalisera en virtuell dator. Om du vill behålla den ursprungliga Virtuella fungerar kan du skapa en [kopia av den virtuella datorn](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) och generalisera kopian. 
>
> Om du planerar att köra Sysprep innan du laddar upp din virtuella hårddisk (VHD) till Azure för första gången, kontrollera att du har [förberett din virtuella dator](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  
> 
> 

Följ dessa steg för att generalisera den virtuella Windows-datorn:

1. Logga in på din Windows-VM.
   
2. Öppna ett kommandotolksfönster som administratör. Ändra katalogen till % windir%\system32\sysprep och kör sedan `sysprep.exe`.
   
3. I den **systemförberedelseverktyget** dialogrutan **ange System Out-of-Box Experience (OOBE)** och välj den **Generalize** markerar du kryssrutan.
   
4. För **Avslutningsalternativ**väljer **avstängning**.
   
5. Välj **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)

6. När Sysprep Slutför, stängs den virtuella datorn. Starta inte om den virtuella datorn.


## <a name="create-a-managed-image-in-the-portal"></a>Skapa en hanterad avbildning i portalen 

1. Öppna [Azure-portalen](https://portal.azure.com).

2. I menyn till vänster väljer **virtuella datorer** och välj sedan den virtuella datorn från listan.

3. I den **VM** för den virtuella datorn på den övre menyn, väljer **avbilda**.

   Den **Skapa avbildning** visas.

4. För **namn**, acceptera förifyllda namnet eller ange ett namn som du vill använda för avbildningen.

5. För **resursgrupp**, Välj antingen **Skapa nytt** och ange ett namn, eller välja **Använd befintlig** och välj en resursgrupp som ska användas från den nedrullningsbara listan.

6. Om du vill ta bort den Virtuella källdatorn när avbildningen har skapat, Välj **automatiskt ta bort den här virtuella datorn när du har skapat avbildningen**.

7. Om du vill kunna använda avbildningen i någon [tillgänglighetszon](../../availability-zones/az-overview.md)väljer **på** för **zonelasticitet**.

8. Välj **skapa** att skapa avbildningen.

9. När avbildningen har skapats kan du kan hitta den som en **bild** resurs i listan över resurser i resursgruppen.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Skapa en avbildning av en virtuell dator med Powershell

Skapa en avbildning direkt från den virtuella datorn ser du till att avbildningen innehåller alla diskar som är associerade med den virtuella datorn, inklusive OS-disken och eventuella datadiskar. Det här exemplet visar hur du skapar en hanterad avbildning från en virtuell dator som använder hanterade diskar.


Innan du börjar måste du se till att du har den senaste versionen av AzureRM.Compute PowerShell-modulen, som måste vara version 5.7.0-installationsprogram eller senare. Om du vill ta reda på vilken version du kör `Get-Module -ListAvailable AzureRM.Compute` i PowerShell. Om du behöver uppgradera kan du läsa [installera Azure PowerShell på Windows med PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps). Om du kör PowerShell lokalt, kör `Connect-AzAccount` att skapa en anslutning till Azure.


> [!NOTE]
> Om du vill lagra avbildningen i zonredundant lagring måste du skapa den i en region som har stöd för [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera den `-ZoneResilient` parameter i bildkonfiguration (`New-AzImageConfig` kommandot).

Följ dessa steg om du vill skapa en VM-avbildning:

1. Skapa några variabler.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Kontrollera att den virtuella datorn har frigjorts.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ange status för den virtuella datorn till **generaliserad**. 
   
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

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Skapa en avbildning från en hanterad disk med hjälp av PowerShell

Om du vill skapa en avbildning av endast OS-disken kan du ange ID för hanterad disk som OS-disken:

    
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

3. Hämta ID för den hantera disken.

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


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Skapa en avbildning från en ögonblicksbild med hjälp av Powershell

Du kan skapa en hanterad avbildning från en ögonblicksbild av en generaliserad virtuell dator genom att följa dessa steg:

    
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


## <a name="create-an-image-from-a-vhd-in-a-storage-account"></a>Skapa en avbildning från en virtuell Hårddisk i ett lagringskonto

Skapa en hanterad avbildning från en generaliserad OS-VHD i ett lagringskonto. Behöver du URI för den virtuella Hårddisken i storage-konto, som finns i följande format: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer* /  *vhdfilename.VHD*. I det här exemplet är den virtuella Hårddisken i *mystorageaccount*, i en behållare med namnet *vhdcontainer*, och VHD-filnamn är *vhdfilename.vhd*.


1.  Skapa några variabler.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Stoppa/frigör den virtuella datorn.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Markera den virtuella datorn som generaliserad.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Skapa avbildningen med hjälp av din generaliserade OS-VHD.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Nästa steg
- [Skapa en virtuell dator från en hanterad avbildning](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

