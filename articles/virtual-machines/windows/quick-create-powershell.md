---
title: "Azure snabbstart – skapa virtuell Windows-dator med PowerShell | Microsoft Docs"
description: "Lär dig att skapa en virtuell Windows-dator med PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0299e0b5a756bc0a13904bf3fda673ace7581795
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell"></a>Skapa en virtuell Windows-dator med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten beskriver hur man använder PowerShell för att skapa en virtuell Azure-dator som kör Windows Server 2016. När distributionen är klar kan vi ansluta till servern och installera IIS.  

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.


## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-networking-resources"></a>Skapa nätverksresurser

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Skapa ett virtuellt nätverk, undernät och offentlig IP-adress. 
Dessa resurser används för att skapa nätverksanslutning till den virtuella datorn och ansluta den till internet.

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location EastUS `
    -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location EastUS `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Skapa en nätverkssäkerhetsgrupp och en regel för nätverkssäkerhetsgrupp. 
Nätverkssäkerhetsgruppen skyddar den virtuella datorn med hjälp av regler för inkommande och utgående trafik. I detta fall skapas en regel för inkommande trafik för port 3389, som tillåter inkommande anslutningar till fjärrskrivbord. Vi vill också skapa en regel för inkommande trafik för port 80, som tillåter inkommande webbtrafik.

```azurepowershell-interactive
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location EastUS `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Skapa ett nätverkskort för den virtuella datorn. 
Skapa ett nätverkskort med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) för den virtuella datorn. Nätverkskortet ansluter den virtuella datorn till ett undernät, en nätverkssäkerhetsgrupp och offentlig IP-adress.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell datorkonfiguration. Den här konfigurationen innehåller de inställningar som används vid distribution av den virtuella datorn, t.ex. den virtuella datorns avbildning, storlek och konfiguration för verifiering. När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

```azurepowershell-interactive
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Skapa den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVM -ResourceGroupName myResourceGroup -Location EastUS -VM $vmConfig
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

När distributionen har slutförts kan du skapa en fjärrskrivbordsanslutning med den virtuella datorn.

Använd kommandot [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) för att returnera den offentliga IP-adressen för den virtuella datorn. Anteckna denna IP-adress så att du kan ansluta till den till din webbläsare för att testa webbanslutningen i ett framtida steg.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Använd följande kommando på den lokala datorn för att skapa en fjärrskrivbordssession med den virtuella datorn. Ersätt IP-adressen med *publicIPAddress* för den virtuella datorn. När du uppmanas att göra det anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn.

```bash 
mstsc /v:<publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Installera IIS via PowerShell

Nu när du har loggat in till den virtuella Azure-datorn kan du använda en rad med PowerShell för att installera IIS och aktivera den lokala brandväggsregeln så att del tillåter webbtrafik. Öppna en PowerShell-kommandotolk och kör följande kommando:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Visa välkomstsidan för IIS

Du kan använda en webbläsare som du själv väljer för att visa standardvälkomstsidan för IIS när IIS är installerat och port 80 nu är öppen på den virtuella datorn från Internet. Se till att använda den *publicIpAddress* som du har dokumenterat ovan för att besöka standardsidan. 

![Standardwebbplatsen i IIS](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en virtuell dator och en regel för nätverkssäkerhetsgrupp samt installerat en webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Windows-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Windows-datorer](./tutorial-manage-vm.md)
