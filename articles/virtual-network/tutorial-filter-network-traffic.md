---
title: Filtrera nätverkstrafik - Azure PowerShell | Microsoft Docs
description: I den här artikeln får lära du att filtrera trafik till ett undernät med en nätverkssäkerhetsgrupp med hjälp av PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 53406150cfc2ec4229ecd9cf3356ad03d60f8e7e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp med hjälp av PowerShell

Du kan filtrera trafik inkommande och utgående från ett undernät för virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehålla säkerhetsregler som filtrera nätverkstrafik av IP-adress, port och protokoll. Säkerhetsregler som tillämpas på resurser har distribuerats i ett undernät. I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> * Skapa en nätverkssäkerhet regler för gruppen och säkerhet
> * Skapa ett virtuellt nätverk och associera en säkerhetsgrupp för nätverk till ett undernät
> * Distribuera virtuella datorer (VM) i ett undernät
> * Testa trafikfilter

Om du vill kan du slutföra den här artikeln med hjälp av den [Azure CLI](tutorial-filter-network-traffic-cli.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver Azure PowerShell Modulversion 5.4.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp innehåller säkerhetsregler. Säkerhetsregler Ange källa och mål. Källor och mål kan vara program säkerhetsgrupper.

### <a name="create-application-security-groups"></a>Skapa säkerhetsgrupper för programmet

Skapa först en resursgrupp för alla resurser som skapats i denna artikel med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp i den *eastus* plats: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa en säkerhetsgrupp för program med [ny AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). En säkerhetsgrupp för programmet kan du till gruppen servrar med liknande portfiltrering krav. I följande exempel skapar två säkerhetsgrupper för programmet.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Skapa säkerhetsregler

Skapa en anslutningssäkerhetsregel med [ny AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). I följande exempel skapas en regel som tillåter trafik inkommande trafik från internet till den *myWebServers* programgruppen för säkerhet via portarna 80 och 443:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

I den här artikeln RDP (port 3389) är exponerad mot internet för den *myAsgMgmtServers* VM. För produktionsmiljöer, i stället för att exponera port 3389 till internet, rekommenderas att du ansluter till Azure-resurser som du vill hantera med hjälp av en [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [privata](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nätverksanslutning.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [ny AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas en virtuell med namnet *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa en konfiguration av undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig), och sedan skriva konfiguration av undernät till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). I följande exempel läggs ett undernät med namnet *mySubnet* till virtuella nätverk och associerar den *myNsg* nätverkssäkerhetsgruppen till den:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Innan du skapar virtuella datorer att hämta objektet virtuellt nätverk med undernät med [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Skapa en offentlig IP-adress för varje virtuell dator med [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` - ResourceGroupName myResourceGroup `
  -Location eastus ` -namnet myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` - ResourceGroupName myResourceGroup `
  -Location eastus ` -namnet myVmMgmt


Skapa två nätverksgränssnitt med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface), och tilldela en offentlig IP-adress till nätverksgränssnittet. I följande exempel skapas ett nätverksgränssnitt, associerar den *myVmWeb* offentlig IP-adress, och gör den till en medlem i den *myAsgWebServers* säkerhetsgrupp för programmet:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

I följande exempel skapas ett nätverksgränssnitt, associerar den *myVmMgmt* offentlig IP-adress, och gör den till en medlem i den *myAsgMgmtServers* säkerhetsgrupp för programmet:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera trafik filtrering i ett senare steg. 

Skapa en VM-konfiguration med [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), skapa den virtuella datorn med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator som fungerar som en webbserver. Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg: 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Skapa en virtuell dator som fungerar som en hanteringsserver:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Det tar några minuter att skapa den virtuella datorn. Inte fortsätta med nästa steg förrän Azure har skapat den virtuella datorn.

## <a name="test-traffic-filters"></a>Testa trafikfilter

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att returnera offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den *myVmMgmt* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbords-session med den *myVmMgmt* virtuell dator från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returneras från det föregående kommandot.

```
mstsc /v:<publicIpAddress>
```

Öppna den hämta RDP-filen. Välj **Anslut**.

Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn), Välj sedan **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** att fortsätta med anslutningen. 
   
Anslutningen lyckas, eftersom port 3389 tillåts inkommande från internet till den *myAsgMgmtServers* programmet säkerhetsgrupp som nätverksgränssnittet som är kopplade till den *myVmMgmt* virtuella datorn.

Använd följande kommando för att skapa en fjärrskrivbordsanslutning till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuell dator med följande kommando från PowerShell:

``` 
mstsc /v:myvmWeb
```

Anslutningen eftersom en standardsäkerhetsregel inom varje nätverkssäkerhetsgruppen tillåter trafik över alla portar mellan alla IP-adresser inom ett virtuellt nätverk. Du kan inte skapa en fjärrskrivbordsanslutning till den *myVmWeb* VM från internet eftersom säkerheten regel för den *myAsgWebServers* tillåter inte att port 3389 inkommande från internet.

Använd följande kommando för att installera Microsoft IIS på den *myVmWeb* VM från PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När IIS-installationen är klar, koppla från den *myVmWeb* VM, vilket lämnar du i den *myVmMgmt* VM anslutning till fjärrskrivbord. Att visa välkomstskärmen IIS, öppna en webbläsare och gå till http://myVmWeb.

Koppla från den *myVmMgmt* VM.

På datorn, anger du följande kommando från PowerShell för att hämta den offentliga IP-adressen för den *myVmWeb* server:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Bekräfta att du har åtkomst till den *myVmWeb* webbserver från utanför Azure, öppna en webbläsare på datorn och bläddra till `http://<public-ip-address-from-previous-step>`. Anslutningen lyckas, eftersom port 80 tillåts inkommande från internet till den *myAsgWebServers* programmet säkerhetsgrupp som nätverksgränssnittet som är kopplade till den *myVmWeb* virtuella datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapas en nätverkssäkerhetsgrupp och som är kopplad till ett undernät för virtuellt nätverk. Läs mer om nätverkssäkerhetsgrupper i [nätverk Säkerhetsöversikt grupp](security-overview.md) och [hantera en nätverkssäkerhetsgrupp](virtual-network-manage-nsg-arm-ps.md).

Azure vägar trafik mellan undernät som standard. Du kan i stället välja att vidarebefordra trafik mellan undernät via en virtuell dator som fungerar som en brandvägg, till exempel. Gå vidare till nästa artikeln om du vill veta hur du skapar en routningstabell.

> [!div class="nextstepaction"]
> [Skapa en routingtabell](./tutorial-create-route-table-portal.md)