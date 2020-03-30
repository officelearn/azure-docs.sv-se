---
title: Ladda upp en generalisera virtuell hårddisk för att skapa flera virtuella datorer i Azure
description: Ladda upp en generaliserad virtuell hårddisk till ett Azure-lagringskonto för att skapa en Windows-virtuell dator som ska användas med Resurshanterarens distributionsmodell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 933b648f15418c4838d3da1ea8379267765c784b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073330"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Ladda upp en generaliserad virtuell hårddisk till Azure för att skapa en ny virtuell dator

Det här avsnittet handlar om att ladda upp en allmän ohanterad disk till ett lagringskonto och sedan skapa en ny virtuell dator med den uppladdade disken. En allmän VHD-avbildning har fått all din personliga kontoinformation borttagen med Sysprep. 

Om du vill skapa en virtuell dator från en specialiserad virtuell hårddisk i ett lagringskonto läser du [Skapa en virtuell dator från en specialiserad virtuell hårddisk](sa-create-vm-specialized.md).

Det här avsnittet handlar om att använda lagringskonton, men vi rekommenderar att kunderna flyttar till att använda hanterade diskar i stället. En fullständig genomgång av hur du förbereder, laddar upp och skapar en ny virtuell dator med hanterade diskar finns i [Skapa en ny virtuell dator från en generaliserad virtuell hårddisk som överförs till Azure med hanterade diskar](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

En allmän virtuell hårddisk har fått all din personliga kontoinformation borttagen med Sysprep. Om du tänker använda den virtuella hårddisken som en avbildning för att skapa nya virtuella datorer från bör du:
  
  * [Förbered en Virtuell Windows-hårddisk för att ladda upp till Azure](prepare-for-upload-vhd-image.md). 
  * Generalisera den virtuella datorn med Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalisera en virtuell Windows-dator med Sysprep
I det här avsnittet visas hur du generaliserar den virtuella Windows-datorn för att användas som avbildning. Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Använda Sysprep: En introduktion).

Kontrollera att serverrollerna som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du kör Sysprep innan du laddar upp din virtuella hårddisk till Azure för första gången kontrollerar du att du har [förberett den virtuella datorn](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du kör Sysprep. 
> 
> 

1. Logga in på den virtuella Windows-datorn.
2. Öppna Kommandotolken som administratör. Ändra katalogen till **%windir%\system32\sysprep** och kör sedan `sysprep.exe`.
3. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.
4. Välj **Avstängning** **i avstängningsalternativ**.
5. Klicka på **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep har slutförts stängs den virtuella datorn av. 

> [!IMPORTANT]
> Starta inte om den virtuella datorn förrän du har laddat upp den virtuella hårddisken till Azure eller skapa en avbildning från den virtuella datorn. Om den virtuella datorn startas om av misstag kör du Sysprep för att generalisera den igen.
> 
> 


## <a name="upload-the-vhd"></a>Ladda upp den virtuella hårddisken

Ladda upp den virtuella hårddisken till ett Azure-lagringskonto.

### <a name="log-in-to-azure"></a>Logga in på Azure
Om du inte redan har PowerShell version 1.4 eller senare installerat läser du [Så här installerar och konfigurerar](/powershell/azure/overview)du Azure PowerShell .

1. Öppna Azure PowerShell och logga in på ditt Azure-konto. Ett popup-fönster öppnas så att du kan ange dina Azure-kontouppgifter.
   
    ```powershell
    Connect-AzAccount
    ```
2. Hämta prenumerations-ID:erna för dina tillgängliga prenumerationer.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ange rätt prenumeration med hjälp av prenumerations-ID. Ersätt `<subscriptionID>` med ID:t för rätt prenumeration.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Hämta lagringskontot
Du behöver ett lagringskonto i Azure för att lagra den överförda VM-avbildningen. Du kan antingen använda ett befintligt lagringskonto eller skapa ett nytt. 

Om du vill visa tillgängliga lagringskonton skriver du:

```powershell
Get-AzStorageAccount
```

Om du vill använda ett befintligt lagringskonto går du vidare till avsnittet Ladda upp vm-avbildningen.

Om du behöver skapa ett lagringskonto gör du så här:

1. Du behöver namnet på resursgruppen där lagringskontot ska skapas. Om du vill ta reda på alla resursgrupper som finns i prenumerationen skriver du:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Om du vill skapa en resursgrupp med namnet **myResourceGroup** i regionen **Västra USA** skriver du:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Skapa ett lagringskonto med namnet **mystorageaccount** i den här resursgruppen med hjälp av cmdleten [New-AzStorageAccount:](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Starta uppladdningen 

Använd [cmdletet Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) för att ladda upp avbildningen till en behållare i ditt lagringskonto. Det här exemplet överför filen **myVHD.vhd** från `"C:\Users\Public\Documents\Virtual hard disks\"` till ett lagringskonto med namnet **mystorageaccount** i resursgruppen **myResourceGroup.** Filen kommer att placeras i behållaren som heter **mycontainer** och det nya filnamnet kommer att **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om det lyckas får du ett svar som liknar detta:

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

Beroende på nätverksanslutningen och storleken på vhd-filen kan det ta ett tag att slutföra det här kommandot.


## <a name="create-a-new-vm"></a>Skapa en ny virtuell dator 

Du kan nu använda den uppladdade virtuella hårddisken för att skapa en ny virtuell dator. 

### <a name="set-the-uri-of-the-vhd"></a>Ställ in URI för den virtuella hårddisken

URI för vhd att använda är i formatet: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. I det här exemplet är den virtuella hårddisken som heter **myVHD** i lagringskontot **mystorageaccount** i behållaren **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa det [virtuella nätverkets](../../virtual-network/virtual-networks-overview.md)virtuella nätverk och undernät .

1. Skapa undernätet. I följande exempel skapas ett undernät med namnet **mySubnet** i resursgruppen **myResourceGroup** med adressprefixet **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa det virtuella nätverket. I följande exempel skapas ett virtuellt nätverk med namnet **myVnet** på platsen i **västra USA** med adressprefixet **10.0.0.0/16**.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Skapa en offentlig IP-adress och ett nätverksgränssnitt
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa en offentlig IP-adress. I det här exemplet skapas en offentlig IP-adress med namnet **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa nätverkskortet. I det här exemplet skapas ett nätverkskort med namnet **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverkssäkerhetsgruppen och en RDP-regel
För att kunna logga in på den virtuella datorn med RDP måste du ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389. 

Det här exemplet skapar en NSG med namnet **myNsg** som innehåller en regel som kallas **myRdpRule** som tillåter RDP-trafik över port 3389. Mer information om NSG finns i [Öppna portar till en virtuell dator i Azure med PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Skapa en variabel för det virtuella nätverket
Skapa en variabel för det slutförda virtuella nätverket. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Skapa den virtuella datorn
Följande PowerShell-skript visar hur du ställer in konfigurationer för virtuella datorer och använder den uppladdade VM-avbildningen som källa för den nya installationen.



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
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
När du är klar bör du se den nyskapade virtuella datorn i [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**eller med hjälp av följande PowerShell-kommandon:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Information om hur du hanterar din nya virtuella dator med Azure PowerShell finns i [Hantera virtuella datorer med Azure Resource Manager och PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


