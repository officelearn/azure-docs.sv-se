---
title: Skapa en virtuell dator från en specialiserad disk i Azure | Microsoft Docs
description: Skapa en ny virtuell dator genom att koppla en specialiserad ohanterad disk i distributions modellen för Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: cc3c1d9352d9df44a51a917700c656055b8b8361
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088630"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Skapa en virtuell dator från en specialiserad virtuell hård disk i ett lagrings konto

Skapa en ny virtuell dator genom att koppla en specialiserad ohanterad disk som OS-disk med PowerShell. En specialiserad disk är en kopia av VHD från en befintlig virtuell dator som underhåller användar konton, program och andra tillstånds data från den ursprungliga virtuella datorn. 

Du kan välja mellan två alternativ:
* [Överföra en virtuell hårddisk](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopiera den virtuella hård disken för en befintlig virtuell Azure-dator](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="option-1-upload-a-specialized-vhd"></a>Alternativ 1: Ladda upp en specialiserad virtuell hård disk

Du kan ladda upp den virtuella hård disken från en specialiserad virtuell dator som skapats med ett lokalt virtualiseringslösningar, till exempel Hyper-V eller en virtuell dator som exporter ATS från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Du kan ladda upp en specialiserad virtuell hård disk som har skapats med en lokal virtuell dator eller en virtuell hård disk som exporter ATS från ett annat moln. En specialiserad virtuell hård disk hanterar användar konton, program och andra tillstånds data från den ursprungliga virtuella datorn. Om du tänker använda den virtuella hård disken för att skapa en ny virtuell dator kontrollerar du att följande steg är slutförda. 
  
  * [Förbered en virtuell Windows-hårddisk att ladda upp till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Generalisera **inte** den virtuella datorn med Sysprep.
  * Ta bort eventuella verktyg och agenter för gästautentisering som är installerade på den virtuella datorn (t. ex. VMware-verktyg).
  * Se till att den virtuella datorn är konfigurerad för att hämta dess IP-adress och DNS-inställningar via DHCP. Detta säkerställer att servern erhåller en IP-adress i VNet när den startas. 


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
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Ladda upp den virtuella hård disken till ditt lagrings konto
Använd cmdleten [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) för att ladda upp avbildningen till en behållare i ditt lagrings konto. I det här exemplet överförs filen **myVHD. VHD** från `"C:\Users\Public\Documents\Virtual hard disks\"` till ett lagrings konto med namnet **mystorageaccount** i resurs gruppen **myResourceGroup** . Filen kommer att placeras i behållaren som heter behållaren och det nya fil namnet kommer att vara **myUploadedVHD. VHD**.

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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Alternativ 2: Kopiera den virtuella hård disken från en befintlig virtuell Azure-dator

Du kan kopiera en virtuell hård disk till ett annat lagrings konto som ska användas när du skapar en ny, duplicerad virtuell dator.

### <a name="before-you-begin"></a>Innan du börjar
Kontrol lera att du:

* Ha information om **käll-och mål lagrings konton**. Du måste ha lagrings konto-och behållar namn för den virtuella käll datorn. Behållarens namn är vanligt vis **virtuella hård diskar**. Du måste också ha ett mål lagrings konto. Om du inte redan har en, kan du skapa en med hjälp av antingen portalen (**alla tjänster** > lagrings konton > Lägg till) eller med cmdleten [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) . 
* Har laddat ned och installerat [AzCopy-verktyget](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Frigöra den virtuella datorn
Frigör den virtuella hård disken som ska kopieras. 

* **Portal**: Klicka på **Virtual Machines** > **myVM** > Stop
* **PowerShell**: Använd [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) för att stoppa (frigöra) den virtuella datorn med namnet **myVM** i resurs gruppen **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Status** för den virtuella datorn i Azure Portal ändras från **stoppad** till **stoppad (Frigjord)** .

### <a name="get-the-storage-account-urls"></a>Hämta lagrings kontots URL: er
Du behöver URL: er för käll-och mål lagrings kontona. URL: erna ser ut `https://<storageaccount>.blob.core.windows.net/<containerName>/`så här:. Om du redan känner till lagrings kontot och behållar namnet kan du bara byta ut informationen mellan hakparenteserna för att skapa din URL. 

Du kan använda Azure Portal eller Azure PowerShell för att hämta URL: en:

* **Portal**:  >  >  >  **>** Klicka på lagrings kontots blobs för lagrings konto för alla tjänster och käll-VHD-filen finns förmodligen i behållaren för **virtuella hård diskar** . Klicka på **Egenskaper** för behållaren och kopiera texten med etiketten **URL**. Du behöver URL: er för både käll-och mål behållarna. 
* **PowerShell**: Använd [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) för att hämta information om den virtuella datorn med namnet **myVM** i resurs gruppen **myResourceGroup**. I resultatet tittar du på lagrings **profil** avsnittet för VHD- **URI: n**. Den första delen av URI: n är URL: en till behållaren och den sista delen är OS-VHD-namnet för den virtuella datorn.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Hämta åtkomst nycklar för lagring
Hitta åtkomst nycklarna för käll-och mål lagrings kontona. Mer information om åtkomst nycklar finns i [om Azure Storage-konton](../../storage/common/storage-create-storage-account.md).

* **Portal**: Klicka **på alla tjänster** > **lagrings konton** > *lagrings kontots* > **åtkomst nycklar**. Kopiera nyckeln märkt som **KEY1**.
* **PowerShell**: Använd [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) för att hämta lagrings nyckeln för lagrings kontot **mystorageaccount** i resurs gruppen **myResourceGroup**. Kopiera nyckeln med etiketten **KEY1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopiera den virtuella hård disken
Du kan kopiera filer mellan lagrings konton med AzCopy. Om den angivna behållaren inte finns i mål behållaren skapas den åt dig. 

Om du vill använda AzCopy öppnar du en kommando tolk på din lokala dator och navigerar till den mapp där AzCopy har installerats. Det kommer att likna *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Om du vill kopiera alla filer i en behållare använder du växeln **/s** . Detta kan användas för att kopiera OS-VHD: n och alla data diskar om de finns i samma behållare. Det här exemplet visar hur du kopierar alla filer i behållaren **mysourcecontainer** i lagrings kontot **mysourcestorageaccount** till behållaren **mydestinationcontainer** i **mydestinationstorageaccount** -lagrings kontot . Ersätt namnen på lagrings kontona och behållarna med dina egna. Ersätt `<sourceStorageAccountKey1>` och`<destinationStorageAccountKey1>` med dina egna nycklar.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Om du bara vill kopiera en speciell virtuell hård disk i en behållare med flera filer kan du också ange fil namnet med hjälp av/Pattern-växeln. I det här exemplet kopieras bara filen med namnet **fil namn. VHD** .

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


När det är klart visas ett meddelande som ser ut ungefär så här:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Felsökning
* Om du ser felet "servern kunde inte autentisera begäran" när du använder AZCopy måste du kontrol lera att värdet för Authorization-huvudet har skapats korrekt, inklusive signaturen. Om du använder nyckel 2 eller den sekundära lagrings nyckeln försöker du använda den primära eller första lagrings nyckeln.

## <a name="create-the-new-vm"></a>Skapa den nya virtuella datorn 

Du måste skapa nätverk och andra virtuella dator resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-vnet"></a>Skapa undernät och vNet

Skapa vNet och undernät för det [virtuella nätverket](../../virtual-network/virtual-networks-overview.md).

1. Skapa under nätet. I det här exemplet skapas ett undernät med namnet **mitt undernät**, i resurs gruppens **myResourceGroup**, och anger under nätets adressprefix till **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa vNet. Det här exemplet anger det virtuella nätverks namnet som ska vara **myVnetName**, platsen för **västra USA**och adressprefixet för det virtuella nätverket till **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverks säkerhets gruppen och en RDP-regel
   Om du vill kunna logga in på den virtuella datorn med RDP måste du ha en säkerhets regel som tillåter RDP-åtkomst på port 3389. Eftersom den virtuella hård disken för den nya virtuella datorn skapades från en befintlig specialiserad virtuell dator, kan du använda ett befintligt konto från den virtuella käll datorn som hade behörighet att logga in med RDP när den virtuella datorn har skapats.
   Detta måste slutföras innan du skapar nätverks gränssnittet som det ska associeras med.  
   Det här exemplet anger NSG namn till **myNsg** och RDP-regelns namn till **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Mer information om slut punkter och NSG-regler finns i [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och ett nätverkskort
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa den offentliga IP-adressen. I det här exemplet anges namnet på den offentliga IP-adressen till **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa NÄTVERKSKORTet. I det här exemplet är NÄTVERKSKORTets namn inställt på **myNicName**. I det här steget associeras även nätverks säkerhets gruppen som skapades tidigare med det här NÄTVERKSKORTet.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ange namn och storlek för den virtuella datorn

I det här exemplet anges namnet på den virtuella datorn till "myVM" och storleken på den virtuella datorn till "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägg till NÄTVERKSKORTet
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurera OS-disken

1. Ange URI för den virtuella hård disk som du laddade upp eller kopierade. I det här exemplet behålls VHD-filen med namnet **myOsDisk. VHD** i ett lagrings konto med namnet **myStorageAccount** i en behållare med namnet.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Lägg till OS-disken. I det här exemplet läggs termen "osDisk" till i det virtuella dator namnet för att skapa operativ systemets disk namn när operativ system disken skapas. I det här exemplet anges också att den här Windows-baserade virtuella hård disken ska kopplas till den virtuella datorn som operativ system disk.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Valfritt: Om du har data diskar som måste kopplas till den virtuella datorn lägger du till data diskarna genom att använda URL: er för data-VHD: er och rätt LUN-nummer (Logical Unit Number).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

När du använder ett lagrings konto ser data och operativ system disk-URL: er ut `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`ungefär så här:. Du hittar det här på portalen genom att bläddra till mål lagrings behållaren, klicka på det operativ system eller den data-VHD som kopierades och sedan kopiera innehållet i URL: en.


### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa den virtuella datorn med de konfigurationer som vi nyss skapade.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Om kommandot lyckades visas utdata som liknar följande:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verifiera att den virtuella datorn har skapats
Du bör se den nyligen skapade virtuella datorn antingen i [Azure Portal](https://portal.azure.com), under **alla tjänster** > **virtuella datorer**eller med hjälp av följande PowerShell-kommandon:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på den nya virtuella datorn. Mer information finns i [så här ansluter du och loggar in på en virtuell Azure-dator som kör Windows](connect-logon.md).

