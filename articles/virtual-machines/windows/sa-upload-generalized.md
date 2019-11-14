---
title: Ladda upp en generaliserad virtuell hård disk för att skapa flera virtuella datorer i Azure
description: Överför en generaliserad virtuell hård disk till ett Azure Storage-konto för att skapa en virtuell Windows-dator som ska användas med distributions modellen för Resource Manager.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073330"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Överför en generaliserad virtuell hård disk till Azure för att skapa en ny virtuell dator

Det här avsnittet handlar om att ladda upp en generaliserad ohanterad disk till ett lagrings konto och sedan skapa en ny virtuell dator med hjälp av den uppladdade disken. En generaliserad VHD-avbildning har haft all personlig konto information borttagen med hjälp av Sysprep. 

Om du vill skapa en virtuell dator från en specialiserad virtuell hård disk i ett lagrings konto, se [skapa en virtuell dator från en specialiserad virtuell hård disk](sa-create-vm-specialized.md).

Det här avsnittet beskriver hur du använder lagrings konton, men vi rekommenderar att kunderna flyttar till att använda Managed Disks i stället. En fullständig genom gång av hur du förbereder, laddar upp och skapar en ny virtuell dator med hjälp av Managed disks finns i [skapa en ny virtuell dator från en generaliserad virtuell hård disk som överförs till Azure med hjälp av Managed disks](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

En generaliserad virtuell hård disk har haft all personlig konto information borttagen med hjälp av Sysprep. Om du tänker använda den virtuella hård disken som en avbildning för att skapa nya virtuella datorer från bör du:
  
  * [Förbered en virtuell Windows-hårddisk att ladda upp till Azure](prepare-for-upload-vhd-image.md). 
  * Generalisera den virtuella datorn med Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalisera en virtuell Windows-dator med Sysprep
Det här avsnittet visar hur du generaliserar din virtuella Windows-dator för användning som en avbildning. Sysprep tar bland annat bort all din personliga kontoinformation och förbereder datorn så att den kan användas som en avbildning. Mer information om Sysprep finns i [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Använda Sysprep: En introduktion).

Kontrol lera att de Server roller som körs på datorn stöds av Sysprep. Mer information finns i [Sysprep-stöd för Server roller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du kör Sysprep innan du laddar upp din virtuella hård disk till Azure för första gången, måste du se till att du har för [berett den virtuella](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) datorn innan du kör Sysprep. 
> 
> 

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


## <a name="upload-the-vhd"></a>Ladda upp den virtuella hård disken

Överför den virtuella hård disken till ett Azure Storage-konto.

### <a name="log-in-to-azure"></a>Logga in på Azure
Om du inte redan har PowerShell version 1,4 eller senare installerat läser [du så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

1. Öppna Azure PowerShell och logga in på ditt Azure-konto. Ett popup-fönster öppnas där du kan ange dina autentiseringsuppgifter för Azure-kontot.
   
    ```powershell
    Connect-AzAccount
    ```
2. Hämta prenumerations-ID: na för dina tillgängliga prenumerationer.
   
    ```powershell
    Get-AzSubscription
    ```
3. Ange rätt prenumeration med prenumerations-ID: t. Ersätt `<subscriptionID>` med ID: t för rätt prenumeration.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Hämta lagrings kontot
Du behöver ett lagrings konto i Azure för att lagra den uppladdade VM-avbildningen. Du kan antingen använda ett befintligt lagrings konto eller skapa ett nytt. 

Om du vill visa tillgängliga lagrings konton skriver du:

```powershell
Get-AzStorageAccount
```

Om du vill använda ett befintligt lagrings konto går du vidare till avsnittet Ladda upp avbildningen av den virtuella datorn.

Följ dessa steg om du behöver skapa ett lagrings konto:

1. Du behöver namnet på den resurs grupp där lagrings kontot ska skapas. Om du vill ta reda på alla resurs grupper som finns i din prenumeration skriver du:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Om du vill skapa en resurs grupp med namnet **myResourceGroup** i regionen **USA, västra** , skriver du:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Skapa ett lagrings konto med namnet **mystorageaccount** i den här resurs gruppen genom att använda cmdleten [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) :
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Starta överföringen 

Använd cmdleten [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) för att ladda upp avbildningen till en behållare i ditt lagrings konto. I det här exemplet överförs filen **myVHD. VHD** från `"C:\Users\Public\Documents\Virtual hard disks\"` till ett lagrings konto med namnet **mystorageaccount** i resurs gruppen **myResourceGroup** . Filen kommer att placeras i behållaren **som heter** behållaren och det nya fil namnet kommer att vara **myUploadedVHD. VHD**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om det lyckas får du ett svar som ser ut ungefär så här:

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

Beroende på din nätverks anslutning och storleken på VHD-filen kan det här kommandot Ta en stund att slutföra.


## <a name="create-a-new-vm"></a>Skapa en ny virtuell dator 

Du kan nu använda den överförda virtuella hård disken för att skapa en ny virtuell dator. 

### <a name="set-the-uri-of-the-vhd"></a>Ange URI för den virtuella hård disken

URI: n för den virtuella hård disken som ska användas är i formatet: https://**mystorageaccount** **. blob.Core.Windows.net/** /**MyVhdName**. VHD. I det här exemplet finns den virtuella hård disken med namnet **myVHD** i lagrings kontot **mystorageaccount** i behållarens **behållare.**

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
    $location = "WestUS"
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
Följande PowerShell-skript visar hur du konfigurerar konfigurationen för virtuella datorer och använder den överförda VM-avbildningen som källa för den nya installationen.



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

## <a name="verify-that-the-vm-was-created"></a>Verifiera att den virtuella datorn har skapats
När du är klar bör du se den nyligen skapade virtuella datorn i [Azure Portal](https://portal.azure.com) under **Bläddra** > **virtuella datorer**, eller genom att använda följande PowerShell-kommandon:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Information om hur du hanterar din nya virtuella dator med Azure PowerShell finns i [Hantera virtuella datorer med Azure Resource Manager och PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


