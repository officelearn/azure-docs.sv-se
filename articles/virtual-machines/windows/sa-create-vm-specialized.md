---
title: Skapa virtuell dator från en särskild disk i Azure | Microsoft Docs
description: Skapa en ny virtuell dator genom att koppla en särskild ohanterad disk i Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 0de7979edd741a7e4a1dc3354a8dc895929a9532
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811689"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Skapa en virtuell dator från en specialiserad virtuell Hårddisk i ett lagringskonto

Skapa en ny virtuell dator genom att koppla en särskild ohanterad disk som OS-disk med hjälp av Powershell. En särskild disk är en kopia av en virtuell Hårddisk från en befintlig virtuell dator som underhåller användarkonton, program och andra systemtillstånd från den ursprungliga virtuella datorn. 

Du kan välja mellan två alternativ:
* [Överföra en virtuell hårddisk](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopiera den virtuella Hårddisken för en befintlig Azure VM](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Innan du börjar
Om du använder PowerShell kan du kontrollera att du har den senaste versionen av AzureRM.Compute PowerShell-modulen. Kör följande kommando för att installera den.

```powershell
Install-Module AzureRM.Compute 
```
Mer information finns i [Azure PowerShell-versionshantering](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>Alternativ 1: Ladda upp en specialiserad virtuell Hårddisk

Du kan överföra den virtuella Hårddisken från en specialiserad virtuell dator som skapats med en lokal virtualisering verktyg, som Hyper-V eller en virtuell dator som har exporterats från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Du kan överföra en specialiserad virtuell Hårddisk som skapades med en lokal virtuell dator eller en virtuell Hårddisk som exporterats från ett annat moln. En specialiserad virtuell Hårddisk har användarkonton, program och andra systemtillstånd från den ursprungliga virtuella datorn. Om du planerar att använda den virtuella Hårddisken som – är att skapa en ny virtuell dator och se till att följande steg har slutförts. 
  
  * [Förbereda en Windows virtuell Hårddisk för överföring till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Inte** generalisera den virtuella datorn med hjälp av Sysprep.
  * Ta bort alla gäst virtualization-verktyg och agenter som installerats på den virtuella datorn (d.v.s. VMware-verktyg).
  * Se till att den virtuella datorn är konfigurerad för att hämta dess IP-adress och DNS-inställningarna via DHCP. Detta säkerställer att servern får en IP-adress inom det virtuella nätverket när den startas. 


### <a name="get-the-storage-account"></a>Hämta lagringskontot
Du behöver ett lagringskonto i Azure för att lagra den uppladdade avbildningen. Du kan antingen använda ett befintligt lagringskonto eller skapa en ny. 

Om du vill visa tillgängliga storage-konton, skriver du:

```powershell
Get-AzureRmStorageAccount
```

Om du vill använda ett befintligt lagringskonto fortsätter du till överföringen på VM-avbildning.

Följ dessa steg om du vill skapa ett lagringskonto:

1. Behöver du namnet på resursgruppen där lagringskontot ska skapas. Om du vill ta reda på alla resursgrupper i prenumerationen, skriver du:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Skapa en resursgrupp med namnet **myResourceGroup** i den **västra USA** region, typ:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Skapa ett lagringskonto med namnet **mystorageaccount** i den här resursgruppen med hjälp av den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Ladda upp den virtuella Hårddisken till ditt storage-konto
Använd den [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) cmdlet för att ladda upp avbildningen till en behållare i ditt storage-konto. Det här exemplet överför filen **myVHD.vhd** från `"C:\Users\Public\Documents\Virtual hard disks\"` till ett lagringskonto med namnet **mystorageaccount** i den **myResourceGroup** resursgrupp. Filen ska placeras i behållaren med namnet **mycontainer** och det nya filnamnet blir **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Om detta lyckas kan du få ett svar som ser ut ungefär så här:

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

Det här kommandot kan ta en stund att slutföra beroende på din nätverksanslutning och storleken på VHD-filen.


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Alternativ 2: Kopiera den virtuella Hårddisken från en befintlig Azure VM

Du kan kopiera en virtuell Hårddisk till ett annat lagringskonto för att använda när du skapar en ny, dubbla virtuell dator.

### <a name="before-you-begin"></a>Innan du börjar
Se till att du:

* Har du information om den **käll- och storage-konton**. Du måste ha de storage-konto och en behållare som för den Virtuella källdatorn. Vanligtvis behållarens namn kommer att **virtuella hårddiskar**. Du måste också ha en mållagringskontot. Om du inte redan har ett kan du skapa en med antingen portal (**alla tjänster** > lagringskonton > Lägg till) eller med hjälp av den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet. 
* Har hämtat och installerat den [AzCopy-verktyget](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Frigör den virtuella datorn
Frigör den virtuella datorn, vilket frigör den virtuella Hårddisken ska kopieras. 

* **Portal**: Klicka på **virtuella datorer** > **myVM** > Stoppa
* **PowerShell**: Använd [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) att stoppa (frigöra) den virtuella datorn med namnet **myVM** i resursgruppen **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Den **Status** för den virtuella datorn i Azure portal ändras från **stoppad** till **Stoppad (frigjord)**.

### <a name="get-the-storage-account-urls"></a>Hämta URL: er för storage-konto
Du behöver URL: er för käll- och storage-konton. URL: er se ut: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Om du redan vet namnet för storage-konto och en behållare kan du bara ersätta informationen mellan hakparenteser för att skapa din URL. 

Du kan använda Azure portal eller Azure Powershell för att hämta Webbadress:

* **Portal**: Klicka på den **>** för **alla tjänster** > **lagringskonton** > *lagringskonto*  >  **Blobar** och VHD-källfilen är förmodligen i den **virtuella hårddiskar** behållare. Klicka på **egenskaper** för behållaren och kopiera texten märkta **URL**. Du behöver URL: er för både källa och mål-behållare. 
* **PowerShell**: Använd [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) hämta information för virtuell dator med namnet **myVM** i resursgruppen **myResourceGroup**. I resultatet, titta i den **lagringsprofilen** för den **Vhd-Uri**. Den första delen av URI: N är URL: en till behållaren och den sista delen är OS-VHD-namnet för den virtuella datorn.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Hämta åtkomstnycklar för lagring
Hitta åtkomstnycklarna för källans och målets storage-konton. Läs mer om åtkomstnycklar [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

* **Portal**: Klicka på **alla tjänster** > **lagringskonton** > *lagringskonto* > **åtkomstnycklar**. Kopiera den nyckel som är märkta som **key1**.
* **PowerShell**: Använd [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) att hämta lagringsnyckeln för lagringskontot **mystorageaccount** i resursgruppen **myResourceGroup**. Kopiera den nyckel som är märkt **key1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopiera den virtuella Hårddisken
Du kan kopiera filer mellan lagringskonton med hjälp av AzCopy. Till Målbehållaren om den angivna behållaren inte finns, skapas den åt dig. 

Använd AzCopy, öppna en kommandotolk på den lokala datorn och navigera till mappen där AzCopy är installerat. Det ska vara detsamma som *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Om du vill kopiera alla filer i en behållare som du använder den **/S** växla. Detta kan användas för att kopiera en VHD-operativsystem och alla datadiskarna som om de finns i samma behållare. Det här exemplet visar hur du kopierar alla filer i behållaren **mysourcecontainer** i storage-konto **mysourcestorageaccount** till behållaren **mydestinationcontainer**i den **mydestinationstorageaccount** storage-konto. Ersätt namnen på storage-konton och behållare med dina egna. Ersätt `<sourceStorageAccountKey1>` och `<destinationStorageAccountKey1>` med dina egna nycklar.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Om du bara vill kopiera en viss VHD i en behållare med flera filer kan du även ange namnet på filen med hjälp av växeln /Pattern. I det här exemplet är den fil som heter **myFileName.vhd** ska kopieras.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


När det är klart får du ett meddelande som ser ut ungefär så:

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
* När du använder AZCopy, om du ser felet ”servern kunde inte autentisera begäran”, se till att har värdet för auktoriseringsrubriken formaterats korrekt inklusive signaturen. Om du använder nyckel 2 eller den sekundära lagringsnyckeln försöka använda den primära eller 1 lagringsnyckeln.

## <a name="create-the-new-vm"></a>Skapa ny virtuell dator 

Du behöver skapa nätverksprodukter och andra VM-resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-vnet"></a>Skapa undernät och virtuella nätverk

Skapa vNet och undernät för den [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

1. Skapa undernätet. Det här exemplet skapar ett undernät med namnet **mySubNet**, i resursgruppen **myResourceGroup**, och anger adressprefixet undernätet till **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa det virtuella nätverket. Det här exemplet anger det virtuella nätverksnamnet vara **myVnetName**, platsen som **västra USA**, och adressprefixet för det virtuella nätverket till **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverkssäkerhetsgruppen och en regel för RDP
Du måste ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389 för att kunna logga in på den virtuella datorn med RDP. Eftersom den virtuella Hårddisken för den nya virtuella datorn har skapats från en befintlig kan specialiserad virtuell dator, när den virtuella datorn skapas du använda ett befintligt konto från den virtuella källdatorn som har behörighet att logga in med RDP.
Det här måste slutföras innan du skapar nätverksgränssnitt som det ska kopplas.  
Det här exemplet anges NSG-namnet **myNsg** och Regelnamnet RDP till **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Läs mer om slutpunkter och NSG-regler, [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och nätverkskort
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa offentlig IP-adress. I det här exemplet anges namnet för offentliga IP-adress till **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa nätverkskortet. I det här exemplet är namnet på nätverkskortet har angetts **myNicName**. Det här steget associerar även Nätverkssäkerhetsgruppen som skapats tidigare med det här nätverkskortet.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ange den virtuella datorns namn och storlek

Det här exemplet anger virtuella datorns namn till ”myVM” och VM-storleken till ”Standard_A2”.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägga till NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurera OS-disken

1. Ange URI: N för den virtuella Hårddisken som du laddat upp eller kopieras. I det här exemplet VHD-fil med namnet **myOsDisk.vhd** förvaras i ett lagringskonto med namnet **myStorageAccount** i en behållare med namnet **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Lägg till OS-disken. I det här exemplet är termen ”osDisk” appened till VM-namn för att skapa OS-disknamnet när OS-disken skapas. Det här exemplet anger också att den här Windows-baserade virtuella Hårddisken ska kopplas till den virtuella datorn som OS-disk.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Valfritt: Om du har datadiskar som ska anslutas till den virtuella datorn kan du lägga till datadiskar med hjälp av URL: er för data virtuella hårddiskar och lämpliga logiska enhetsnummer (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

När du använder ett lagringskonto, data- och operativsystemets disk URL: er ut ungefär så här: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Du hittar detta i portalen genom att bläddra till mål-storage-behållare, att klicka på operativsystemet eller data-VHD som du kopierade och sedan kopiera innehållet i URL: en.


### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa den virtuella datorn med hjälp av de konfigurationer som vi precis skapade.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Om det här kommandot lyckades visas utdata som liknar detta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
Du bör se den nya virtuella datorn antingen i den [Azure-portalen](https://portal.azure.com)under **alla tjänster** > **virtuella datorer**, eller genom att använda följande PowerShell kommandon:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på din nya virtuella dator. Mer information finns i [hur du ansluter och logga in på Azure-datorer som kör Windows](connect-logon.md).

