---
title: Skapa en ohanterad avbildning av en generaliserad virtuell dator i Azure | Microsoft Docs
description: Skapa en unmanged avbildning av en generaliserad virtuell Windows-dator för att skapa flera kopior av en virtuell dator i Azure.
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
ms.openlocfilehash: b416acd9a2a3b03502b7eca11eade9dbd56f3afe
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
ms.locfileid: "34072057"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Hur du skapar en ohanterad VM-avbildning från en Azure VM

Den här artikeln täcker storage-konton. Vi rekommenderar att du använder hanterade diskar och hanterade bilder i stället för ett lagringskonto. Mer information finns i [samla in en hanterad avbildning av en generaliserad virtuell dator i Azure](capture-image-resource.md).

Den här artikeln visar hur du använder Azure PowerShell för att skapa en avbildning av en generaliserad virtuell Azure-dator med hjälp av ett lagringskonto. Du kan sedan använda avbildningen för att skapa en annan virtuell dator. Bilden innehåller operativsystemdisken och datadiskar som är kopplade till den virtuella datorn. Bilden innehåller inga virtuella nätverksresurser, så du måste ange dessa resurser när du skapar den nya virtuella datorn. 

## <a name="prerequisites"></a>Förutsättningar
Du behöver ha Azure PowerShell version 1.0.x eller nyare. Om du inte redan har installerat PowerShell, läsa [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview) för installationssteg.

## <a name="generalize-the-vm"></a>Generalisera den virtuella datorn 
Det här avsnittet visar hur du generalisera din Windows-dator för användning som en bild. Att generalisera en virtuell dator tar du bort alla dina personlig information, bland annat och förbereder datorn som ska användas som en bild. Mer information om Sysprep finns [så att använda Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx).

Se till att serverroller som körs på datorn som stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du överför den virtuella Hårddisken till Azure för första gången, kontrollera att du har [förberett din virtuella dator](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du kör Sysprep. 
> 
> 

Du kan också generalisera en Linux VM som använder `sudo waagent -deprovision+user` och sedan använda PowerShell för att avbilda den virtuella datorn. Information om hur du använder CLI för att avbilda en virtuell dator finns [så att generalisera och avbilda en Linux-dator med hjälp av Azure CLI ](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Logga in på Azure PowerShell
1. Öppna Azure PowerShell och logga in på ditt Azure-konto.
   
    ```powershell
    Connect-AzureRmAccount
    ```
   
    Ett popup-fönster öppnas där du kan ange dina autentiseringsuppgifter för Azure-konto.
2. Hämta ID: N för prenumeration för din tillgängliga prenumerationer.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Ange rätt prenumerationen med prenumerations-ID.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Frigör den virtuella datorn och ställa in tillståndet till generaliserad

> [!IMPORTANT] 
> Du kan inte lägga till, redigera eller ta bort taggar från en virtuell dator när den har markerats som generaliserad. Om du vill lägga till en tagg till den virtuella datorn, kontrollera att du lägger till taggar innan markeras som generaliserad.
> 

1. Frigöra VM-resurser.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Den *Status* för den virtuella datorn i Azure portal ändras från **stoppad** till **Stoppad (frigjord)**.
2. Ange status för den virtuella datorn till **generaliserad**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Kontrollera status för den virtuella datorn. Den **OSState/generaliserad** avsnittet för den virtuella datorn ska ha den **DisplayStatus** inställd på **VM generaliserad**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Skapa avbildningen

Skapa en avbildning av ohanterade virtuell dator i målbehållare för lagring med hjälp av det här kommandot. Bilden har skapats i samma lagringskonto som den ursprungliga virtuella datorn. Den `-Path` parametern sparar en kopia av JSON-mall för den Virtuella källdatorn till den lokala datorn. Den `-DestinationContainerName` parameter är namnet på den behållare som du vill hålla dina bilder. Om behållaren inte finns, skapas den åt dig.
   
```powershell
Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Du kan hämta URL för bilden från mallen JSON-fil. Gå till den **resurser** > **storageProfile** > **osDisk** > **bild**  >  **uri** avsnittet för den fullständiga sökvägen av avbildningen. URL till bilden som ser ut: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Du kan också kontrollera URI i portalen. Avbildningen kopieras till en behållare med namnet **system** i ditt lagringskonto. 

## <a name="create-a-vm-from-the-image"></a>Skapa en virtuell dator från avbildningen

Nu kan du skapa en eller flera virtuella datorer från ohanterade avbildningen.

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
Följande PowerShell Slutför konfigurationer av virtuella datorer och använder ohanterade avbildningen som källa för den nya installationen.

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

### <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
När du är klar bör du se den nya virtuella datorn i den [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**, eller genom att använda följande PowerShell-kommandon:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg
För att hantera din nya virtuella datorn med Azure PowerShell, se [hantera virtuella datorer med Azure Resource Manager och PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


