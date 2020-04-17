---
title: Skapa en ohanterad avbildning av en allmän virtuell dator i Azure
description: Skapa en ohanterad avbildning av en generaliserad Windows-virtuell dator som ska användas för att skapa flera kopior av en virtuell dator i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 1f44f30666ead84b2bd2fc63d8a8eb624f70c85c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458087"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Så här skapar du en ohanterat vm-avbildning från en virtuell Azure-dator

Den här artikeln handlar om hur du använder lagringskonton. Vi rekommenderar att du använder hanterade diskar och hanterade avbildningar i stället för ett lagringskonto. Mer information finns i [Ta en hanterad avbildning av en allmän virtuell dator i Azure](capture-image-resource.md).

Den här artikeln visar hur du använder Azure PowerShell för att skapa en avbildning av en generaliserad Azure-virtuell dator med ett lagringskonto. Du kan sedan använda avbildningen för att skapa en annan virtuell dator. Avbildningen innehåller OS-disken och de datadiskar som är kopplade till den virtuella datorn. Avbildningen innehåller inte de virtuella nätverksresurserna, så du måste ställa in dessa resurser när du skapar den nya virtuella datorn. 

 

## <a name="generalize-the-vm"></a>Generalisera den virtuella datorn 
I det här avsnittet visas hur du generaliserar den virtuella Windows-datorn för att användas som avbildning. Genom att generalisera en virtuell dator tas all personlig kontoinformation bort och maskinen förbereds så att den används som avbildning. Mer information om Sysprep finns i [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Använda Sysprep: En introduktion).

Kontrollera att serverrollerna som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du laddar upp din virtuella hårddisk till Azure för första gången kontrollerar du att du har [förberett den virtuella datorn](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du kör Sysprep. 
> 
> 

Du kan också generalisera `sudo waagent -deprovision+user` en Virtuell Linux-dator med och sedan använda PowerShell för att fånga den virtuella datorn. Information om hur du använder CLI för att samla in en virtuell dator finns i [Så här generaliserar och fångar du en virtuell Linux-dator med Azure CLI](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Logga in på Azure PowerShell
1. Öppna Azure PowerShell och logga in på ditt Azure-konto.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Ett popup-fönster öppnas så att du kan ange dina Azure-kontouppgifter.
2. Hämta prenumerations-ID:erna för dina tillgängliga prenumerationer.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ange rätt prenumeration med hjälp av prenumerations-ID.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Frigöra den virtuella datorn och ange tillståndet till generaliserad

> [!IMPORTANT] 
> Du kan inte lägga till, redigera eller ta bort taggar från en virtuell dator när den har markerats som generaliserad. Om du vill lägga till en tagg i den virtuella datorn kontrollerar du att du lägger till taggarna innan du markerar den som generaliserad.
> 

1. Frigöra resurser för virtuella datorer.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Status för* den virtuella datorn i Azure-portalen ändras från **Stoppad** till **Stoppad (avtalad)**.
2. Ange status för den virtuella datorn till **Generaliserad**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Kontrollera status för den virtuella datorn. Avsnittet **OSState/generaliserad** för den virtuella datorn bör ha **DisplayStatus** inställt på **vm-generaliserad**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Skapa avbildningen

Skapa en ohantrad avbildning av virtuella datorer i behållaren för mållagring med det här kommandot. Avbildningen skapas i samma lagringskonto som den ursprungliga virtuella datorn. Parametern `-Path` sparar en kopia av JSON-mallen för källdatorn till den lokala datorn. Parametern `-DestinationContainerName` är namnet på den behållare som du vill behålla bilderna. Om behållaren inte finns skapas den åt dig.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Du kan hämta webbadressen till din bild från JSON-filmallen.  > Gå till avsnittet**resurslagringProfile** >  **resources****osDisk** > **image** > **uri** för hela sökvägen till avbildningen. Webbadressen till bilden ser `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`ut som: .
   
Du kan också verifiera URI i portalen. Avbildningen kopieras till en behållare med namnet **system** i ditt lagringskonto. 

## <a name="create-a-vm-from-the-image"></a>Skapa en VM från avbildningen

Nu kan du skapa en eller flera virtuella datorer från den ohanterat avbildningen.

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
    $location = "West US"
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
Följande PowerShell slutför konfigurationerna för den virtuella datorn och använder ohanterat avbildning som källa för den nya installationen.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
När du är klar bör du se den nyskapade virtuella datorn i [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**eller med hjälp av följande PowerShell-kommandon:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Information om hur du hanterar din nya virtuella dator med Azure PowerShell finns i [Hantera virtuella datorer med Azure Resource Manager och PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


