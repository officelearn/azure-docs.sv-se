<properties
    pageTitle="Skapa en virtuell dator i Azure med hjälp av PowerShell | Microsoft Azure"
    description="Skapa enkelt en ny virtuell dator som kör Windows Server med hjälp av Azure PowerShell och Azure Resource Manager."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/07/2016"
    ms.author="davidmu"/>


# Skapa en virtuell Windows-dator med hjälp av Resource Manager och PowerShell

I den här artikeln lär du dig hur du snabbt kan skapa en virtuell Azure-dator som kör Windows Server, och om de resurser som den behöver, med hjälp av [Resource Manager](../resource-group-overview.md) och PowerShell. 

Alla stegen i den här artikeln krävs för att skapa en virtuell dator. Det bör ta runt 30 minuter att utföra stegen.

## Steg 1: Installera Azure PowerShell

Information om hur du installerar den senaste versionen av Azure PowerShell, hur du väljer prenumeration och hur du loggar in på ditt Azure-konto finns i [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md)
        
## Steg 2: Skapa en resursgrupp

Först måste du skapa en resursgrupp.

1. Hämta en lista över tillgängliga platser där resurser kan skapas.

        Get-AzureRmLocation | sort Location | Select Location
        
    Du bör se något liknande följande:
    
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus

2. Ersätt värdet för **$locName** med en plats från listan. Skapa variabeln.

        $locName = "centralus"
        
3. Ersätt värdet för **$rgName** med ett namn för den nya resursgruppen. Skapa variabeln och resursgruppen.

        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Steg 3: Skapa ett lagringskonto

Ett [lagringskonto](../storage/storage-introduction.md) behövs för att lagra den virtuella hårddisken som används av den virtuella datorn som du skapar.

1. Ersätt värdet för **$stName** med ett namn för lagringskontot. Testa namnet för att se om det är unikt.

        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName

    Om det här kommandot returnerar **True** är ditt föreslagna namn unikt i Azure. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
    
2. Kör nu kommando för att skapa lagringskontot.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName
        
## Steg 4: Skapa ett virtuellt nätverk

Alla virtuella datorer ingår i ett [virtuellt nätverk](../virtual-network/virtual-networks-overview.md).

1. Ersätt värdet för **$subnetName** med ett namn för undernätet. Skapa variabeln och undernätet.
        
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Ersätt värdet för **$vnetName** med ett namn för det virtuella nätverket. Skapa variabeln och det virtuella nätverket med undernätet.

        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
    Använd värden som passar ditt program och din miljö.
        
## Steg 5: Skapa en offentlig IP-adress och ett nätverksgränssnitt

För att upprätta kommunikation med den virtuella datorn i det virtuella nätverket behöver du en [offentlig IP-adress](../virtual-network/virtual-network-ip-addresses-overview-arm.md) och ett nätverksgränssnitt.

1. Ersätt värdet för **$ipName** med ett namn för den offentliga IP-adressen. Skapa variabeln och den offentliga IP-adressen.

        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Ersätt värdet för **$nicName** med ett namn för nätverksgränssnittet. Skapa variabeln och nätverksgränssnittet.

        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Steg 6: Skapa en virtuell dator

Nu när alla delar är på plats är det dags att skapa den virtuella datorn.

1. Kör kommandot för att definiera namnet på administratörskontot och lösenordet för den virtuella datorn.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    Lösenordet måste bestå av mellan 8 och 123 tecken och uppfylla tre av fyra komplexitetskrav: en gemen, en versal, en siffra och ett specialtecken. Läs mer om [krav för användarnamn och lösenord](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).
        
2. Ersätt värdet för **$vmName** med ett namn för den virtuella datorn. Skapa variabeln och konfigurationen av den virtuella datorn.

        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    En lista med tillgängliga storlekar för virtuella datorer finns i [Storlekar för virtuella datorer i Azure](virtual-machines-windows-sizes.md).
    
3. Ersätt värdet för **$compName** med ett datornamn för den virtuella datorn. Skapa variabeln och lägg till information om operativsystemet i konfigurationen.

        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Definiera avbildningen som ska användas för att etablera den virtuella datorn. 

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Mer information om hur du väljer de avbildningar finns i [Navigera och välja avbildningar för virtuella Windows-datorer i Azure med PowerShell eller CLI](virtual-machines-windows-cli-ps-findimage.md).
        
5. Lägg till nätverksgränssnittet som du skapade i konfigurationen.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Ersätt värdet för **$blobPath** med en sökväg och ett filnamn i lagringssystemet som den virtuella hårddisken ska använda. Den virtuella hårddisken lagras vanligtvis i en behållare, till exempel **vhds/WindowsVMosDisk.vhd**. Skapa variablerna.

        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Ersätt värdet för **$diskName** med ett namn för disken med operativsystemet. Skapa variabeln och lägg till diskinformationen i konfigurationen.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Skapa slutligen den virtuella datorn.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Resursgruppen och alla dess resurser bör visas på Azure Portal och en text om lyckad status bör visas i PowerShell-fönstret:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Nästa steg

- Om det uppstod problem med distributionen läser du [Felsöka distributionen av resursgrupper med Azure Portal](../resource-manager-troubleshoot-deployments-portal.md)
- Lär dig hur du hanterar den virtuella dator som du nyss har skapat genom att läsa [Hantera virtuella datorer med hjälp av Azure Resource Manager och PowerShell](virtual-machines-windows-ps-manage.md).
- Utnyttja fördelarna med att använda en mall för att skapa en virtuell dator. Mer information finns i [Skapa en virtuell Windows-dator med en Resource Manager-mall](virtual-machines-windows-ps-template.md)



<!--HONumber=Sep16_HO3-->


