---
title: Skapa en ohanterad avbildning av en generaliserad virtuell dator i Azure | Microsoft Docs
description: Skapa en unmanged avbildning av en generaliserad virtuell Windows-dator som ska användas för att skapa flera kopior av en virtuell dator i Azure.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 45c59ccdd45a0c00635c3e0a3919248f33e2919a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102453"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Så här skapar du en ohanterad virtuell dator avbildning från en virtuell Azure-dator

Den här artikeln beskriver hur du använder lagrings konton. Vi rekommenderar att du använder hanterade diskar och hanterade avbildningar i stället för ett lagrings konto. Mer information finns i [avbilda en hanterad avbildning av en generaliserad virtuell dator i Azure](capture-image-resource.md).

Den här artikeln visar hur du använder Azure PowerShell för att skapa en avbildning av en generaliserad virtuell Azure-dator med ett lagrings konto. Du kan sedan använda avbildningen för att skapa en annan virtuell dator. Avbildningen inkluderar OS-disken och de data diskar som är anslutna till den virtuella datorn. Avbildningen omfattar inte de virtuella nätverks resurserna, så du måste konfigurera dessa resurser när du skapar den nya virtuella datorn. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-vm"></a>Generalisera den virtuella datorn 
Det här avsnittet visar hur du generaliserar din virtuella Windows-dator för användning som en avbildning. Generalisera en virtuell dator tar bort all personlig konto information, bland annat, och förbereder datorn som ska användas som en avbildning. Mer information om Sysprep finns i [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Använda Sysprep: En introduktion).

Kontrol lera att de Server roller som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för Server roller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du laddar upp din virtuella hård disk till Azure för första gången måste du se till att du har för [berett den virtuella](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) datorn innan du kör Sysprep. 
> 
> 

Du kan också generalisera en virtuell Linux-dator `sudo waagent -deprovision+user` med hjälp av och sedan använda PowerShell för att avbilda den virtuella datorn. Information om hur du använder CLI för att avbilda en virtuell dator finns i [generalisera och avbilda en virtuell Linux-dator med hjälp av Azure CLI](../linux/capture-image.md).


1. Logga in på den virtuella Windows-datorn.
2. Öppna Kommandotolken som administratör. Ändra katalogen till **%windir%\system32\sysprep** och kör sedan `sysprep.exe`.
3. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.
4. I **avslutnings alternativ**väljer du **Stäng**av.
5. Klicka på **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep har slutförts stängs den virtuella datorn av. 

> [!IMPORTANT]
> Starta inte om den virtuella datorn förrän du är klar med att ladda upp den virtuella hård disken till Azure eller skapa en avbildning från den virtuella datorn. Om den virtuella datorn oavsiktligt startas om kör du Sysprep för att generalisera den igen.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Logga in på Azure PowerShell
1. Öppna Azure PowerShell och logga in på ditt Azure-konto.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Ett popup-fönster öppnas där du kan ange dina autentiseringsuppgifter för Azure-kontot.
2. Hämta prenumerations-ID: na för dina tillgängliga prenumerationer.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ange rätt prenumeration med prenumerations-ID: t.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Frigör den virtuella datorn och ange status som generaliserad

> [!IMPORTANT] 
> Du kan inte lägga till, redigera eller ta bort taggar från en virtuell dator när den har marker ATS som generaliserad. Om du vill lägga till en tagg till den virtuella datorn kontrollerar du att du lägger till taggarna innan du markerar den som generaliserad.
> 

1. Frigör de virtuella dator resurserna.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    *Status* för den virtuella datorn i Azure Portal ändras från **stoppad** till **stoppad (Frigjord)** .
2. Ange statusen för den virtuella datorn som **generaliserad**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Kontrol lera statusen för den virtuella datorn. **OSState/generaliserat** avsnitt för den virtuella datorn ska ha **DisplayStatus** inställt på **VM generaliserad**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Skapa avbildningen

Skapa en ohanterad avbildning av virtuella datorer i mål lagrings behållaren med det här kommandot. Avbildningen skapas i samma lagrings konto som den ursprungliga virtuella datorn. `-Path` Parametern sparar en kopia av JSON-mallen för den virtuella käll datorn på den lokala datorn. `-DestinationContainerName` Parametern är namnet på den behållare som du vill lagra dina bilder i. Om behållaren inte finns skapas den åt dig.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Du kan hämta URL: en för din avbildning från JSON-filmallen. Gå till avsnittet > Resources**storageProfile** > **osDisk** > imageURI > för den fullständiga sökvägen till din avbildning. URL: en för bilden ser ut så `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`här:.
   
Du kan också kontrol lera URI: n i portalen. Avbildningen kopieras till en behållare med namnet **system** i ditt lagrings konto. 

## <a name="create-a-vm-from-the-image"></a>Skapa en VM från avbildningen

Nu kan du skapa en eller flera virtuella datorer från den ohanterade avbildningen.

### <a name="set-the-uri-of-the-vhd"></a>Ange URI för den virtuella hård disken

URI: n för den virtuella hård disken som ska användas är i formatet: https:///**mystorageaccount**. blob.Core.Windows.NET/finns**MyVhdName**. VHD. I det här exemplet finns den virtuella hård disken med namnet **myVHD** i lagrings kontot **mystorageaccount** i behållarens behållare.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa vNet och undernät för det [virtuella nätverket](../../virtual-network/virtual-networks-overview.md).

1. Skapa under nätet. I följande exempel skapas ett undernät med namnet **mitt undernät** i resurs gruppen **myResourceGroup** med adressprefixet **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa det virtuella nätverket. I följande exempel skapas ett virtuellt nätverk med namnet **myVnet** på platsen **västra USA** med adressprefixet **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Skapa en offentlig IP-adress och ett nätverks gränssnitt
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa en offentlig IP-adress. I det här exemplet skapas en offentlig IP-adress med namnet **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa NÄTVERKSKORTet. I det här exemplet skapas ett nätverkskort med namnet **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverks säkerhets gruppen och en RDP-regel
Om du vill kunna logga in på den virtuella datorn med RDP måste du ha en säkerhets regel som tillåter RDP-åtkomst på port 3389. 

I det här exemplet skapas en NSG med namnet **myNsg** som innehåller en regel som kallas **MYRDPRULE** som tillåter RDP-trafik över port 3389. Mer information om NSG: er finns i [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
Följande PowerShell Slutför konfigurationerna för den virtuella datorn och använder ohanterad avbildning som källa för den nya installationen.

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

### <a name="verify-that-the-vm-was-created"></a>Verifiera att den virtuella datorn har skapats
När du är klar bör du se den nyligen skapade virtuella datorn i [Azure Portal](https://portal.azure.com) under **Bläddra** > i**virtuella datorer**, eller genom att använda följande PowerShell-kommandon:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Information om hur du hanterar din nya virtuella dator med Azure PowerShell finns i [Hantera virtuella datorer med Azure Resource Manager och PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


