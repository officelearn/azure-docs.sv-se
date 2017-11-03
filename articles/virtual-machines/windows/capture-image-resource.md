---
title: Skapa en hanterad avbildning i Azure | Microsoft Docs
description: "Skapa en hanterad avbildning av en generaliserad virtuell dator eller virtuell Hårddisk i Azure. Bilder kan användas för att skapa flera virtuella datorer som använder hanterade diskar."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: d6409ac490f530d49f82c93b07b0fd22adbec4de
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Skapa en hanterad avbildning av en generaliserad virtuell dator i Azure

En hanterad avbildning resurs kan skapas från en generaliserad virtuell dator som lagras som en hanterad disk eller en ohanterad disk i ett lagringskonto. Bilden kan sedan användas för att skapa flera virtuella datorer. 


## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera Windows VM med hjälp av Sysprep

Sysprep tar bort alla dina personlig information, bland annat och förbereder datorn som ska användas som en bild. Mer information om Sysprep finns [så att använda Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx).

Se till att serverroller som körs på datorn som stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du kör Sysprep innan du laddar upp den virtuella Hårddisken till Azure för första gången, kontrollera att du har [förberett din virtuella dator](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du kör Sysprep. 
> 
> 

1. Logga in på Windows-dator.
2. Öppna Kommandotolken som administratör. Ändra katalogen till **%windir%\system32\sysprep**, och kör sedan `sysprep.exe`.
3. I den **systemförberedelseverktyget** dialogrutan **ange System Out of Box Experience (OOBE)**, och se till att den **Generalize** är markerad.
4. I **avstängningsalternativ**väljer **avstängning**.
5. Klicka på **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep har slutförts stängs den virtuella datorn. Starta inte om den virtuella datorn.


## <a name="create-a-managed-image-in-the-portal"></a>Skapa en hanterad avbildning på portalen 

1. Öppna den [portal](https://portal.azure.com).
2. Klicka på virtuella datorer på menyn till vänster och välj sedan den virtuella datorn från listan.
3. Klicka på sidan för den virtuella datorn på menyn övre **avbilda**.
3. I **namn**, skriver du namnet som du vill använda för avbildningen.
4. I **resursgruppen** Välj antingen **Skapa nytt** och ange ett namn eller välj **använda befintliga** och välja en resursgrupp i den nedrullningsbara listan.
5. Om du vill ta bort den Virtuella källdatorn efter att avbildningen har skapats, Välj **automatiskt ta bort den här virtuella datorn när du har skapat avbildningen**.
6. När du är klar klickar du på **skapa**.
16. När avbildningen har skapats visas den som en **bild** resurs i listan över resurser i resursgruppen.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Skapa en avbildning av en virtuell dator med hjälp av Powershell

Skapa en avbildning direkt från den virtuella datorn ser du till att bilden innehåller alla diskar som är kopplade till den virtuella datorn, inklusive OS-disken och eventuella hårddiskar. Det här exemplet visar hur du skapar en hanterad avbildning från en virtuell dator som använder hanterade diskar.


Innan du börjar bör du kontrollera att du har den senaste versionen av AzureRM.Compute PowerShell-modulen. Kör följande kommando för att installera den.

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Mer information finns i [Azure PowerShell versionshantering](/powershell/azure/overview).


1. Skapa några variabler.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Kontrollera att den virtuella datorn har frigjorts.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Ange status för den virtuella datorn till **generaliserad**. 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Hämta den virtuella datorn. 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Skapa image-konfigurationen.

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Skapa en avbildning från en hanterad disk med hjälp av PowerShell

Om du bara vill skapa en avbildning av OS-disk kan du också crate en avbildning genom att ange hanterade disk-ID som OS-disk.

    
1. Skapa några variabler. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Hämta den virtuella datorn.

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name myVM -ResourceGroupName $rgName
   ```

3. Hämta ID för den hantera disken.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Skapa image-konfigurationen.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Skapa en avbildning från en ögonblicksbild med hjälp av Powershell

Du kan skapa en hanterad avbildning från en ögonblicksbild av en generaliserad virtuell dator.

    
1. Skapa några variabler. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Hämta ögonblicksbilden.

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Skapa image-konfigurationen.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Skapa avbildningen.

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>Skapa avbildning från en virtuell Hårddisk i ett lagringskonto

Skapa en hanterad avbildning från en generaliserad OS-VHD i ett lagringskonto. Behöver du URI för den virtuella Hårddisken i storage-konto som är i formatet https://*mittlagringskonto*.blob.core.windows.net/*behållare*/*vhd_filename.vhd*. I det här exemplet är den virtuella Hårddisken som vi använder i *mittlagringskonto* i en behållare med namnet *vhdcontainer* och VHD-filnamnet är *osdisk.vhd*.


1.  Innan du kan definiera de gemensamma parametrarna:

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Step\deallocate den virtuella datorn.

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Markera den virtuella datorn som generaliserad.

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  Skapa avbildningen med din generaliserad OS-VHD.

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Nästa steg
- Nu kan du [skapa en virtuell dator från generaliserad hanterad avbildning](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

