---
title: "Belastningsutjämna virtuella Windows-datorer i Azure | Microsoft Docs"
description: "Lär dig hur du använder Azure Load Balancer för att skapa hög tillgänglighet och säkerhet för program över tre virtuella Windows-datorer"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f0e154d0ac917d2ef2799431a72969a96415e0c0
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Belastningsutjämna virtuella Windows-datorer i Azure för att skapa ett program med hög tillgänglighet
Med belastningsutjämning får du högre tillgänglighet genom att inkommande begäranden sprids över flera virtuella datorer. I den här kursen får du lära dig mer om de olika komponenterna i Azure Load Balancer som distribuerar trafik och ger hög tillgänglighet. Lär dig att:

> [!div class="checklist"]
> * Skapa en Azure Load Balancer
> * Skapa en hälsoavsökning för belastningsutjämnaren
> * Skapa trafikregler för belastningsutjämnaren
> * Skapa en grundläggande IIS-webbplats med tillägget för anpassat skript
> * Skapa virtuella datorer och anslut dem till en belastningsutjämnare
> * Visa en belastningsutjämnare i praktiken
> * Lägga till och ta bort virtuella datorer från en belastningsutjämnare

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 5.3 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 


## <a name="azure-load-balancer-overview"></a>Översikt över Azure Load Balancer
En Azure-belastningsutjämnare är en Layer-4-belastningsutjämnare (TCP, UDP) som ger hög tillgänglighet genom att distribuera inkommande trafik till felfria virtuella datorer. Belastningsutjämnaren har en hälsoavsökningsfunktion som övervakar en given port på varje virtuell dator och ser till att trafik endast distribueras till virtuella datorer som fungerar.

Du definierar en IP-konfiguration på klientdelen som innehåller en eller flera offentliga IP-adresser. IP-konfigurationen på klientdelen gör att det går att komma åt belastningsutjämnaren och program via Internet. 

Virtuella datorer ansluter till en belastningsutjämnare med ett virtuellt nätverkskort. För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren.

För att styra trafikflödet definierar du regler för belastningsutjämnaren för specifika portar och protokoll som mappar till dina virtuella datorer.


