---
title: "Dirigera nätverkstrafik - PowerShell | Microsoft Docs"
description: "Lär dig mer om att dirigera nätverkstrafik till en routingtabell med hjälp av PowerShell."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Dirigera nätverkstrafik till en routingtabell med hjälp av PowerShell

Azure automatiskt vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan skapa egna flöden om du vill åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbart om du till exempel du vill vidarebefordra trafik mellan undernät via en brandvägg. I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa en routingtabell
> * Skapa en väg
> * Associera en routingtabell till ett undernät för virtuellt nätverk
> * Testa Routning
> * Felsöka routning

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt i den här artikeln kräver Azure PowerShell Modulversion 5.4.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="create-a-route-table"></a>Skapa en routingtabell

Azure vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Läs mer om Azures standardvägar i [systemvägar](virtual-networks-udr-overview.md). Om du vill åsidosätta standardvärdet för Azures routning, skapa en routingtabell som innehåller vägar och associera routningstabellen till ett undernät för virtuellt nätverk.

Innan du kan skapa en routingtabell, skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* för alla resurser som har skapats i den här artikeln. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa en routingtabell med [ny AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). I följande exempel skapas en routingtabell som heter *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Skapa en väg

En routningstabell innehåller noll eller flera vägar. Skapa en väg genom att hämta objektet väg tabell med [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), skapa en väg med [Lägg till AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig), sedan skriva konfiguration väg i routningstabellen med [Set AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

Vägen kommer att dirigera all trafik till adressprefixet 10.0.1.0/24 via en virtuell nätverksenhet med IP-adressen 10.0.2.4. Virtuell nätverksenhet och undernät med det angivna adressprefixet skapas i senare steg. Vägen åsidosätter Azures standard routning, som dirigerar trafik mellan undernät direkt. Varje väg anger nästa hopptyp. Nästa hopptyp instruerar Azure så att dirigera trafiken. I det här exemplet nästa hopptyp är *VirtualAppliance*. Läs mer om alla tillgängliga nästa hopptyper i Azure och när de ska användas i [typer för nästa hopp](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routingtabell till ett undernät

Innan du kan koppla en routingtabell till ett undernät som du behöver skapa ett virtuellt nätverk och undernät. Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa tre undernät genom att skapa tre konfigurationer för undernät med [ny AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). I följande exempel skapas tre konfigurationer för undernät för *offentliga*, *privata*, och *DMZ* undernät:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Adressprefixen måste ligga inom adressprefixet som definierats för det virtuella nätverket. Adressprefix undernät kan inte överlappa varandra.

Skriva undernät konfigurationer till det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), vilket skapar undernäten i det virtuella nätverket:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

Du kan associera en routingtabell till noll eller flera undernät. Ett undernät kan ha noll eller en vägtabell associerad till den. Utgående trafik från ett undernät vidarebefordras baserat på Azures standardvägar och eventuella anpassade vägar som du har lagt till en routingtabell som du kopplar till ett undernät. Koppla den *myRouteTablePublic* routningstabellen till den *offentliga* undernät med [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) och sedan skriva undernätskonfiguration till den virtuellt nätverk med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Innan du distribuerar routningstabeller för produktion, rekommenderas att du noggrant bekanta dig med [routning i Azure](virtual-networks-udr-overview.md) och [Azure gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testa Routning

Om du vill testa routning, ska du skapa en virtuell dator som fungerar som virtuell nätverksenhet att flödet som du skapade i föregående steg vägar via. När du har skapat virtuell nätverksenhet du ska distribuera en virtuell dator i den *offentliga* och *privata* undernät. Du kommer sedan att dirigera trafik från den *offentliga* undernät så att den *privata* undernätet via virtuell nätverksenhet.

### <a name="create-a-network-virtual-appliance"></a>Skapa en virtuell nätverksenhet

En virtuell dator som kör ett nätverksprogram kallas ofta för en virtuell nätverksenhet. Virtuella installationer i nätverket tar emot nätverkstrafik, utföra någon åtgärd sedan vidarebefordra vanligtvis eller släppa nätverkstrafik baserat på logik som konfigurerats i nätverksprogrammet. 

#### <a name="create-a-network-interface"></a>Skapa ett nätverksgränssnitt

I föregående steg skapas en väg som angetts av en virtuell nätverksenhet som nexthop-typen. En virtuell dator som kör ett nätverksprogram kallas ofta för en virtuell nätverksenhet. Den virtuella nätverksenhet som du distribuerar är ofta en förkonfigurerad virtuell dator i produktionsmiljöer. Flera virtuella nätverksinstallationer är tillgängliga från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). En enkel virtuell dator skapas i den här artikeln.

En virtuell dator har en eller flera nätverksgränssnitt kopplat till den, som gör att den virtuella datorn kan kommunicera med nätverket. För ett nätverksgränssnitt för att kunna vidarebefordra trafik skickas till den, som inte är avsett för sin egen IP-adressen, måste IP-vidarebefordran aktiveras för nätverksgränssnittet. Innan du skapar ett nätverksgränssnitt kan du behöva hämta den virtuella nätverks-Id med [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork), sedan Id för undernätet med [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Skapa ett nätverksgränssnitt med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) i den *DMZ* undernät med IP-vidarebefordring är aktiverat:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

#### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator och bifoga en befintlig nätverksgränssnitt, måste du först skapa en konfiguration av virtuell dator med [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). Konfigurationen innehåller nätverksgränssnittet som du skapade i föregående steg. När du tillfrågas om användarnamn och lösenord, Välj användarnamn och lösenord som du vill logga in på den virtuella datorn med. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Skapa den virtuella datorn med konfigurationen av virtuella datorn med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

Den `-AsJob` alternativet skapar den virtuella datorn i bakgrunden så du kan fortsätta till nästa steg. När du uppmanas, anger du användarnamn och lösenord som du vill logga in på den virtuella datorn med. Den virtuella nätverksenhet som du distribuerar är ofta en förkonfigurerad virtuell dator i produktionsmiljöer. Flera virtuella nätverksinstallationer är tillgängliga från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Azure utsetts 10.0.2.4 privata IP-adressen för den virtuella datorn, eftersom 10.0.2.4 är den första tillgängliga IP-adressen i den *DMZ* undernätet för *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera att trafik från den *offentliga* undernät dirigeras till den *privata* undernätet via virtuell nätverksenhet i ett senare steg. 

Skapa en virtuell dator i den *offentliga* undernät med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas en virtuell dator med namnet *myVmWeb* i den *offentliga* undernätet för den *myVirtualNetwork* virtuellt nätverk. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

Azure utsetts 10.0.0.4 privata IP-adressen för den virtuella datorn, eftersom 10.0.1.4 är den första tillgängliga IP-adressen i den *offentliga* undernätet för *myVirtualNetwork*.

Skapa en virtuell dator i den *privata* undernät.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

Det tar några minuter att skapa den virtuella datorn. Azure utsetts 10.0.1.4 privata IP-adressen för den virtuella datorn, eftersom 10.0.1.4 är den första tillgängliga IP-adressen i den *privata* undernätet för *myVirtualNetwork*. 

Inte fortsätta med nästa steg förrän den virtuella datorn skapas och Azure returnerar utdata till PowerShell.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Vidarebefordra trafik via en virtuell nätverksenhet

Använd [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) att aktivera ICMP inkommande till den *myVmWeb* och *myVmMgmt* virtuella datorer via Windows-brandväggen för att testa med tracert kommunikation mellan de virtuella datorerna i ett senare steg:

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

Kommandona kan ta några minuter att slutföra. Fortsätt inte med nästa steg tills kommandona Slutför och resultatet returneras till PowerShell. Även om tracert används för att testa routning i den här artikeln, rekommenderas inte att tillåta ICMP via Windows-brandväggen för Produktionsdistribution.

Du ansluter till en virtuell dators offentliga IP-adress från Internet. Använd [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den *myVmMgmt* virtuell dator:

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

En Remote Desktop Protocol (RDP)-fil skapas, hämtas till datorn och öppnas. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapa den virtuella datorn) och välj sedan **OK**. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen. 

Du har aktiverat IP-vidarebefordran i Azure för den virtuella datorns nätverksgränssnitt i [aktivera IP-fowarding](#enable-ip-forwarding). I den virtuella datorn, måste operativsystemet eller ett program som körs på den virtuella datorn också kunna vidarebefordra nätverkstrafik. När du distribuerar en virtuell nätverksenhet i en produktionsmiljö anordningen vanligtvis filtrerar, loggar eller utför vissa andra funktion innan den vidarebefordrar trafik. I den här artikeln dock vidarebefordrar operativsystemet all trafik som tas emot. Aktivera IP-vidarebefordring i operativsystemet på den *myVmNva*:

Från en kommandotolk på den *myVmMgmt* virtuella datorer, fjärrskrivbord till den *myVmNva* virtuell dator:

``` 
mstsc /v:myVmNva
```
    
Så här aktiverar du IP-vidarebefordring i operativsystemet på den *myVmNva* virtuell dator, anger du följande kommando i PowerShell den *myVmNva* virtuell dator:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Starta om den *myVmNva* virtuell dator som kommer också att koppla från fjärrskrivbords-sessionen, så att du inom fjärrskrivbordssession som du öppnade till den *myVmMgmt* virtuella datorn.

Efter den *myVmNva* omstarter av den virtuella datorn, använder du följande kommando för att testa routning av nätverkstrafik till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuella datorn.

```
tracert myvmweb
```

Svaret liknar följande exempel:

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Du kan se trafik vidarebefordras direkt från den *myVmMgmt* virtuella datorn till den *myVmWeb* virtuella datorn. Azures standardvägar, vidarebefordra trafik direkt mellan undernät.

Använd följande kommando för att fjärrskrivbord till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuella:

``` 
mstsc /v:myVmWeb
```

Att testa routning av nätverkstrafik till den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn, anger du följande kommando från en kommandotolk:

```
tracert myvmmgmt
```

Svaret liknar följande exempel:

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

Du kan se att det första hoppet är 10.0.2.4, vilket är den virtuella nätverksenhets privat IP-adress. Ett andra hopp är 10.0.1.4 privata IP-adressen för den *myVmMgmt* virtuella datorn. Vägen som lagts till i den *myRouteTablePublic* routningstabellen och som är associerade med den *offentliga* undernät orsakade Azure för att dirigera trafik via en NVA i stället för direkt till den *privata* undernät.

Stäng fjärrskrivbordssessioner för både den *myVmWeb* och *myVmMgmt* virtuella datorer.

## <a name="troubleshoot-routing"></a>Felsöka routning

Du har lärt dig i föregående steg gäller standardvägar som du kan alternativt kan du åsidosätta med egna vägar i Azure. Ibland kan trafik inte dirigeras som du tror att. Använd [ny AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) att aktivera en Nätverksbevakaren i EastUS region, om du inte redan har en Nätverksbevakaren i den regionen:

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Använd [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) att avgöra hur trafik dirigeras mellan två virtuella datorer. Till exempel följande kommando testar routning av nätverkstrafik från den *myVmWeb* (10.0.0.4) virtuella datorn till den *myVmMgmt* (10.0.1.4) virtuell dator:

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
Efter en kort vänta returneras följande utdata:

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

Utdata informerar dig om att nästa hopp-IP-adress för trafik från *myVmWeb* till *myVmMgmt* är 10.0.2.4 (den *myVmNva* virtuell dator), att nästa hopptyp är  *VirtualAppliance*, och att routningstabellen som orsakar routning är *myRouteTablePublic*.

Effektiva vägar för varje nätverksgränssnitt är en kombination av Azures standardvägar och alla vägar som du definierar. Att se alla vägar som gäller för ett nätverksgränssnitt i en virtuell dator med [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Till exempel för att visa effektiva vägarna för den *myVmWeb* nätverksgränssnitt i den *myVmWeb* virtuella datorn, anger du följande kommando:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Alla standardvägar och flödet som du lade till i föregående steg, returneras.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver använda [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) att ta bort resursgruppen och alla resurser som den innehåller.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapa en routingtabell och som är kopplad till ett undernät. Du har skapat en virtuell nätverksenhet som dirigeras trafiken från offentliga undernät till ett privat undernät. Resurser för vissa Azure PaaS-tjänster kan inte distribueras till ett virtuellt nätverk medan du kan distribuera många Azure-resurser inom ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten till resurser av vissa Azure PaaS-tjänster till trafik från ett undernät för virtuellt nätverk men. Gå vidare till nästa artikel för att lära dig att begränsa nätverksåtkomsten till Azure PaaS-resurser.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](virtual-network-service-endpoints-configure.md#azure-powershell)