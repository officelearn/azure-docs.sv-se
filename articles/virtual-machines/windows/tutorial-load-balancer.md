---
title: "Hur du läser in balansera Windows-datorer i Azure | Microsoft Docs"
description: "Lär dig hur du skapar ett program med hög tillgänglighet och säkert över tre virtuella Windows-datorer med hjälp av Azure belastningsutjämnare"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6738d88d5a0430abaf3855dbf97a618e4c83617f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Hur du läser in balansera Windows-datorer i Azure för att skapa ett program med hög tillgänglighet
Belastningsutjämning ger högre tillgänglighet genom att sprida inkommande begäranden över flera virtuella datorer. I kursen får du lära dig om de olika komponenterna i Azure belastningsutjämnare som distribuerar trafik och ger hög tillgänglighet. Lär dig att:

> [!div class="checklist"]
> * Skapa en Azure belastningsutjämnare
> * Skapa en belastningsutjämnaren, hälsoavsökningen
> * Skapa regler för nätverkstrafik för belastningsutjämnare
> * Använda tillägget för anpassat skript för att skapa en grundläggande IIS-webbplats
> * Skapa virtuella datorer och ansluta till en belastningsutjämnare
> * Visa en belastningsutjämnare i praktiken
> * Lägga till och ta bort virtuella datorer från en belastningsutjämnare

Den här självstudien kräver Azure PowerShell-modul version 3.6 eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).


## <a name="azure-load-balancer-overview"></a>Översikt över Azure belastningen belastningsutjämnare
En Azure belastningsutjämnare är en belastningsutjämnare för Layer-4 (TCP, UDP) som ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfri virtuella datorer. En belastningsutjämnaren, hälsoavsökningen övervakar en viss port på varje virtuell dator och distribuerar endast trafik till en virtuell dator i drift.

Du kan definiera en frontend IP-konfiguration som innehåller en eller flera offentliga IP-adresser. Den här frontend IP-konfigurationen kan din belastningsutjämnare och program som ska vara tillgänglig via Internet. 

Virtuella datorer ansluta till en belastningsutjämnare med hjälp av deras virtuella nätverksgränssnittskortet (NIC). Om du vill distribuera trafik till de virtuella datorerna en backend-adresspool som innehåller IP-adresser för virtuella (NIC) som är anslutna till belastningsutjämnaren.

Om du vill styra flödet av trafik, kan du definiera belastningen belastningsutjämnaren regler för specifika portar och protokoll som mappar till dina virtuella datorer.


