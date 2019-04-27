---
title: Skriv om HTTP-huvuden i Azure Application Gateway
description: Den här artikeln innehåller information om hur du skapar ett Azure Application Gateway och skriva om HTTP-huvuden med Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 4747d824dcf531ed883d476a0daad182ea081c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715104"
---
# <a name="tutorial-create-an-application-gateway-and-rewrite-http-headers"></a>Självstudier: Skapa en Programgateway och skriv om HTTP-huvuden

Du kan använda Azure PowerShell för att konfigurera [regler för att skriva om HTTP-begäran och svarshuvuden](rewrite-http-headers.md) när du skapar den nya [automatisk skalning och zonredundant application gateway SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)

> [!IMPORTANT] 
> SKU:n för zonredundant programgateway för automatisk skalning är för närvarande tillgänglig som en offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa ett virtuellt nätverk för autoskalning
> * Skapa en reserverad offentlig IP-adress
> * Konfigurera infrastrukturen för din application gateway
> * Ange http-huvud omarbetning Regelkonfiguration
> * Ange automatisk skalning
> * Skapa programgatewayen
> * Testa programgatewayen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien kräver att du kör Azure PowerShell lokalt. Du måste ha Az Modulversion 1.0.0 eller senare. Kör `Import-Module Az` och sedan`Get-Module Az` att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.

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

Konfigurera IP-config, frontend IP-config, backend-poolen, http-inställningar, certifikat, port och lyssnare i identiska format till befintliga Standard application gateway. Den nya SKU:n följer samma objektmodell som standard-SKU:n.

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

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>Ange HTTP-huvud omarbetning Regelkonfiguration

Konfigurera de nya objekt som krävs för att skriva om http-huvuden:

- **RequestHeaderConfiguration**: det här objektet används för att ange fält i begärans-huvud som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till.
- **ResponseHeaderConfiguration**: det här objektet används för att ange huvudfält för svar som du avser att skriva om och det nya värdet som de ursprungliga sidhuvuden behöva skrivas till.
- **ActionSet**: det här objektet innehåller konfigurationerna för de begärande- och svarshuvuden som anges ovan. 
- **RewriteRule**: det här objektet innehåller alla de *actionSets* angetts ovan. 
- **RewriteRuleSet**– det här objektet innehåller alla de *rewriteRules* och måste vara kopplad till en begäran routningsregel - grundläggande eller -baserad.

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>Ange en routningsregel

Skapa en regel för vidarebefordran av begäran. När du skapat är den här Skriv om konfigurationen kopplat till käll-lyssnaren via en routningsregel för. När du använder en grundläggande routningsregel rubrik Skriv om konfigurationen är associerad med en käll-lyssnare och är en global huvud-omskrivning. När en sökvägsbaserad regel används har rubrik Skriv om konfigurationen definierats för Webbadress för sökvägskarta. Därför gäller detta bara till området angiven sökväg för en plats. Nedan, skapas en regel för vidarebefordran av grundläggande och skriv om regeluppsättningen är ansluten.

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

Använd Get-AzPublicIPAddress för att hämta den offentliga IP-adressen för application gateway. Kopiera den offentliga IP-adressen eller DNS-namnet och klistra in det i webbläsarens adressfält.

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>Rensa resurser

Utforska först de resurser som har skapats med application gateway. När du inte behöver dem längre kan du använda kommandot `Remove-AzResourceGroup` för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-powershell.md)
