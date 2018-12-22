---
title: 'Självstudie: Skapa en zonredundant programgateway för automatisk skalning med en reserverad IP-adress – Azure PowerShell'
description: I den här självstudien får du lära dig hur du skapar en zonredundant programgateway för automatisk skalning med en reserverad IP-adress med hjälp av Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99fa5d6f0ba74b56a53f2d1af1b99c7e5c2896a7
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323208"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Självstudie: Skapa en application gateway som förbättrar åtkomsten till webbprogrammet

Om du är IT-administratör som arbetar med att förbättra webbappmåtkomst kan du kan optimera din programgateway om du vill skala baserat på kundernas efterfrågan och sträcka dig över flera tillgänglighetszoner. Den här självstudien hjälper dig att konfigurera Azure Application Gateway-funktioner som gör det: automatisk skalning, zonredundans och reserverade virtuella IP-adresser (statisk IP-adress). Du använder Azure PowerShell-cmdletar och Azure Resource Manager-distributionsmodellen för att lösa problemet.

> [!IMPORTANT] 
> SKU:n för zonredundant programgateway för automatisk skalning är för närvarande tillgänglig som en offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk för autoskalning
> * Skapa en reserverad offentlig IP-adress
> * Konfigurera infrastrukturen för din application gateway
> * Ange automatisk skalning
> * Skapa programgatewayen
> * Testa programgatewayen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien kräver att du kör Azure PowerShell lokalt. Du måste ha Azure PowerShell-modulen version 6.9.0 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp på en av de tillgängliga platserna.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med ett dedikerat undernät för en programgateway för automatisk skalning. För närvarande går det bara att distribuera en programgateway för automatisk skalning i varje dedikerat undernät.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Skapa en reserverad offentlig IP-adress

Ange allokeringsmetoden för PublicIPAddress som **Statisk**. En virtuell IP-adress för en programgateway för automatisk skalning kan bara vara statisk. Dynamiska IP-adresser stöds inte. Endast standard-SKU:n för PublicIpAddress stöds.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Hämta information

Hämta information för resursgruppen, undernätet och IP-adressen i ett lokalt objekt för att skapa informationen om programgatewayens IP-konfiguration.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>Konfigurera infrastrukturen

Konfigurera IP-konfigurationen, IP-konfigurationen för klientsidan, serverdelspoolen, HTTP-inställningarna, certifikatet, porten, lyssnaren och regeln i samma format som den befintliga standardprogramgatewayen. Den nya SKU:n följer samma objektmodell som standard-SKU:n.

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Ange automatisk skalning

Nu kan du ange konfigurationen för automatisk skalning för programgatewayen. Två typer av konfiguration för automatisk skalning stöds:

* **Läget för bestämd kapacitet**. I det här läget skalas inte programgatewayen automatiskt och körs med en fast skalningsenhetskapacitet.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **Läget för automatisk skalning**. I det här läget skalas programgatewayen automatiskt baserat på programmets trafikmönster.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Skapa programgatewayen och inkludera redundanszoner och konfigurationen för automatisk skalning.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Använd Get-AzureRmPublicIPAddress till att hämta den offentliga IP-adressen för programgatewayen. Kopiera den offentliga IP-adressen eller DNS-namnet och klistra in det i webbläsarens adressfält.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Rensa resurser

Utforska först de resurser som har skapats med application gateway. När du inte behöver dem längre kan du använda kommandot `Remove-AzureRmResourceGroup` för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-powershell.md)