## <a name="create-azure-load-balancer"></a>Skapa en Azure Load Balancer
I det här avsnittet beskrivs hur du skapar och konfigurerar varje komponent i belastningsutjämnaren. Innan du kan skapa belastningsutjämnaren måste du skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupLoadBalancer* på platsen *EastUS* (Östra USA):

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
För att kunna komma åt din app på Internet behöver du en offentlig IP-adress för belastningsutjämnaren. Skapa en offentlig IP-adress med [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* i resursgruppen *myResourceGroupLoadBalancer*:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Skapa en belastningsutjämnare
Skapa en IP-adresspool på klientdelen med [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). I följande exempel skapas en IP-adresspool på klientdelen med namnet *myFrontEndPool* och adressen *myPublicIP* kopplas: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Skapa en adresspool på serverdelen med [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). Virtuella datorer ansluter till den här adresspoolen i de resterande stegen. I följande exempel skapas en adresspool på serverdelen med namnet *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

Skapa belastningsutjämnaren med [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). I följande exempel skapas en belastningsutjämnare med namnet *myLoadBalancer* med de IP-pooler för klient- och serverdelen som skapades i föregående steg:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Om du vill att belastningsutjämnaren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från belastningsutjämnarens rotation baserat på deras svar på hälsokontroller. Som standard tas en virtuell dator bort från belastningsutjämnarens distribution efter två fel i följd inom ett intervall på 15 sekunder. Du skapar en hälsoavsökning baserat på ett protokoll eller en specifik hälsokontrollsida för din app. 

I följande exempel skapas en TCP-avsökning. Du kan också skapa anpassade HTTP-avsökningar om du vill ha mer detaljerade hälsokontroller. När du använder en anpassad HTTP-avsökning måste du skapa en hälsokontrollsida, till exempel *healthcheck.aspx*. Avsökningen måste returnera svaret **HTTP 200 OK** för att belastningsutjämnaren ska behålla värden i rotation.

Du skapar en TCP-hälsoavsökning med [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). I följande exempel skapas en hälsoavsökning med namnet *myHealthProbe* som övervakar alla virtuella datorer på *TCP*-port *80*:

```azurepowershell-interactive
Add-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Om du vill använda hälsoavsökningen uppdaterar du belastningsutjämnaren med [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Skapa en belastningsutjämningsregel
En belastningsutjämningsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. För att säkerställa att de virtuella datorerna endast tar emot felfri trafik definierar du också vilken hälsoavsökning som ska användas.

Skapa en belastningsutjämningsregel med [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). I följande exempel skapas en belastningsutjämningsregel med namnet *myLoadBalancerRule* och trafiken utjämnas på *TCP*-port *80*:

```azurepowershell-interactive
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Uppdatera belastningsutjämnaren med [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Innan du kan distribuera virtuella datorer och testa din belastningsutjämnare skapar du virtuella nätverksresurser. Mer information om virtuella nätverk finns i självstudiekursen [Hantera virtuella Azure-nätverk](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Skapa nätverksresurser
Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Virtuella nätverkskort skapas med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). I följande exempel skapas tre virtuella nätverkskort. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.) Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när du vill och lägga till dem i belastningsutjämnaren:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Skapa virtuella datorer
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app.

Skapa en tillgänglighetsuppsättning med [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). I följande exempel skapas en tillgänglighetsuppsättning med namnet *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

Ange ett administratörsanvändarnamn och lösenord för de virtuella datorerna med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nu kan du skapa de virtuella datorerna med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas tre virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Parametern `-AsJob` skapar den virtuella datorn som en bakgrundsaktivitet så att PowerShell-kommandotolkarna återgår till dig. Du kan visa information om bakgrundsjobb med cmdleten `Job`. Det tar några minuter att skapa och konfigurera de tre virtuella datorerna.


### <a name="install-iis-with-custom-script-extension"></a>Installera IIS med tillägget för anpassat skript
I en tidigare självstudiekurs om [hur du konfigurerar en virtuell Windows-dator](tutorial-automate-vm-deployment.md) lärde du dig hur du automatiserar VM-anpassning med tillägget för anpassat skript för Windows. Du kan använda samma tillvägagångssätt för att installera och konfigurera IIS på dina virtuella datorer.

Använd [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) för att installera tillägget för anpassat skript. Tillägget kör `powershell Add-WindowsFeature Web-Server` för att installera IIS-webbservern och uppdaterar sedan sidan *Default.htm* så att värddatornamnet visas för den virtuella datorn:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testa belastningsutjämnaren
Hämta den offentliga IP-adressen för belastningsutjämnaren med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Webbplatsen visas, inklusive värddatornamnet för den virtuella dator som belastningsutjämnaren distribuerade trafik till, som i följande exempel:

![Köra IIS-webbplatsen](./media/tutorial-load-balancer/running-iis-website.png)

Om du vill se hur belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer som kör din app, kan du framtvinga uppdatering av webbläsaren.


## <a name="add-and-remove-vms"></a>Lägga till och ta bort virtuella datorer
Du kan behöva utföra underhåll på de virtuella datorerna som kör appen, till exempel installera uppdateringar av operativsystemet. För att klara ökad trafik till din app kan du behöva lägga till fler virtuella datorer. I det här avsnittet visas hur du tar bort eller lägger till en virtuell dator från belastningsutjämnaren.

### <a name="remove-a-vm-from-the-load-balancer"></a>Ta bort en virtuell dator från belastningsutjämnaren
Hämta nätverkskortet med [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) och ange sedan värdet *$null* för egenskapen *LoadBalancerBackendAddressPools* för det virtuella nätverkskortet. Uppdatera slutligen det virtuella nätverkskortet:

```azurepowershell-interactive
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Om du vill se hur belastningsutjämnaren distribuerar trafik över de två återstående virtuella datorerna som kör din app, kan du framtvinga uppdatering av webbläsaren. Nu kan du utföra underhåll på den virtuella datorn, till exempel installera uppdateringar av operativsystemet eller göra en omstart av den virtuella datorn.

### <a name="add-a-vm-to-the-load-balancer"></a>Lägga till en virtuell dator i belastningsutjämnaren
Om du behöver utöka kapaciteten efter underhåll av den virtuella datorn kan du ange egenskapen *LoadBalancerBackendAddressPools* för det virtuella nätverket till *BackendAddressPool* från [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer):

Hämta belastningsutjämnaren:

```azurepowershell-interactive
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Nästa steg

I den här kursen har du skapat en belastningsutjämnare och kopplat virtuella datorer till den. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en Azure Load Balancer
> * Skapa en hälsoavsökning för belastningsutjämnaren
> * Skapa trafikregler för belastningsutjämnaren
> * Skapa en grundläggande IIS-webbplats med tillägget för anpassat skript
> * Skapa virtuella datorer och anslut dem till en belastningsutjämnare
> * Visa en belastningsutjämnare i praktiken
> * Lägga till och ta bort virtuella datorer från en belastningsutjämnare

Gå vidare till nästa självstudie där du får lära dig hur du hanterar VM-nätverk.

> [!div class="nextstepaction"]
> [Hantera virtuella datorer och virtuella nätverk](./tutorial-virtual-network.md)
