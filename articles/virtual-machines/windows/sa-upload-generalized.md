---
title: "Överför generalize VHD att skapa flera virtuella datorer i Azure | Microsoft Docs"
description: "Överför en generaliserad virtuell Hårddisk till en Azure storage-konto för att skapa en virtuell Windows-dator för användning med Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ms.openlocfilehash: e6fc49855b449a7723a7f8a0c1c41516b3a44ee5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Ladda upp en generaliserad virtuell Hårddisk till Azure för att skapa en ny virtuell dator

Det här avsnittet beskriver ladda upp en generaliserad ohanterade disk till ett lagringskonto och sedan skapa en ny virtuell dator med den överförda disken. En generaliserad virtuell hårddiskavbildning har haft all personlig information bort med hjälp av Sysprep. 

Om du vill skapa en virtuell dator från en särskild virtuell Hårddisk i ett lagringskonto finns [skapa en virtuell dator från en särskild virtuell Hårddisk](sa-create-vm-specialized.md).

Det här avsnittet beskriver med lagringskonton, men vi rekommenderar kunder flytta till med hjälp av hanterade diskar i stället. En fullständig genomgång av hur du förbereder, ladda upp och skapa en ny virtuell dator med hjälp av hanterade diskar finns [skapa en ny virtuell dator från en generaliserad virtuell Hårddisk som har överförts till Azure med hjälp av hanterade diskar](upload-generalized-managed.md).



## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

En generaliserad virtuell Hårddisk har haft all personlig information bort med hjälp av Sysprep. Om du tänker använda den virtuella Hårddisken som en bild för att skapa nya virtuella datorer från bör du:
  
  * [Förbereda en Windows-VHD att överföra till Azure](prepare-for-upload-vhd-image.md). 
  * Generalisera den virtuella datorn med hjälp av Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalisera Windows-dator med hjälp av Sysprep
Det här avsnittet visar hur du generalisera din Windows-dator för användning som en bild. Sysprep tar bort alla dina personlig information, bland annat och förbereder datorn som ska användas som en bild. Mer information om Sysprep finns [så att använda Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx).

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
6. När Sysprep har slutförts stängs den virtuella datorn. 

> [!IMPORTANT]
> Starta inte om den virtuella datorn förrän du är klar överföra den virtuella Hårddisken till Azure eller skapa en avbildning från den virtuella datorn. Kör Sysprep för att generalisera den igen om den virtuella datorn av misstag hämtar startas om.
> 
> 


## <a name="upload-the-vhd"></a>Överför den virtuella Hårddisken

Överför den virtuella Hårddisken till ett Azure storage-konto.

### <a name="log-in-to-azure"></a>Logga in på Azure
Om du inte redan har PowerShell version 1.4 eller senare installerat, läsa [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

1. Öppna Azure PowerShell och logga in på ditt Azure-konto. Ett popup-fönster öppnas där du kan ange dina autentiseringsuppgifter för Azure-konto.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Hämta ID: N för prenumeration för din tillgängliga prenumerationer.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Ange rätt prenumerationen med prenumerations-ID. Ersätt `<subscriptionID>` med ID korrekt prenumeration.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Hämta storage-konto
Du behöver ett lagringskonto i Azure för att lagra överförda VM-avbildning. Du kan använda ett befintligt lagringskonto eller skapa en ny. 

Om du vill visa tillgängliga storage-konton, skriver du:

```powershell
Get-AzureRmStorageAccount
```

Om du vill använda ett befintligt lagringskonto fortsätter du till den [överför den Virtuella datoravbildningen](#upload-the-vm-vhd-to-your-storage-account) avsnitt.

Följ dessa steg om du behöver skapa ett lagringskonto:

1. Du måste namnet på resursgruppen där lagringskontot ska skapas. Om du vill ta reda på alla resursgrupper i din prenumeration, skriver du:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Så här skapar du en resursgrupp med namnet **myResourceGroup** i den **västra USA** region, typ:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Skapa ett lagringskonto med namnet **mittlagringskonto** i den här resursgruppen med hjälp av den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Starta överföringen 

Använd den [Lägg till AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) för att ladda upp avbildningen till en behållare i ditt lagringskonto. Det här exemplet överför filen **myVHD.vhd** från `"C:\Users\Public\Documents\Virtual hard disks\"` till ett lagringskonto med namnet **mittlagringskonto** i den **myResourceGroup** resursgruppen. Filen placeras i behållare med namnet **minbehållare** och det nya namnet kommer att **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om det lyckas, kan du få ett svar som liknar denna:

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

Det här kommandot kan ta en stund att slutföra beroende på nätverksanslutningen och storleken på VHD-filen.


## <a name="create-a-new-vm"></a>Skapa en ny virtuell dator 

Du kan nu använda den överförda virtuella Hårddisken för att skapa en ny virtuell dator. 

### <a name="set-the-uri-of-the-vhd"></a>Ange URI för den virtuella Hårddisken

URI för den virtuella Hårddisken ska använda är i formatet: https://**mittlagringskonto**.blob.core.windows.net/**minbehållare**/**MyVhdName**VHD. I det här exemplet den virtuella Hårddisken med namnet **myVHD** finns i lagringskontot **mittlagringskonto** i behållaren **minbehållare**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa vNet och undernät för den [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

1. Skapa undernätet. Följande exempel skapar ett undernät med namnet **mySubnet** i resursgruppen **myResourceGroup** med adressprefixet för **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa det virtuella nätverket. Följande exempel skapar ett virtuellt nätverk med namnet **myVnet** i den **västra USA** plats med adressprefixet för **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Skapa en offentlig IP-adress och gränssnitt
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa en offentlig IP-adress. Det här exemplet skapas en offentlig IP-adress med namnet **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa nätverkskortet. Det här exemplet skapar ett nätverkskort med namnet **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa säkerhetsgrupp för nätverk och en RDP-regel
Du måste ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389 för att kunna logga in på den virtuella datorn med RDP. 

Det här exemplet skapas en NSG som heter **myNsg** som innehåller en regel med namnet **myRdpRule** som tillåter RDP-trafik via port 3389. Mer information om NSG: er finns [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Skapa en variabel för det virtuella nätverket
Skapa en variabel för det virtuella nätverket som slutförda. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Skapa den virtuella datorn
Följande PowerShell-skript visar hur du ställer in konfigurationer av virtuella datorer och använder överförda VM-avbildning som källa för den nya installationen.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
När du är klar bör du se den nya virtuella datorn i den [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**, eller genom att använda följande PowerShell-kommandon:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg
För att hantera din nya virtuella datorn med Azure PowerShell, se [hantera virtuella datorer med Azure Resource Manager och PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


