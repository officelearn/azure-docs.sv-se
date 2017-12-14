---
title: "Skapa en Windows-dator med hjälp av PowerShell i Azure-stacken | Microsoft Docs"
description: Skapa en virtuell Windows-dator med PowerShell i Azure-stacken.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 7CA6C0AC-23B7-4007-BA32-7A950FD1F3B8
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 688ab6c55867d72d55e27c21c883c14ef90078d2
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-windows-virtual-machine-by-using-powershell-in-azure-stack"></a>Skapa en Windows-dator med hjälp av PowerShell i Azure-stacken

*Gäller för: Azure Stack integrerat system*

Den här guiden information som använder PowerShell för att skapa en virtuell Windows Server 2016-dator i Azure-stacken. Du kan köra stegen som beskrivs i den här artikeln från Azure Stack Development Kit eller från en extern Windows-baserad klient om du är ansluten via VPN. 

## <a name="prerequisites"></a>Krav 

* Kontrollera att Azure Stack-operator har lagt till ”Windows Server 2016” bilden Stack för Azure marketplace.  

* Azure-stacken kräver en viss version av Azure PowerShell för att skapa och hantera resurser. Om du inte har konfigurerats för stacken Azure PowerShell, följer du stegen för att [installera](azure-stack-powershell-install.md) och [konfigurera](azure-stack-powershell-configure-user.md) PowerShell.    

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk behållare i vilka Azure-stacken resurser distribueras och hanteras. Kör följande kodblock om du vill skapa en resursgrupp från din development kit eller Azure-stacken integrerat system. Vi har tilldelat värden för alla variabler i det här dokumentet kan du använda dem som är eller tilldela ett annat värde.  

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Skapa storage-resurser 

Skapa ett lagringskonto och en lagringsbehållare för lagring av Windows Server 2016-avbildningen.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Skapa nätverksresurser

Skapa ett virtuellt nätverk, undernät och offentlig IP-adress. Dessa resurser används för att ge nätverksanslutning till den virtuella datorn.  

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Skapa en nätverkssäkerhetsgrupp och en regel för nätverkssäkerhetsgrupp

Nätverkssäkerhetsgruppen skyddar den virtuella datorn med hjälp av regler för inkommande och utgående. Kan skapa en regel för inkommande trafik för port 3389 för att tillåta inkommande anslutningar till fjärrskrivbord och en inkommande regel för port 80 för att tillåta inkommande webbtrafik.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb 
```
 
### <a name="create-a-network-card-for-the-virtual-machine"></a>Skapa ett nätverkskort för den virtuella datorn

Nätverkskortet ansluter den virtuella datorn till ett undernät, en nätverkssäkerhetsgrupp och offentlig IP-adress.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id 
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell datorkonfiguration. Konfigurationen innehåller de inställningar som används när du distribuerar den virtuella datorn, till exempel en avbildning av virtuell dator, storlek, autentiseringsuppgifter.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize 

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential 

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine. 
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id 

#Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Fjärråtkomst till den virtuella datorn som du skapade i föregående steg, om du behöver den offentliga IP-adressen. Kör följande kommando för att hämta den offentliga IP-adressen för den virtuella datorn: 

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```
 
Använd följande kommando för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adress med publicIPAddress för den virtuella datorn. När du uppmanas, anger du användarnamn och lösenord som du använde när du skapar den virtuella datorn.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Installera IIS via PowerShell

Nu när du har loggat in till den virtuella Azure-datorn kan du använda en rad med PowerShell för att installera IIS och aktivera den lokala brandväggsregeln så att del tillåter webbtrafik. Öppna en PowerShell-kommandotolk och kör följande kommando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare som du själv väljer för att visa standardvälkomstsidan för IIS när IIS är installerat och port 80 nu är öppen på den virtuella datorn från Internet. Se till att använda den *publicIpAddress* som du har dokumenterat ovan för att besöka standardsidan. 

![Standardwebbplatsen i IIS](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png) 


## <a name="delete-the-virtual-machine"></a>Ta bort den virtuella datorn

När det inte längre behövs, Använd följande kommando för att ta bort resursgruppen som innehåller den virtuella datorn och dess relaterade resurser:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

Du har distribuerat en enkel Windows virtuell dator i den här snabbstarten. Om du vill veta mer om Azure-stacken virtuella datorer kan fortsätta att [överväganden för virtuella datorer i Azure-stacken](azure-stack-vm-considerations.md).

