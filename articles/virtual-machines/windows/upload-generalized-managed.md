---
title: "Skapa en hanterad Azure virtuell dator från en virtuell Hårddisk generaliserad lokalt | Microsoft Docs"
description: "Överför en generaliserad virtuell Hårddisk till Azure och använda den för att skapa nya virtuella datorer, i Resource Manager-distributionsmodellen."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: cynthn
ms.openlocfilehash: d802ba16ecb4e32e2adb7be3a8e99c72a1625841
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Överför en generaliserad virtuell Hårddisk och använda den för att skapa nya virtuella datorer i Azure

Det här avsnittet beskriver hur du använder PowerShell för att överföra en VHD från en generaliserad virtuell dator till Azure, skapa en avbildning från den virtuella Hårddisken och skapa en ny virtuell dator från den avbildningen. Du kan överföra en virtuell Hårddisk som exporteras från ett verktyg för virtualisering av lokalt eller från ett annat moln. Med hjälp av [hanterade diskar](managed-disks-overview.md) för den nya virtuella datorn förenklas VM och ger bättre tillgänglighet när den virtuella datorn placeras i en tillgänglighetsuppsättning. 

Om du vill använda ett exempelskript finns [exempel på skript för att överföra en virtuell Hårddisk till Azure och skapa en ny virtuell dator](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Innan du börjar

- Du bör följa innan du laddar upp alla VHD till Azure [förbereda en Windows-VHD eller VHDX för att överföra till Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Granska [planera för migrering till hanterade diskar](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) innan du påbörjar migreringen till [hanterade diskar](managed-disks-overview.md).
- Kontrollera att du har den senaste versionen av AzureRM.Compute PowerShell-modulen. Kör följande kommando för att installera den.

    ```powershell
    Install-Module AzureRM.Compute -RequiredVersion 2.6.0
    ```
    Mer information finns i [Azure PowerShell versionshantering](/powershell/azure/overview).


## <a name="generalize-the-windows-vm-using-sysprep"></a>Generalisera Windows VM med hjälp av Sysprep

Sysprep tar bort alla dina personlig information, bland annat och förbereder datorn som ska användas som en bild. Mer information om Sysprep finns [så att använda Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx).

Se till att serverroller som körs på datorn som stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Om du kör Sysprep innan du laddar upp den virtuella Hårddisken till Azure för första gången, kontrollera att du har [förberett din virtuella dator](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) innan du kör Sysprep. 
> 
> 

1. Logga in på Windows-dator.
2. Öppna Kommandotolken som administratör. Ändra katalogen till **%windir%\system32\sysprep**, och kör sedan `sysprep.exe`.
3. I den **systemförberedelseverktyget** dialogrutan **ange System Out of Box Experience (OOBE)**, och se till att den **Generalize** är markerad.
4. I **avstängningsalternativ**väljer **avstängning**.
5. Klicka på **OK**.
   
    ![Starta Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. När Sysprep har slutförts stängs den virtuella datorn. Starta inte om den virtuella datorn.



## <a name="log-in-to-azure"></a>Logga in på Azure
Om du inte redan har PowerShell version 1.4 eller senare installerat, läsa [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

1. Öppna Azure PowerShell och logga in på ditt Azure-konto. Ett popup-fönster öppnas där du kan ange dina autentiseringsuppgifter för Azure-konto.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Hämta ID: N för prenumeration för din tillgängliga prenumerationer.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Ange rätt prenumerationen med prenumerations-ID. Ersätt  *<subscriptionID>*  med ID korrekt prenumeration.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Hämta storage-konto
Du behöver ett lagringskonto i Azure för att lagra överförda VM-avbildning. Du kan använda ett befintligt lagringskonto eller skapa en ny. 

Om du kommer att använda den virtuella Hårddisken för att skapa en hanterad disk för en virtuell dator, lagringsplats för kontot måste vara samma plats där du skapar den virtuella datorn.

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

    Så här skapar du en resursgrupp med namnet **myResourceGroup** i den **östra USA** region, typ:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "East US"
    ```

2. Skapa ett lagringskonto med namnet **mittlagringskonto** i den här resursgruppen med hjälp av den [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) cmdlet:
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "East US"`
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Giltiga värden för - SkuName är:
   
   * **Standard_LRS** -lokalt redundant lagring. 
   * **Standard_ZRS** -zonen redundant lagring.
   * **Standard_GRS** -Geo-redundant lagring. 
   * **Standard_RAGRS** -geo-redundant lagring med läsbehörighet. 
   * **Premium_LRS** -Premium lokalt redundant lagring. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Överför den virtuella Hårddisken till ditt lagringskonto

Använd den [Lägg till AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) för att ladda upp den virtuella Hårddisken till en behållare i ditt lagringskonto. Det här exemplet överför filen *myVHD.vhd* från *”C:\Users\Public\Documents\Virtual hårddiskar\"*  till ett lagringskonto med namnet *mittlagringskonto* i den *myResourceGroup* resursgruppen. Filen placeras i behållare med namnet *minbehållare* och det nya namnet kommer att *myUploadedVHD.vhd*.

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

Det här kommandot kan ta en stund att slutföra beroende på nätverksanslutningen och storleken på VHD-filen

Spara den **mål-URI** sökväg som ska användas senare om du ska skapa en hanterad disk eller en ny virtuell dator med hjälp av den överförda virtuella Hårddisken.

### <a name="other-options-for-uploading-a-vhd"></a>Andra alternativ för att överföra en virtuell Hårddisk
 
 
Du kan också ladda upp en virtuell Hårddisk till ditt lagringskonto med hjälp av något av följande:

- [AzCopy](http://aka.ms/downloadazcopy)
- [Azure Storage kopiera Blob API](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Azure Storage Explorer överför Blobbar](https://azurestorageexplorer.codeplex.com/)
- [Storage Import/Export Service REST API-referens](https://msdn.microsoft.com/library/dn529096.aspx)
-   Du rekommenderas att använda Import/Export Service om överföring tid är längre än 7 dagar. Du kan använda [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) att uppskatta storlek och överför dataenheten tidpunkt. 
    Import/Export kan användas för att kopiera till ett standardlagringskonto. Du måste kopiera från standardlagring till premium storage-konto med ett verktyg som AzCopy.


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Skapa en hanterad avbildning från den överförda virtuella Hårddisken 

Skapa en hanterad avbildning med hjälp av din generaliserad OS-VHD. Ersätt värdena med din egen information.


1.  Innan du kan definiera de gemensamma parametrarna:

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    $vmSize = "Standard_DS1_v2"
    $location = "East US" 
    $imageName = "yourImageName"
    ```

4.  Skapa avbildningen med din generaliserad OS-VHD.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $urlOfUploadedImageVhd
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa vNet och undernät för den [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

1. Skapa undernätet. Det här exemplet skapar ett undernät med namnet *mySubnet* med adressprefixet för *10.0.0.0/24*.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Skapa det virtuella nätverket. Det här exemplet skapar ett virtuellt nätverk med namnet *myVnet* med adressprefixet för *10.0.0.0/16*.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Skapa en offentlig IP-adress och gränssnitt

För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa en offentlig IP-adress. Det här exemplet skapas en offentlig IP-adress med namnet *myPip*. 
   
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

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Skapa säkerhetsgrupp för nätverk och en RDP-regel

Du måste ha en nätverkssäkerhetsregeln (NSG) som gör att RDP-åtkomst på port 3389 för att kunna logga in på den virtuella datorn med RDP. 

Det här exemplet skapas en NSG som heter *myNsg* som innehåller en regel med namnet *myRdpRule* som tillåter RDP-trafik via port 3389. Mer information om NSG: er finns [öppna portar till en virtuell dator i Azure med hjälp av PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Skapa en variabel för det virtuella nätverket

Skapa en variabel för det virtuella nätverket som slutförda. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Hämta autentiseringsuppgifterna för den virtuella datorn

Följande cmdlet öppnas ett fönster där du ska ange ett nytt användarnamn och lösenord ska användas som ett lokalt administratörskonto för att få fjärråtkomst till den virtuella datorn. 

```powershell
$cred = Get-Credential
```

## <a name="add-the-vm-name-and-size-to-the-vm-configuration"></a>Lägg till VM-namnet och storleken i VM-konfigurationen.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Ange VM-avbildning som källbilden för den nya virtuella datorn

Ange källa avbildningen med ID: T för den hanterade VM-avbildningen.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Ange Operativsystemets konfiguration och Lägg till nätverkskortet.

Ange lagringstypen av (PremiumLRS eller StandardLRS) och storleken på operativsystemdisken. Det här exemplet anges kontotypen *PremiumLRS*, diskens storlek till *128 GB* och disk caching *ReadWrite*.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den nya virtuella datorn med hjälp av konfigurationen som lagrats i den **$vm** variabeln.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Kontrollera att den virtuella datorn har skapats
När du är klar bör du se den nya virtuella datorn i den [Azure-portalen](https://portal.azure.com) under **Bläddra** > **virtuella datorer**, eller genom att använda följande PowerShell-kommandon:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Nästa steg

Om du vill logga in till din nya virtuella datorn, bläddra till den virtuella datorn i den [portal](https://portal.azure.com), klickar du på **Anslut**, öppna fjärrskrivbord RDP-filen. Använda kontouppgifter i den ursprungliga virtuella datorn för att logga in på den nya virtuella datorn. Mer information finns i [ansluta och logga in på en virtuell Azure-dator kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

