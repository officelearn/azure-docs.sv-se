---
title: Skapa en ohanterad avbildning av en generaliserad virtuell dator i Azure | Microsoft Docs
description: Skapa en unmanged avbildning av en generaliserad Windows virtuell dator för att skapa flera kopior av en virtuell dator i Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: a7181c91424e1d905329eefd13f926d0b2700081
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864982"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Hur du skapar en ohanterad VM-avbildning från en Azure-dator

Den här artikeln beskriver storage-konton. Vi rekommenderar att du använder hanterade diskar och hanterade bilder i stället för ett lagringskonto. Mer information finns i [avbilda en hanterad avbildning av en generaliserad virtuell dator i Azure](capture-image-resource.md).

Den här artikeln visar hur du använder Azure PowerShell för att skapa en avbildning av en generaliserad virtuell Azure-dator med hjälp av ett lagringskonto. Du kan sedan använda avbildningen för att skapa en annan virtuell dator. Bilden innehåller operativsystemdisken och datadiskar som är kopplade till den virtuella datorn. Bilden innehåller inte de virtuella nätverksresurserna, så du måste konfigurera de här resurserna när du skapar den nya virtuella datorn. 

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="generalize-the-vm"></a>Generalisera den virtuella datorn 
Det här avsnittet visar hur att generalisera den virtuella datorn i Windows för användning som en bild. Generalisera en virtuell dator tar bort all personlig kontoinformation, bland annat och förbereder datorn som ska användas som en bild. Mer information om Sysprep finns i [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Använda Sysprep: En introduktion).

Se till att serverroller som körs på datorn som stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du överför en virtuell Hårddisk till Azure för första gången, kontrollera att du har [förberett din virtuella dator](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du kör Sysprep. 
> 
> 

Du kan också generalisera en Linux VM med hjälp av `sudo waagent -deprovision+user` och använda PowerShell för att avbilda den virtuella datorn. Information om hur du använder CLI för att avbilda en virtuell dator finns i [generalisera och avbilda en Linux-dator med hjälp av Azure CLI](../linux/capture-image.md).


1. Logga in på den virtuella datorn i Windows.
2. Öppna Kommandotolken som administratör. Ändra katalogen till **%windir%\system32\sysprep** och kör sedan `sysprep.exe`.
3. Välj **Starta OOBE för systemet (Out-of-Box Experience)** i dialogrutan **Systemförberedelseverktyget** och kontrollera att kryssrutan **Generalisera** är markerad.
4. I **Avslutningsalternativ**väljer **avstängning**.
5. Klicka på **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep har slutförts stängs den virtuella datorn av. 

> [!IMPORTANT]
> Starta inte om den virtuella datorn förrän du är klar ladda upp den virtuella Hårddisken till Azure eller skapa en avbildning från den virtuella datorn. Om den virtuella datorn av misstag hämtar om, kan du köra Sysprep för att generalisera den igen.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Logga in på Azure PowerShell
1. Öppna Azure PowerShell och logga in på ditt Azure-konto.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Ett popup-fönster öppnas där du kan ange dina autentiseringsuppgifter för Azure-konto.
2. Hämta prenumerations-ID för dina tillgängliga prenumerationer.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ange rätt prenumeration med hjälp av prenumerations-ID.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Frigör den virtuella datorn och ange läget till generaliserad

> [!IMPORTANT] 
> Du kan inte lägga till, redigera eller ta bort taggar från en virtuell dator när den har markerats som generaliserad. Om du vill lägga till en tagg till den virtuella datorn måste du kontrollera att du lägger till taggar innan du markerar det som generaliserad.
> 

1. Frigör VM-resurser.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Den *Status* för den virtuella datorn i Azure portal ändras från **stoppad** till **Stoppad (frigjord)**.
2. Ange status för den virtuella datorn till **generaliserad**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Kontrollera status för den virtuella datorn. Den **OSState/generaliserad** avsnittet för den virtuella datorn ska ha den **DisplayStatus** inställd **virtuell dator generaliserad**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Skapa avbildningen

Skapa en ohanterad VM-avbildning i mål-storage-behållare med hjälp av det här kommandot. Avbildningen skapas i samma lagringskonto som den ursprungliga virtuella datorn. Den `-Path` parametern sparar en kopia av JSON-mallen för den Virtuella källdatorn till din lokala dator. Den `-DestinationContainerName` parametern är namnet på den behållare som du vill lagra dina avbildningar. Om behållaren inte finns skapas den åt dig.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Du kan få URL: en för din avbildning från en mall för JSON-fil. Gå till den **resurser** > **storageProfile** > **osDisk** > **bild**  >  **uri** avsnittet för den fullständiga sökvägen i din avbildning. URL för bilden ser ut som: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Du kan också kontrollera URI: N i portalen. Avbildningen har kopierats till en behållare med namnet **system** i ditt lagringskonto. 

## <a name="create-a-vm-from-the-image"></a>Skapa en virtuell dator från avbildningen

Nu kan du skapa en eller flera virtuella datorer från ohanterad avbildning.

### <a name="set-the-uri-of-the-vhd"></a>Ange URI för den virtuella Hårddisken

URI för den virtuella Hårddisken ska använda har format: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**VHD. I det här exemplet den virtuella Hårddisken med namnet **myVHD** är i lagringskontot **mystorageaccount** i behållaren **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa vNet och undernät för den [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

1. Skapa undernätet. Följande exempel skapar ett undernät med namnet **mySubnet** i resursgruppen **myResourceGroup** med adressprefix **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa det virtuella nätverket. I följande exempel skapas ett virtuellt nätverk med namnet **myVnet** i den **västra USA** plats med adressprefix **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Skapa en offentlig IP-adress och ett nätverksgränssnitt
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa en offentlig IP-adress. Det här exemplet skapar en offentlig IP-adress med namnet **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa nätverkskortet. Det här exemplet skapar ett nätverkskort med namnet **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverkssäkerhetsgruppen och en regel för RDP
Du måste ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389 för att kunna logga in på den virtuella datorn med RDP. 

Det här exemplet skapar en Nätverkssäkerhetsgrupp med namnet **myNsg** som innehåller en regel som kallas **myRdpRule** som tillåter RDP-trafik via port 3389. Mer information om Nätverkssäkerhetsgrupper finns i [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
Skapa en variabel för det virtuella nätverket som slutförda. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Skapa den virtuella datorn
Följande PowerShell Slutför konfigurationerna för virtuella datorer och använder ohanterad avbildning som källa för den nya installationen.

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
När du är klar bör du se den nyligen skapade virtuella datorn i den [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**, eller genom att använda följande PowerShell kommandon:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg
För att hantera din nya virtuella dator med Azure PowerShell, se [hantera virtuella datorer med Azure Resource Manager och PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


