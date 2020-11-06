---
title: 'Självstudie: förbättra webb program åtkomst – Azure Application Gateway'
description: I den här självstudien får du lära dig hur du skapar en zonredundant programgateway för automatisk skalning med en reserverad IP-adress med hjälp av Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 5731b65892877e5c363220d84a0bddeb5f958cee
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93396880"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>Självstudie: skapa en Programgateway som förbättrar åtkomsten till webb program

Om du är IT-administratör som arbetar med att förbättra webbappmåtkomst kan du kan optimera din programgateway om du vill skala baserat på kundernas efterfrågan och sträcka dig över flera tillgänglighetszoner. Den här självstudien hjälper dig att konfigurera Azure Application Gateway-funktioner som gör det: automatisk skalning, zonredundans och reserverade virtuella IP-adresser (statisk IP-adress). Du använder Azure PowerShell-cmdletar och Azure Resource Manager-distributionsmodellen för att lösa problemet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa ett virtuellt nätverk för autoskalning
> * Skapa en reserverad offentlig IP-adress
> * Konfigurera infrastrukturen för din application gateway
> * Ange automatisk skalning
> * Skapa programgatewayen
> * Testa programgatewayen

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här självstudien kräver att du kör Azure PowerShell lokalt. Du måste ha Azure PowerShell-modul version 1.0.0 eller senare installerad. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp på en av de tillgängliga platserna.

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

I produktion bör du importera ett giltigt certifikat som är signerat av en betrodd provider. I den här självstudiekursen skapar du ett självsignerat certifikat med [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Du kan använda [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) med det tumavtryck som returnerades och exportera en pfx-fil från certifikatet.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Du bör se något som liknar det här resultatet:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Använd tumavtrycket till att skapa pfx-filen:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
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

Konfigurera IP-konfigurationen, IP-konfigurationen för klientsidan, serverdelspoolen, HTTP-inställningarna, certifikatet, porten, lyssnaren och regeln i samma format som den befintliga standardprogramgatewayen. Den nya SKU:n följer samma objektmodell som standard-SKU:n.

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
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
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Använd Get-AzPublicIPAddress för att hämta den offentliga IP-adressen för programgatewayen. Kopiera den offentliga IP-adressen eller DNS-namnet och klistra in det i webbläsarens adressfält.

`Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Rensa resurser

Utforska först de resurser som har skapats med application gateway. När du inte behöver dem längre kan du använda kommandot `Remove-AzResourceGroup` för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en programgateway med webbadressbaserade routningsregler](./tutorial-url-route-powershell.md)