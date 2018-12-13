---
title: 'Snabbstart: Skapa en Standardbelastningsutjämnare – Azure PowerShell'
titlesuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en Standard Load Balancer med PowerShell
services: load-balancer
documentationcenter: na
author: KumudD
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: qucikstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2018
ms.author: kumud
ms:custom: seodec18
ms.openlocfilehash: 0db575ee6c04c0fb4639ea2e9e095200da781fe8
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256323"
---
# <a name="get-started"></a>Snabbstart: Skapa en Standardbelastningsutjämnare med Azure PowerShell
Den här snabbstarten visar hur du skapar en Standard Load Balancer med Azure PowerShell. Om du vill testa lastbalanseraren så distribuera två virtuella datorer (VM) som kör Windows-servern och lastbalansera en webbapp mellan de virtuella datorerna. Mer information om Standard Load Balancer finns i [Vad är en Standard Load Balancer](load-balancer-standard-overview.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable AzureRM` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du kan skapa lastbalanseraren måste du skapa en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroupLB* på platsen *eastus*:

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS"
```
## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
För att kunna komma åt din app på Internet behöver du en offentlig IP-adress för lastbalanseraren. Skapa en offentlig IP-adress med [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* i resursgruppen *myResourceGroupLB*:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
-Name "myPublicIP" `
-ResourceGroupName "myResourceGroupLB" `
-Location "EastUS" `
-Sku "Standard" `
-AllocationMethod "Static"
  
```
## <a name="create-standard-load-balancer"></a>Skapa en Standard Load Balancer
 I det här avsnittet konfigurerar du klientdelens IP-adress och serverdelsadresspoolen för lastbalanseraren och skapar sedan en Basic-lastbalanserare.
 
### <a name="create-frontend-ip"></a>Skapa klientdels-IP
Skapa en IP-adress på klientdelen med [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). I följande exempel skapas en IP-konfiguration på klientdelen med namnet *myFrontEnd* och adressen *myPublicIP* kopplas: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEnd" `
  -PublicIpAddress $publicIP
```

### <a name="configure-backend-address-pool"></a>Konfigurera en serverdelsadresspool

Skapa en adresspool på serverdelen med [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). Virtuella datorer ansluter till den här adresspoolen i de resterande stegen. I följande exempel skapas en adresspool på serverdelen med namnet *myBackEndPool*:

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```
### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Om du vill att lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Som standard tas en virtuell dator bort från lastbalanserarens distribution efter två fel i följd inom ett intervall på 15 sekunder. Du skapar en hälsoavsökning baserat på ett protokoll eller en specifik hälsokontrollsida för din app. 

I följande exempel skapas en TCP-avsökning. Du kan också skapa anpassade HTTP-avsökningar om du vill ha mer detaljerade hälsokontroller. När du använder en anpassad HTTP-avsökning måste du skapa en hälsokontrollsida, till exempel *healthcheck.aspx*. Avsökningen måste returnera svaret **HTTP 200 OK** för att lastbalanseraren ska behålla värden i rotation.

Du skapar en TCP-hälsoavsökning med [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). I följande exempel skapas en hälsoavsökning med namnet *myHealthProbe* som övervakar alla virtuella datorer på *HTTP*-port *80*:

```azurepowershell-interactive
$probe = New-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -RequestPath healthcheck2.aspx `
  -Protocol http `
  -Port 80 `
  -IntervalInSeconds 16 `
  -ProbeCount 2
  ```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. För att säkerställa att de virtuella datorerna endast tar emot felfri trafik definierar du också vilken hälsoavsökning som ska användas.

Skapa en lastbalanseringsregel med [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). I följande exempel skapas en lastbalanseringsregel med namnet *myLoadBalancerRule* och trafiken utjämnas på *TCP*-port *80*:

```azurepowershell-interactive
$lbrule = New-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

### <a name="create-the-nat-rules"></a>Skapa NAT-reglerna

Skapa NAT-regler med [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig). Följande exempel skapar NAT-regler som heter *myLoadBalancerRDP1* och *myLoadBalancerRDP2* för att tillåta RDP-anslutningar till backend-servarna med port 4221 och 4222:

```azurepowershell-interactive
$natrule1 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP1' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4221 `
-BackendPort 3389

$natrule2 = New-AzureRmLoadBalancerInboundNatRuleConfig `
-Name 'myLoadBalancerRDP2' `
-FrontendIpConfiguration $frontendIP `
-Protocol tcp `
-FrontendPort 4222 `
-BackendPort 3389
```

### <a name="create-load-balancer"></a>Skapa en lastbalanserare

Skapa Standard Load Balancer med [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). Följande exempel skapar en offentlig Basic-lastbalanserare med namnet myLoadBalancer med klientdelens IP-konfiguration, serverdelspoolen, hälsoavsökningen, lastbalanseringsregeln och NAT-reglerna som du skapade i föregående steg:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
-ResourceGroupName 'myResourceGroupLB' `
-Name 'MyLoadBalancer' `
-Location 'eastus' `
-FrontendIpConfiguration $frontendIP `
-BackendAddressPool $backendPool `
-Probe $probe `
-LoadBalancingRule $lbrule `
-InboundNatRule $natrule1,$natrule2 `
-sku Standard
```

## <a name="create-network-resources"></a>Skapa nätverksresurser
Innan du kan distribuera virtuella datorer och testa din belastningsutjämnare måste du skapar nätverksresurser som stöds – virtuellt nätverk och virtuella nätverkskort. 

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk med [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med *mySubnet*:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.2.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLB" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnetConfig
```
### <a name="create-network-security-group"></a>Skapa nätverkssäkerhetsgrupp
Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Skapa en regel för nätverkssäkerhetsgruppen för port 3389
Skapa en regel för nätverkssäkerhetsgrupp som tillåter RDP-anslutningar via port 3389 med [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive

$rule1 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 1000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Skapa en regel för nätverkssäkerhetsgruppen för port 80
Skapa en regel för nätverkssäkerhetsgrupp som tillåter inkommande anslutningar via port 80 med [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleHTTP' `
-Description 'Allow HTTP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 2000 `
-SourceAddressPrefix Internet `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'myNetworkSecurityGroup' `
-SecurityRules $rule1,$rule2
```

### <a name="create-nics"></a>Skapa nätverkskort
Skapa virtuella nätverkskort med [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). I följande exempel skapas två virtuella nätverkskort. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.) Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när du vill och lägga till dem i lastbalanseraren:

```azurepowershell-interactive
# Create NIC for VM1
$nicVM1 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic1' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule1 `
-Subnet $vnet.Subnets[0]

# Create NIC for VM2
$nicVM2 = New-AzureRmNetworkInterface `
-ResourceGroupName 'myResourceGroupLB' `
-Location 'EastUS' `
-Name 'MyNic2' `
-LoadBalancerBackendAddressPool $backendPool `
-NetworkSecurityGroup $nsg `
-LoadBalancerInboundNatRule $natrule2 `
-Subnet $vnet.Subnets[0]

```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app.

Skapa en tillgänglighetsuppsättning med [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). I följande exempel skapas en tillgänglighetsuppsättning med namnet *myAvailabilitySet*:

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLB" `
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

Nu kan du skapa de virtuella datorerna med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns:

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLB" `
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

Parametern `-AsJob` skapar den virtuella datorn som en bakgrundsaktivitet så att PowerShell-kommandotolkarna återgår till dig. Du kan visa information om bakgrundsjobb med cmdleten `Job`. Det tar några minuter att skapa och konfigurera de två virtuella datorerna.

### <a name="install-iis-with-custom-web-page"></a>Installera IIS med anpassad webbsida
 
Installera IIS med en anpassad webbsida på de båda virtuella datorerna på serversidan enligt följande:

1. Hämta den offentliga IP-adressen för lastbalanseraren. Med `Get-AzureRmPublicIPAdress` hämtar du den offentliga IP-adressen för lastbalanseraren.

  ```azurepowershell-interactive
    Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupLB" `
    -Name "myPublicIP" | select IpAddress
  ```
2. Skapa en anslutning till fjärrskrivbord till VM1 med den offentliga IP-adress du hämtade i föregående steg. 

  ```azurepowershell-interactive

      mstsc /v:PublicIpAddress:4221  
  
  ```
3. Ange autentiseringsuppgifterna för *VM1* för att starta RDP-sessionen.
4. Starta Windows PowerShell på VM1 och använd följande kommandon för att installera IIS-servern och uppdatera standard-HTML-filen.
    ```azurepowershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host" + $env:computername)
    ```
5. Stäng RDP-anslutningen med *myVM1*.
6. Skapa en RDP-anslutning med *myVM2* genom att köra kommandot`mstsc /v:PublicIpAddress:4222` och upprepa steg 4 för *VM2*.

## <a name="test-load-balancer"></a>Testa lastbalanseraren
Hämta den offentliga IP-adressen för lastbalanseraren med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLB" `
  -Name "myPublicIP" | select IpAddress
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Webbplatsen visas, inklusive värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till, som i följande exempel:

![Testa lastbalanseraren](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

Om du vill se hur lastbalanseraren distribuerar trafik över de båda virtuella datorerna som kör din app, kan du framtvinga uppdatering av webbläsaren. 

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroupLB
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en grundläggande lastbalanserare, anslutit virtuella datorer till den, konfigurerat regeln för trafiklastbalansering, konfigurerat hälsoavsökningen och sedan testat lastbalanseraren. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