## <a name="create-azure-load-balancer"></a>Skapa Azure belastningsutjämnare
Det här avsnittet beskrivs hur du kan skapa och konfigurera varje komponent i belastningsutjämnaren. Innan du kan skapa din belastningsutjämnare, skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupLoadBalancer* i den *EastUS* plats:

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS
```

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
För att komma åt din app på Internet, måste en offentlig IP-adress för belastningsutjämnaren. Skapa en offentlig IP-adress med [ny AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* i den *myResourceGroupLoadBalancer* resursgrupp:

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>Skapa en belastningsutjämnare
Skapa en IP-adress för klientdel med [ny AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). I följande exempel skapas en IP-adress för klientdel som heter *myFrontEndPool*: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

Skapa en backend-adresspool med [ny AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). I följande exempel skapas en backend-adresspool med namnet *myBackEndPool*:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Nu ska du skapa belastningsutjämnaren med [ny AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). I följande exempel skapas en belastningsutjämnare med namnet *myLoadBalancer* med hjälp av den *myPublicIP* adress:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökningen
Om du vill att belastningsutjämnaren ska övervaka status för din app kan du använda en hälsoavsökningen. Avsökningen hälsa dynamiskt lägger till eller tar bort virtuella datorer från belastningen belastningsutjämnaren rotationen baserat på deras svar på hälsokontroller. En virtuell dator tas bort från belastningsutjämnaren belastningsdistribution efter två på varandra följande fel intervaller 15 sekunder som standard. Du skapar en hälsoavsökningen baserat på ett protokoll eller ett visst hälsotillstånd sidan för kontroll för din app. 

I följande exempel skapas en TCP-avsökning. Du kan också skapa anpassade HTTP-avsökningar mer detaljerade hälsokontroller. När du använder en anpassad HTTP-avsökningen, måste du skapa sidan för kontroll av hälsotillstånd som *healthcheck.aspx*. Avsökningen måste returnera ett **HTTP 200 OK** svar att behålla värden i rotation belastningsutjämnaren.

Om du vill skapa en TCP-hälsoavsökningen du använder [Lägg till AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). I följande exempel skapas en hälsoavsökningen med namnet *myHealthProbe* som övervakar varje virtuell dator:

```powershell
Add-AzureRmLoadBalancerProbeConfig `
  -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Uppdaterar belastningsutjämnaren med [Set AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Skapa en regel för belastningsutjämnare
En regel för belastningsutjämnare används för att definiera hur trafiken distribueras till de virtuella datorerna. Du kan definiera frontend IP-konfiguration för inkommande trafik och backend-IP-adresspool för att ta emot trafik, tillsammans med nödvändig käll- och port. Om du vill kontrollera endast felfri virtuella datorer ta emot trafik kan definiera du också hälsoavsökningen att använda.

Skapa en regel för belastningsutjämnare med [Lägg till AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). I följande exempel skapas en regel för belastningsutjämnare med namnet *myLoadBalancerRule* och balanserar trafik på port *80*:

```powershell
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name myHealthProbe

Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Uppdaterar belastningsutjämnaren med [Set AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Innan du distribuerar vissa virtuella datorer och testa din belastningsutjämnare, skapa stödresurser för virtuellt nätverk. Mer information om virtuella nätverk finns i [hantera virtuella Azure-nätverk](tutorial-virtual-network.md) kursen.

### <a name="create-network-resources"></a>Skapa nätverksresurser
Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med *mySubnet*:

```powershell
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Skapa en grupp nätverkssäkerhetsregeln med [ny AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig), skapa en nätverkssäkerhetsgrupp med [ny AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Lägga till nätverkssäkerhetsgruppen till undernät med [Set AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) och uppdatera sedan det virtuella nätverket med [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). 

I följande exempel skapas en grupp nätverkssäkerhetsregeln med namnet *myNetworkSecurityGroup* och tillämpar den *mySubnet*:

```powershell
# Create security rule config
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create the network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule

# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

Virtuella nätverkskort skapas med [ny AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). I följande exempel skapas tre virtuella nätverkskort. (Ett virtuellt nätverkskort för varje virtuell dator skapar du en app i följande steg). Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när som helst och lägga till dem i belastningsutjämnaren:

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location EastUS `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app.

Skapa en tillgänglighetsuppsättning med [ny AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). I följande exempel skapas en tillgänglighetsuppsättning namngivna *myAvailabilitySet*:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location EastUS `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

Ange en administratör användarnamn och lösenord för de virtuella datorerna med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Nu kan du skapa de virtuella datorerna med [ny AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas tre virtuella datorer:

```powershell
for ($i=1; $i -le 3; $i++)
{
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_D1 `
    -AvailabilitySetId $availabilitySet.Id
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk$i `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
  $nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic$i
  $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
  New-AzureRmVM `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Location EastUS `
    -VM $vm
}
```

Det tar några minuter att skapa och konfigurera alla tre virtuella datorer.

### <a name="install-iis-with-custom-script-extension"></a>Installera IIS med tillägget för anpassat skript
I en tidigare självstudiekurs om [hur du anpassar en Windows-dator](tutorial-automate-vm-deployment.md), du har lärt dig hur du automatiserar VM anpassning med anpassade skript tillägget för Windows. Du kan använda samma metod för att installera och konfigurera IIS på dina virtuella datorer.

Använd [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) att installera tillägget för anpassat skript. Tillägget körs `powershell Add-WindowsFeature Web-Server` att installera IIS-webbserver och uppdateringar av *Default.htm* sidan för att visa värdnamnet för den virtuella datorn:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Testa belastningsutjämnare
Hämta offentlig IP-adressen för din belastningsutjämnare med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtar IP-adressen för *myPublicIP* skapade tidigare:

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Webbplatsen visas, inklusive värdnamnet för den virtuella datorn som belastningsutjämnaren distribuerade trafik till som i följande exempel:

![Kör IIS-webbplats](./media/tutorial-load-balancer/running-iis-website.png)

Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer som kör appen du kan force-uppdatera webbläsaren.


## <a name="add-and-remove-vms"></a>Lägga till och ta bort virtuella datorer
Du kan behöva utföra underhåll på virtuella datorer som kör appen, till exempel installera uppdateringar av OS. Du kan behöva lägga till ytterligare virtuella datorer för att hantera den ökade trafiken till din app. Det här avsnittet visar hur du tar bort eller lägga till en virtuell dator från belastningsutjämnaren.

### <a name="remove-a-vm-from-the-load-balancer"></a>Ta bort en virtuell dator från belastningsutjämnaren
Hämta nätverkskort med [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface), ange den *LoadBalancerBackendAddressPools* -egenskapen för det virtuella nätverkskortet till *$null*. Slutligen uppdatera det virtuella nätverkskortet.:

```powershell
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Om du vill se belastningsutjämnaren distribuerar trafik över de återstående två virtuella datorerna kör appen du kan framtvinga-uppdatera webbläsaren. Du kan nu utföra underhåll på den virtuella datorn, till exempel installera uppdateringar av operativsystem eller utföra en VM-omstart.

### <a name="add-a-vm-to-the-load-balancer"></a>Lägga till en virtuell dator till belastningsutjämnaren
Efter utföra VM underhåll eller om du behöver Utöka kapaciteten på *LoadBalancerBackendAddressPools* -egenskapen för det virtuella nätverkskortet till den *BackendAddressPool* från [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer):

Hämta belastningsutjämnaren:

```powershell
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Nästa steg

I kursen får du skapat en belastningsutjämnare och anslutna virtuella datorer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en Azure belastningsutjämnare
> * Skapa en belastningsutjämnaren, hälsoavsökningen
> * Skapa regler för nätverkstrafik för belastningsutjämnare
> * Använda tillägget för anpassat skript för att skapa en grundläggande IIS-webbplats
> * Skapa virtuella datorer och ansluta till en belastningsutjämnare
> * Visa en belastningsutjämnare i praktiken
> * Lägga till och ta bort virtuella datorer från en belastningsutjämnare

Gå vidare till nästa kurs att lära dig hur du hanterar Virtuella nätverk.

> [!div class="nextstepaction"]
> [Hantera virtuella datorer och virtuella nätverk](./tutorial-virtual-network.md)
