---
title: Skapa virtuell dator från en specialiserad disk i Azure
description: Skapa en ny virtuell dator genom att koppla en specialiserad ohanterat disk, i Resurshanterarens distributionsmodell.
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
ms.openlocfilehash: d887ef2ef74bb433d6e8ae7f53cd0b77f5948303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073352"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Skapa en virtuell dator från en specialiserad virtuell hårddisk i ett lagringskonto

Skapa en ny virtuell dator genom att koppla en specialiserad ohanterad disk som OS-disk med Powershell. En specialiserad disk är en kopia av VHD från en befintlig virtuell dator som underhåller användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. 

Du kan välja mellan två alternativ:
* [Ladda upp en virtuell hårddisk](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Kopiera den virtuella hårddisken för en befintlig Azure-virtuell dator](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Alternativ 1: Ladda upp en specialiserad virtuell hårddisk

Du kan ladda upp den virtuella hårddisken från en specialiserad virtuell dator som skapats med ett lokalt virtualiseringsverktyg, till exempel Hyper-V, eller en virtuell dator som exporteras från ett annat moln.

### <a name="prepare-the-vm"></a>Förbereda den virtuella datorn
Du kan ladda upp en specialiserad virtuell hårddisk som har skapats med en lokal virtuell dator eller en virtuell hårddisk som exporteras från ett annat moln. En specialiserad virtuell hårddisk underhåller användarkonton, program och andra tillståndsdata från den ursprungliga virtuella datorn. Om du tänker använda den virtuella hårddisken som den är för att skapa en ny virtuell dator, se till att följande steg har slutförts. 
  
  * [Förbered en Virtuell Windows-hårddisk för att ladda upp till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Generalisera inte** den virtuella datorn med Sysprep.
  * Ta bort alla gästvirtualiseringsverktyg och agenter som är installerade på den virtuella datorn (dvs. VMware-verktyg).
  * Kontrollera att den virtuella datorn är konfigurerad för att hämta sina IP-adress- och DNS-inställningar via DHCP. Detta säkerställer att servern skaffar en IP-adress i det virtuella nätverket när den startar. 


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
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Ladda upp den virtuella hårddisken till lagringskontot
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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Alternativ 2: Kopiera den virtuella hårddisken från en befintlig Azure-virtuell dator

Du kan kopiera en virtuell hårddisk till ett annat lagringskonto som ska användas när du skapar en ny, dubblett-VM.

### <a name="before-you-begin"></a>Innan du börjar
Se till att du:

* Ha information om **käll- och mållagringskontona**. För källdatorns virtuella dator måste du ha lagringskontot och behållarnamnen. Vanligtvis kommer behållarnamnet att **vara virtuella hårddiskar**. Du måste också ha ett mållagringskonto. Om du inte redan har en kan du skapa en med antingen portalen (**Alla tjänster** > lagringskonton > Lägg till) eller med cmdleten [New-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) 
* Har laddat ner och installerat [AzCopy verktyget](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Frigöra den virtuella datorn
Deallocate den virtuella datorn, vilket frigör den virtuella hårddisken som ska kopieras. 

* **Portal:** Klicka **virtuella datorer** > **myVM** > Stop
* **Powershell**: Använd [Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) för att stoppa (deallocate) den virtuella datorn som heter **myVM** i resursgruppen **myResourceGroup**.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

**Status för** den virtuella datorn i Azure-portalen ändras från **Stoppad** till **Stoppad (avtalad)**.

### <a name="get-the-storage-account-urls"></a>Hämta URL:er för lagringskonto
Du behöver url:erna för käll- och mållagringskontona. Webbadresserna ser ut `https://<storageaccount>.blob.core.windows.net/<containerName>/`som: . Om du redan känner till lagringskontot och behållarnamnet kan du bara ersätta informationen mellan parenteserna för att skapa webbadressen. 

Du kan använda Azure-portalen eller Azure Powershell för att få URL:en:

* **Portal**: **>** Klicka på för **alla tjänster** > **Lagringskonton** > *lagringskonto* > **Blobbar** och din källa VHD-fil är förmodligen i **vhds** behållaren. Klicka på **Egenskaper** för behållaren och kopiera texten med etiketten **URL.** Du behöver webbadresserna för både käll- och målbehållarna. 
* **Powershell**: Använd [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) för att hämta information för VM som heter **myVM** i resursgruppen **myResourceGroup**. I resultatet tittar du i avsnittet **Lagringsprofil** för **Vhd Uri**. Den första delen av Uri är URL:en till behållaren och den sista delen är OS VHD-namnet för den virtuella datorn.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Hämta lagringsnycklarna
Hitta åtkomstnycklarna för käll- och mållagringskontona. Mer information om åtkomstnycklar finns i [Om Azure-lagringskonton](../../storage/common/storage-create-storage-account.md).

* **Portal**: Klicka på **Alla tjänster** > **Lagringskonton** > *lagringskonto* > **Åtkomstnycklar**. Kopiera nyckeln som är märkt som **nyckel1**.
* **Powershell**: Använd [Get-AzStorageAccountKey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) för att hämta lagringsnyckeln för lagringskontot **mystorageaccount** i resursgruppen **myResourceGroup**. Kopiera nyckeln med etiketten **nyckel1**.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Kopiera den virtuella hårddisken
Du kan kopiera filer mellan lagringskonton med AzCopy. Om den angivna behållaren inte finns för målbehållaren skapas den åt dig om den angivna behållaren inte finns. 

Om du vill använda AzCopy öppnar du en kommandotolk på den lokala datorn och navigerar till mappen där AzCopy är installerat. Det liknar *C:\Program Files (x86)\Microsoft SDK\Azure\AzCopy*. 

Om du vill kopiera alla filer i en behållare använder du växeln **/S.** Detta kan användas för att kopiera OS VHD och alla datadiskar om de finns i samma behållare. Det här exemplet visar hur du kopierar alla filer i behållaren **mysourcecontainer** i lagringskontot **mysourcestorageaccount** till **behållaren mydestinationcontainer** i **mydestinationstorageakt** lagringskontot. Ersätt namnen på lagringskontona och behållarna med dina egna. Byt ut `<sourceStorageAccountKey1>` och `<destinationStorageAccountKey1>` med dina egna nycklar.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Om du bara vill kopiera en viss virtuell hårddisk i en behållare med flera filer kan du också ange filnamnet med växeln /Pattern. I det här exemplet kopieras endast filen som heter **myFileName.vhd.**

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


När den är klar får du ett meddelande som ser ut ungefär som:

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
* När du använder AZCopy, om felet "Servern misslyckades med att autentisera begäran", kontrollerar du att värdet för auktoriseringshuvudet har skapats korrekt inklusive signaturen. Om du använder Nyckel 2 eller den sekundära lagringsnyckeln kan du prova att använda den primära eller 1:a lagringsnyckeln.

## <a name="create-the-new-vm"></a>Skapa den nya virtuella datorn 

Du måste skapa nätverk och andra vm-resurser som ska användas av den nya virtuella datorn.

### <a name="create-the-subnet-and-vnet"></a>Skapa undernätet och det virtuella nätverket

Skapa det [virtuella nätverkets](../../virtual-network/virtual-networks-overview.md)virtuella nätverk och undernät .

1. Skapa undernätet. I det här exemplet skapas ett undernät med namnet **mySubNet**i resursgruppen **myResourceGroup**och undernätsadressprefixet anges **till 10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa det virtuella nätverket. I det här exemplet anges det virtuella nätverksnamnet som **myVnetName**, platsen till **västra USA**och adressprefixet för det virtuella nätverket till **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa nätverkssäkerhetsgruppen och en RDP-regel
   För att kunna logga in på den virtuella datorn med RDP måste du ha en säkerhetsregel som tillåter RDP-åtkomst på port 3389. Eftersom den virtuella datorn för den nya virtuella datorn skapades från en befintlig specialiserad virtuell dator, kan du använda ett befintligt konto från den virtuella datorn som hade behörighet att logga in med RDP när den virtuella datorn har skapats.
   Detta måste slutföras innan du skapar det nätverksgränssnitt som det kommer att associeras med.  
   I det här exemplet anges NSG-namnet **på myNsg** och RDP-regelnamnet till **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Mer information om slutpunkter och NSG-regler finns i [Öppna portar till en virtuell dator i Azure med PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Skapa en offentlig IP-adress och ett nätverkskort
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa den offentliga IP-adressen. I det här exemplet är det offentliga IP-adressnamnet inställt på **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Skapa nätverkskortet. I det här exemplet är nätverkskortets namn inställt på **myNicName**. Det här steget associerar också nätverkssäkerhetsgruppen som skapats tidigare med det här nätverkskortet.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>Ange vm-namn och storlek

I det här exemplet anges vm-namnet på "myVM" och vm-storleken till "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Lägg till nätverkskortet
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Konfigurera OS-disken

1. Ange URI för den virtuella hårddisk som du laddade upp eller kopierade. I det här exemplet lagras VHD-filen som heter **myOsDisk.vhd** i ett lagringskonto med namnet **myStorageAccount** i en behållare med namnet **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Lägg till OS-disken. I det här exemplet läggs termen "osDisk" till i vm-namnet när OS-disken skapas. I det här exemplet anges också att den här Windows-baserade virtuella hårddisken ska kopplas till den virtuella datorn som OS-disken.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Valfritt: Om du har datadiskar som måste kopplas till den virtuella datorn lägger du till datadiskarna med hjälp av URL:erna för virtuella data-hårddiskar och lämpligt logiskt enhetsnummer (Lun).

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

När du använder ett lagringskonto ser url:erna `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`för data och operativsystem disk ungefär så här: . Du hittar detta på portalen genom att bläddra till mållagringsbehållaren, klicka på operativsystemet eller data VHD som kopierades och sedan kopiera innehållet i webbadressen.


### <a name="complete-the-vm"></a>Slutför den virtuella datorn 

Skapa den virtuella datorn med de konfigurationer som vi just skapade.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Om det här kommandot lyckades visas utdata så här:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
Du bör se den nyligen skapade virtuella datorn antingen i [Azure-portalen,](https://portal.azure.com)under **Alla virtuella tjänster** > **datorer**eller med hjälp av följande PowerShell-kommandon:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Nästa steg
Logga in på din nya virtuella dator. Mer information finns i [Så här ansluter och loggar du in på en virtuell Azure-dator som kör Windows](connect-logon.md).

