---
title: Filtrera nätverkstrafik – Azure PowerShell | Microsoft Docs
description: I den här artikeln får du lära dig hur du filtrerar nätverkstrafik till ett undernät med en nätverkssäkerhetsgrupp med hjälp av PowerShell.
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
ms.custom: mvc
ms.openlocfilehash: dfeff34de882602711ed375d81977ae501ec51cf
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428415"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrera nätverkstrafik med en nätverkssäkerhetsgrupp med hjälp av PowerShell

Du kan filtrera inkommande och utgående nätverkstrafik till och från ett undernät i ett virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehåller säkerhetsregler som filtrerar nätverkstrafik efter IP-adress, port och protokoll. Säkerhetsregler tillämpas på resurser som har distribuerats i ett undernät. I den här artikeln kan du se hur du:

* Skapa en nätverkssäkerhetsgrupp och säkerhetsregler
* Skapa ett virtuellt nätverk och associera en nätverkssäkerhetsgrupp med ett undernät
* Distribuera virtuella datorer (VM) i ett undernät
* Testa trafikfilter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt, i den här artikeln kräver Azure PowerShell-Modulversion 6.2.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/azurerm/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp innehåller säkerhetsregler. Säkerhetsregler anger en källa och ett mål. Källor och mål kan vara programsäkerhetsgrupper.

### <a name="create-application-security-groups"></a>Skapa programsäkerhetsgrupper

Skapa först en resursgrupp för alla resurser som skapats i den här artikeln med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp på platsen *eastus*: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa en programsäkerhetsgrupp med [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). En programsäkerhetsgrupp gör att du kan gruppera servrar med liknande portfiltreringskrav. I följande exempel skapas två programsäkerhetsgrupper.

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

Skapa en säkerhetsregel med [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). I följande exempel skapas en regel som tillåter inkommande trafik från Internet till programsäkerhetsgruppen *myWebServers* via port 80 och 443:

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

I den här artikeln exponeras RDP (port 3389) mot internet för den *myAsgMgmtServers* VM. För produktionsmiljöer rekommenderas, i stället för att exponera port 3389 mot Internet, att du ansluter till Azure-resurser som du vill hantera med hjälp av en [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-anslutning eller [privat](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nätverksanslutning.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa en undernätskonfiguration med [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) och skriv sedan undernätskonfigurationen till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). I följande exempel läggs ett undernät med namnet *mySubnet* till i det virtuella nätverket och nätverkssäkerhetsgruppen *myNsg* associeras med det:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Innan du skapar de virtuella datorerna hämtar du det virtuella nätverksobjektet med undernätet med [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Skapa en offentlig IP-adress för varje virtuell dator med [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Skapa två nätverksgränssnitt med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) och tilldela en offentlig IP-adress till nätverksgränssnittet. I följande exempel skapas ett nätverksgränssnitt, den offentliga IP-adressen för *myVmWeb* associeras med det och det görs till medlem i programsäkerhetsgruppen *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

I följande exempel skapas ett nätverksgränssnitt, den offentliga IP-adressen för *myVmMgmt* associeras med det och det görs till medlem i programsäkerhetsgruppen *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera trafikfiltrering i ett senare steg. 

Skapa en VM-konfiguration med [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) och skapa sedan den virtuella datorn med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator som fungerar som en webbserver. Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg: 

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

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med nästa steg förrän Azure har skapat den virtuella datorn.

## <a name="test-traffic-filters"></a>Testa trafikfilter

Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den virtuella datorn *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbordssession med den virtuella datorn *myVmMgmt* från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returnerades från föregående kommando.

```
mstsc /v:<publicIpAddress>
```

Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det.

Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen. 
   
Anslutningen lyckas, eftersom port 3389 tillåts inkommande från Internet till programsäkerhetsgruppen *myAsgMgmtServers* där nätverksgränssnittet som är kopplat till den virtuella datorn *myVmMgmt* ingår.

Använd följande kommando för att skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVmWeb*, från den virtuella datorn *myVmMgmt*, med följande kommando, från PowerShell:

``` 
mstsc /v:myvmWeb
```

Anslutningen lyckas eftersom en standardsäkerhetsregel i varje nätverkssäkerhetsgrupp tillåter trafik via alla portar mellan alla IP-adresser i ett virtuellt nätverk. Det går inte att skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVmWeb* från Internet eftersom säkerhetsregeln för *myAsgWebServers* inte tillåter port 3389 inkommande från Internet.

Använd följande kommando för att installera Microsoft IIS på den virtuella datorn *myVmWeb* från PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När IIS-installationen är klar kopplar du från den virtuella datorn *myVmWeb*, vilket lämnar dig i fjärrskrivbordsanslutningen med den virtuella datorn *myVmMgmt*. Visa IIS-välkomstskärmen genom att öppna en webbläsare och gå till http://myVmWeb.

Koppla från den virtuella datorn *myVmMgmt*.

På datorn anger du följande kommando från PowerShell för att hämta den offentliga IP-adressen för servern *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Bekräfta att du har åtkomst till webbservern *myVmWeb* utanför Azure genom att öppna en webbläsare på datorn och gå till `http://<public-ip-address-from-previous-step>`. Anslutningen lyckas, eftersom port 80 tillåts inkommande från Internet till programsäkerhetsgruppen *myAsgWebServers* där nätverksgränssnittet som är kopplat till den virtuella datorn *myVmWeb* ingår.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när de inte längre behövs:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du skapade en nätverkssäkerhetsgrupp och associerat den till ett virtuellt nätverksundernät. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](security-overview.md) och [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md).

Azure dirigerar som standard trafik mellan undernät. Du kan i stället välja att exempelvis dirigera trafik mellan undernät via en virtuell dator, som fungerar som en brandvägg. Läs hur genom att läsa [skapar en routningstabell](tutorial-create-route-table-powershell.md).
