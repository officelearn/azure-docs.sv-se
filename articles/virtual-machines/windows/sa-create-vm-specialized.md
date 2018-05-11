---
title: Skapa virtuell dator från en särskild disk i Azure | Microsoft Docs
description: Skapa en ny virtuell dator genom att koppla en särskild ohanterade disk i Resource Manager-distributionsmodellen.
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
ms.openlocfilehash: da1fa2b182888e623f8df734c9119e208433e2bd
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Skapa en virtuell dator från en särskild virtuell Hårddisk i ett lagringskonto

Skapa en ny virtuell dator genom att koppla en särskild ohanterade disk som OS-disk med hjälp av Powershell. En specialiserad disk är en kopia av VHD från en befintlig virtuell dator som hanterar användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. 

Du kan välja mellan två alternativ:
* [Överföra en virtuell hårddisk](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopiera VHD från en befintlig virtuell Azure-dator](sa-create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Innan du börjar
Om du använder PowerShell kan du kontrollera att du har den senaste versionen av AzureRM.Compute PowerShell-modulen. Kör följande kommando för att installera den.

```powershell
Install-Module AzureRM.Compute 
```
Mer information finns i [Azure PowerShell versionshantering](/powershell/azure/overview).


## <a name="option-1-upload-a-specialized-vhd"></a>Alternativ 1: Ladda upp en särskild virtuell Hårddisk

Du kan överföra den virtuella Hårddisken från en särskild virtuell dator som skapats med ett lokalt virtualisering verktyg, som Hyper-V eller en virtuell dator som har exporterats från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Du kan ladda upp en särskild virtuell Hårddisk som har skapats med en lokal dator eller en virtuell Hårddisk som exporterats från ett annat moln. En särskild virtuell Hårddisk har användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. Om du tänker använda den virtuella Hårddisken som – är att skapa en ny virtuell dator och se till att följande åtgärder har slutförts. 
  
  * [Förbereda en Windows-VHD att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Inte** generalisera den virtuella datorn med hjälp av Sysprep.
  * Ta bort gäst virtualiseringsverktyg och agenter som installerats på den virtuella datorn (d.v.s. VMware-verktyg).
  * Se till att den virtuella datorn är konfigurerad för att hämta dess IP-adress och DNS-inställningarna via DHCP. Detta säkerställer att servern erhåller en IP-adress inom VNet när den startas. 


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
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Överför den virtuella Hårddisken till ditt lagringskonto
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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Alternativ 2: Kopiera den virtuella Hårddisken från en befintlig virtuell Azure-dator

Du kan kopiera en virtuell Hårddisk till en annan storage-konto som ska användas när du skapar en ny, dubbla virtuell dator.

### <a name="before-you-begin"></a>Innan du börjar
Se till att du:

* Innehåller information om den **käll- och storage-konton**. Du måste ha storage-konto och en behållare namn för den Virtuella källdatorn. Vanligtvis behållarens namn kommer att **virtuella hårddiskar**. Du måste också ha en mål-lagringskontot. Om du inte redan har en, du kan skapa en med hjälp av antingen portalen (**alla tjänster** > lagringskonton > Lägg till) eller med hjälp av den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet. 
* Har hämtat och installerat den [AzCopy verktyget](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Frigör den virtuella datorn
Frigör den virtuella datorn, vilket frigör den virtuella Hårddisken ska kopieras. 

* **Portalen**: Klicka på **virtuella datorer** > **myVM** > Stoppa
* **PowerShell**: Använd [stoppa AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) att stoppa (frigöra) den virtuella datorn med namnet **myVM** i resursgruppen **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Den **Status** för den virtuella datorn i Azure portal ändras från **stoppad** till **Stoppad (frigjord)**.

### <a name="get-the-storage-account-urls"></a>Hämta URL: er för storage-konto
Du behöver URL: er för de käll- och storage-kontona. URL: er se ut: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Du kan bara ersätta information mellan hakparenteser för att skapa din URL Om du redan vet lagernamnet konto och en behållare. 

Du kan använda Azure-portalen eller Azure Powershell för att hämta Webbadress:

* **Portalen**: Klicka på den **>** för **alla tjänster** > **lagringskonton** > *lagring kontot* > **Blobbar** och käll-VHD-filen är förmodligen i den **virtuella hårddiskar** behållare. Klicka på **egenskaper** för behållaren och kopiera texten märkta **URL**. Du behöver URL: er för både käll- och behållare. 
* **PowerShell**: Använd [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) att hämta information för den virtuella datorn med namnet **myVM** i resursgruppen **myResourceGroup**. I resultaten, titta i den **lagringsprofil** avsnittet för den **Vhd-Uri**. Den första delen av URI: N är Webbadressen till behållaren och den sista delen är virtuella Hårddiskens OS-namn för den virtuella datorn.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Hämta åtkomstnycklar för lagring
Hitta åtkomstnycklarna för käll- och storage-konton. Läs mer om åtkomstnycklarna [om Azure storage-konton](../../storage/common/storage-create-storage-account.md).

* **Portalen**: Klicka på **alla tjänster** > **lagringskonton** > *lagringskonto*  >   **Åtkomstnycklar**. Kopiera den nyckel som är märkta som **key1**.
* **PowerShell**: Använd [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) att hämta lagringsnyckeln för lagringskontot **mittlagringskonto** i resursgruppen **myResourceGroup**. Kopiera den nyckel som är märkta **key1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopiera den virtuella Hårddisken
Du kan kopiera filer mellan storage-konton med hjälp av AzCopy. För Målbehållaren om den angivna behållaren inte finns, kommer den att skapas för dig. 

Du använder AzCopy, öppna Kommandotolken på den lokala datorn och navigera till mappen där AzCopy är installerat. Den kommer att likna *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Om du vill kopiera alla filer i en behållare som du använder den **/S** växla. Detta kan användas för att kopiera den OS virtuella Hårddisken och alla datadiskar om de finns i samma behållare. Det här exemplet illustrerar hur du kopierar alla filer i behållaren **mysourcecontainer** i lagringskonto **mysourcestorageaccount** till behållaren **mydestinationcontainer**i den **mydestinationstorageaccount** storage-konto. Ersätta namnen på storage-konton och behållare med din egen. Ersätt `<sourceStorageAccountKey1>` och `<destinationStorageAccountKey1>` med egna nycklar.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Om du bara vill kopiera en viss VHD i en behållare med flera filer kan du också ange namnet på filen med hjälp av växeln /Pattern. I detta exempel på filen som heter **myFileName.vhd** ska kopieras.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


När den är klar, visas ett meddelande som ser ut ungefär så:

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
* När du använder AZCopy, om du ser felet ”servern gick inte att autentisera begäran”, se till att är värdet för Authorization-huvud formaterad korrekt inklusive signaturen. Om du använder nyckel 2 eller sekundära lagringsplatsen nyckeln, försök med den primära eller 1-nyckeln.

## <a name="create-the-new-vm"></a>Skapa ny virtuell dator 

Du måste skapa nätverk och andra VM resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-vnet"></a>Skapa undernät och virtuella nätverk

Skapa vNet och undernät för den [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

1. Skapa undernätet. Det här exemplet skapar ett undernät med namnet **mySubNet**, i resursgrupp **myResourceGroup**, och anger adressprefixet undernät till **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa vNet. Det här exemplet anger det virtuella nätverksnamnet ska **myVnetName**, platsen för **västra USA**, och adressprefixet för det virtuella nätverket till **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa säkerhetsgrupp för nätverk och en RDP-regel
Du måste ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389 för att kunna logga in på den virtuella datorn med RDP. Eftersom den virtuella Hårddisken för den nya virtuella datorn har skapats från en befintlig kan särskild virtuell dator när den virtuella datorn skapas du använda ett befintligt konto från den virtuella källdatorn som har behörighet att logga in med RDP.
Detta måste slutföras innan du skapar kommer att kopplas till nätverksgränssnittet.  
Det här exemplet anger NSG namn **myNsg** och Regelnamnet RDP till **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Mer information om slutpunkter och NSG-regler finns [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och ett nätverkskort
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa offentlig IP. I det här exemplet anges det offentliga IP-adress namnet till **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa nätverkskortet. I det här exemplet NIC-namnet har angetts **myNicName**. Det här steget associerar även Nätverkssäkerhetsgruppen som skapats tidigare med det här nätverkskortet.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ange namn på virtuell dator och storlek

Det här exemplet anger VM-namn till ”myVM” och VM-storlek till ”Standard_A2”.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägg till nätverkskortet
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurera OS-disk

1. Ange URI för den virtuella Hårddisken som du laddat upp eller kopieras. I det här exemplet VHD-filen med namnet **myOsDisk.vhd** sparas i ett lagringskonto med namnet **Mittlagringskonto** i en behållare med namnet **Minbehållare**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Lägg till OS-disk. I det här exemplet är termen ”osDisk” appened till VM-namn för att skapa OS-disknamnet när OS-disken skapas. Det här exemplet anger också att den här Windows-baserade virtuella Hårddisken ska kopplas till den virtuella datorn som OS-disk.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Valfritt: Om du har datadiskar som måste vara kopplad till den virtuella datorn, Lägg till datadiskar med hjälp av URL: er för data virtuella hårddiskar och den lämpliga logiska enhetsnummer (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

När du använder ett lagringskonto, data och operativsystemet disk webbadresser ut ungefär så här: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Du hittar det på portalen genom att bläddra till målbehållare för lagring, klicka på operativsystemet eller data VHD som har kopierats och sedan kopiera innehållet i URL: en.


### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa den virtuella datorn med hjälp av de konfigurationer som vi just skapade.

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
Du bör se den nyligen skapade Virtuellt antingen i den [Azure-portalen](https://portal.azure.com)under **alla tjänster** > **virtuella datorer**, eller genom att använda följande PowerShell kommandon:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på den nya virtuella datorn. Mer information finns i [ansluta och logga in på en virtuell Azure-dator kör Windows](connect-logon.md).

