---
title: Skapa en Azure Application Gateway-& skriva om HTTP-huvuden
description: Den här artikeln innehåller information om hur du skapar en Azure Application Gateway och skriver om HTTP-huvuden med Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 2663c049245a7025b5948a64fc5008bb9e7dee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173722"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>Skapa en programgateway och skriva om HTTP-huvuden

Du kan använda Azure PowerShell för att konfigurera [regler för att skriva om HTTP-begärande- och svarshuvuden](rewrite-http-headers.md) när du skapar den nya [automatiska skalning och zon redundanta programgateway-SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Skapa ett virtuellt nätverk för autoskalning
> * Skapa en reserverad offentlig IP-adress
> * Konfigurera infrastrukturen för din application gateway
> * Ange konfiguration av regeln för http-rubriks skriva om
> * Ange automatisk skalning
> * Skapa programgatewayen
> * Testa programgatewayen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Den här artikeln kräver att du kör Azure PowerShell lokalt. Du måste ha Az modul version 1.0.0 eller senare installerat. Kör `Import-Module Az` och`Get-Module Az` sedan för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp på en av de tillgängliga platserna.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med ett dedikerat undernät för en programgateway för automatisk skalning. För närvarande går det bara att distribuera en programgateway för automatisk skalning i varje dedikerat undernät.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Skapa en reserverad offentlig IP-adress

Ange allokeringsmetoden för PublicIPAddress som **Statisk**. En virtuell IP-adress för en programgateway för automatisk skalning kan bara vara statisk. Dynamiska IP-adresser stöds inte. Endast standard-SKU:n för PublicIpAddress stöds.

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Hämta information

Hämta information för resursgruppen, undernätet och IP-adressen i ett lokalt objekt för att skapa informationen om programgatewayens IP-konfiguration.

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurera infrastrukturen

Konfigurera IP-config, frontend IP config, backend-pool, HTTP-inställningar, certifikat, port och lyssnare i ett identiskt format som den befintliga standardprogramgatewayen. Den nya SKU:n följer samma objektmodell som standard-SKU:n.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Ange omskrivningsregelkonfiguration för HTTP-huvudet

Konfigurera de nya objekt som krävs för att skriva om http-huvudena:

- **RequestHeaderConfiguration**: Det här objektet används för att ange de fält för begäranhuvud som du tänker skriva om och det nya värdet som de ursprungliga rubrikerna måste skrivas om till.
- **ResponseHeaderConfiguration**: det här objektet används för att ange de svarshuvudfält som du tänker skriva om och det nya värdet som de ursprungliga rubrikerna måste skrivas om till.
- **ActionSet**: det här objektet innehåller konfigurationerna för de begärande- och svarshuvuden som anges ovan. 
- **Skriv omRule:** det här objektet innehåller alla *actionSets* som anges ovan. 
- **Skriv omRuleSet**- det här objektet innehåller alla *omskrivningsrules* och måste kopplas till en routningsregel för begäran - grundläggande eller sökvägsbaserade.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Ange routningsregeln

Skapa en routningsregel för begäran. När den här omskrivningskonfigurationen har skapats är den kopplad till källlyssnaren via routningsregeln. När du använder en grundläggande routningsregel associeras konfigurationen för huvudreformat med en källavlyssnare och en global rubrikskrivning. När en sökvägsbaserad routningsregel används definieras konfigurationen för rubrikreda på URL-sökvägen. Så gäller det bara för det specifika sökvägsområdet på en webbplats. Nedan skapas en grundläggande routningsregel och omskrivningsregeluppsättningen bifogas.

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>Ange automatisk skalning

Nu kan du ange konfigurationen för automatisk skalning för programgatewayen. Två typer av konfiguration för automatisk skalning stöds:

* **Läget för bestämd kapacitet**. I det här läget skalas inte programgatewayen automatiskt och körs med en fast skalningsenhetskapacitet.

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Läget för automatisk skalning**. I det här läget skalas programgatewayen automatiskt baserat på programmets trafikmönster.

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Skapa programgatewayen och inkludera redundanszoner och konfigurationen för automatisk skalning.

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Använd Get-AzPublicIPAddress för att hämta den offentliga IP-adressen för programgatewayen. Kopiera den offentliga IP-adressen eller DNS-namnet och klistra in det i webbläsarens adressfält.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Rensa resurser

Utforska först de resurser som har skapats med application gateway. När du inte behöver dem längre kan du använda kommandot `Remove-AzResourceGroup` för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Nästa steg

- [Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-powershell.md)
