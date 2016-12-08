---
title: "Skapa en virtuell dator i Azure med hjälp av PowerShell | Microsoft Docs"
description: "Skapa enkelt en ny virtuell dator som kör Windows Server med hjälp av Azure PowerShell och Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: edeee13457c1098eb1b44efaa97e9a84d29e88e7
ms.openlocfilehash: 12903dc79ac6349da9f4897cdb0db5cb62f67b22


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Skapa en virtuell Windows-dator med hjälp av Resource Manager och PowerShell
I den här artikeln lär du dig hur du snabbt kan skapa en virtuell Azure-dator som kör Windows Server, och om de resurser som den behöver, med hjälp av [Resource Manager](../azure-resource-manager/resource-group-overview.md) och PowerShell. 

Alla steg i den här artikeln behövs för att skapa en virtuell maskin och det tar cirka 30 minuter att utföra stegen. Ersätt exempelparametervärden i kommandona med namn som passar din miljö.

## <a name="step-1-install-azure-powershell"></a>Steg 1: Installera Azure PowerShell
Se [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md) för information om hur du installerar den senaste versionen av Azure PowerShell, väljer din prenumeration och loggar in på ditt konto.

## <a name="step-2-create-a-resource-group"></a>Steg 2: Skapa en resursgrupp
Alla resurser måste finnas i en resursgrupp, så låt oss skapa en först.  

1. Hämta en lista över tillgängliga platser där resurser kan skapas.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```
2. Ange platsen för resurserna. Det här kommandot anger platsen till **centralus**.
   
    ```powershell
    $location = "centralus"
    ```
3. Skapa en resursgrupp. Det här kommandot skapar en resursgrupp med namnet **myResourceGroup** på den plats som du anger.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-create-a-storage-account"></a>Steg 3: Skapa ett lagringskonto
Ett [lagringskonto](../storage/storage-introduction.md) behövs för att lagra den virtuella hårddisken som används av den virtuella datorn som du skapar. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.

1. Testa lagringskontonamnet för unikhet. Det här kommandot testar namnet **myStorageAccount**.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Om det här kommandot returnerar **True** är ditt föreslagna namn unikt i Azure. 
2. Nu är det dags att skapa lagringskontot.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>Steg 4: Skapa ett virtuellt nätverk
Alla virtuella datorer ingår i ett [virtuellt nätverk](../virtual-network/virtual-networks-overview.md).

1. Skapa ett undernät för det virtuella nätverket. Det här kommandot skapar ett undernät med namnet **mySubnet** med adressprefixet 10.0.0.0/24.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
2. Skapa nu det virtuella nätverket. Det här kommandot skapar ett virtuellt nätverk med namnet **myVnet** med det undernät som du skapade och adressprefixet **10.0.0.0/16**.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Steg 5: Skapa en offentlig IP-adress och ett nätverksgränssnitt
För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Skapa den offentliga IP-adressen. Det här kommandot skapar en offentlig IP-adress med namnet **myPublicIp** med allokeringsmetoden **Dynamisk**.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Skapa nätverksgränssnittet. Det här kommandot skapar ett nätverksgränssnitt med namnet **myNIC**.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>Steg 6: Skapa en virtuell dator
Nu när alla delar är på plats är det dags att skapa den virtuella datorn.

1. Kör kommandot för att definiera namnet på administratörskontot och lösenordet för den virtuella datorn.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    Lösenordet måste vara 12-123 tecken långt och innehålla minst en gemen, en versal, en siffra och ett specialtecken. 
2. Skapa konfigurationsobjektet för den virtuella datorn. Det här kommandot skapar ett konfigurationsobjekt med namnet **myVmConfig** som definierar namnet på den virtuella datorn och storleken på den virtuella datorn.
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    En lista med tillgängliga storlekar för virtuella datorer finns i [Storlekar för virtuella datorer i Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
3. Konfigurera inställningarna för operativsystemet för den virtuella datorn. Det här kommandot anger datornamn, typ av operativsystem och autentiseringsuppgifter för den virtuella datorn.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```
4. Definiera avbildningen som ska användas för att etablera den virtuella datorn. Det här kommandot definierar Windows Server-avbildningen som ska användas för den virtuella datorn. 
   
    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
   
    Mer information hur du väljer vilken avbildning som ska användas finns i [Navigera och välja virtuella Windows-avbildningar i Azure med PowerShell eller CLI](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Lägg till nätverksgränssnittet som du skapade i konfigurationen.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. Definiera namn och plats för den virtuella hårddisken. Den virtuella hårddiskfilen lagras i en behållare. Det här kommandot skapar disken i en behållare med namnet **vhds/myOsDisk1.vhd** i lagringskontot som du skapade.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. Lägg till diskinformation om operativsystemet i VM-konfigurationen. Ersätt värdet för **$diskName** med ett namn för disken med operativsystemet. Skapa variabeln och lägg till diskinformationen i konfigurationen.
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. Skapa slutligen den virtuella datorn.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Nästa steg
* Om det var problem med distributionen, är nästa steg att titta på [Felsöka resursgruppdistribueringar med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)
* Läs [Hantera virtuella datorer med Azure Resource Manager och PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för att lära dig mer om hur du hanterar din virtuella dator.
* Utnyttja fördelarna med att använda en mall för att skapa en virtuell dator. Mer information finns i [Skapa en virtuell Windows-dator med en Resource Manager-mall](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO5-->


