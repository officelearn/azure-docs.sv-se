---
title: "Konfigurera brandväggar för webbaserade program: Azure Programgateway | Microsoft Docs"
description: "Den här artikeln innehåller råd om hur du startar med en brandvägg för webbaserade program på en befintlig eller ny application gateway."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Konfigurera brandväggar för webbaserade program på en ny eller befintlig Programgateway

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Lär dig hur du skapar en brandvägg för webbaserade program (Brandvägg)-aktiverat Programgateway. Lär dig också att lägga till en Brandvägg i en befintlig gateway för programmet.

Brandvägg i Azure Application Gateway skyddar webbprogram från vanliga webbaserade attacker som SQL injection, webbplatser scripting attacker och sessionen hijacks.

 Programgateway är en lager 7 belastningsutjämnare. Det ger redundans, prestanda-routing HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Programgateway innehåller många funktioner i programmet leverans nätverksstyrenheten (ADC):

 * HTTP för belastningsutjämning
 * Cookie-baserad session tillhörighet
 * Secure Sockets Layer (SSL)-avlastning
 * Anpassade hälsoavsökningar
 * Stöd för multisitefunktionen
 
 En fullständig lista över funktioner som stöds finns i [översikt för Programgateway](application-gateway-introduction.md).

Den här artikeln visar hur du [lägga till en Brandvägg i en befintlig Programgateway](#add-web-application-firewall-to-an-existing-application-gateway). Den visar även hur du [skapa en Programgateway som använder en Brandvägg](#create-an-application-gateway-with-web-application-firewall).

![scenario-bild][scenario]

## <a name="waf-configuration-differences"></a>Brandvägg configuration skillnader

Om du har läst [skapar en Programgateway med PowerShell](application-gateway-create-gateway-arm.md), du förstår SKU-inställningar för att konfigurera när du skapar en Programgateway. Brandvägg ger ytterligare inställningar för att definiera när du konfigurerar en SKU på en Programgateway. Det finns inga ytterligare ändringar som du gör på Programgateway sig själv.

| **Inställning** | **Detaljer**
|---|---|
|**SKU** |Har stöd för en normal Programgateway utan en Brandvägg **Standard\_små**, **Standard\_medel**, och **Standard\_stor**storlekar. Det finns två ytterligare SKU: er, med introduktionen av en Brandvägg, **Brandvägg\_medel** och **Brandvägg\_stor**. En Brandvägg stöds inte på små programgatewayer.|
|**Nivå** | Tillgängliga värden är **Standard** eller **Brandvägg**. När du använder en Brandvägg måste du välja **Brandvägg**.|
|**Läge** | Den här inställningen är läget för Brandvägg. tillåtna värden är **identifiering** och **förebyggande**. När Brandvägg ställs in i **identifiering** läge måste alla hot som lagras i en loggfil. I **förebyggande** läge, händelser loggas fortfarande men angriparen tar emot ett 403 obehörig svar från programgatewayen.|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>Lägg till en brandvägg för webbaserade program i en befintlig Programgateway

Kontrollera att du använder den senaste versionen av Azure PowerShell. Mer information finns i [använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

1. Logga in på ditt Azure-konto.

    ```powershell
    Login-AzureRmAccount
    ```

2. Välj prenumerationen som ska användas för det här scenariot.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Hämta gateway där du vill lägga till en Brandvägg.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. Konfigurera Brandvägg SKU: N. Tillgängliga storlekar är **Brandvägg\_stor** och **Brandvägg\_medel**. När du använder en Brandvägg nivån måste vara **Brandvägg**. Bekräfta kapacitet när du ställer in SKU: N.

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. Konfigurera inställningar för Brandvägg som har definierats i följande exempel. För **FirewallMode**, tillgängliga värden är **förebyggande** och **identifiering**.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. Uppdatera programgatewayen med inställningarna i föregående steg.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Det här kommandot uppdaterar programgatewayen med en Brandvägg. Information om hur du visar loggar för din Programgateway finns [Programgateway diagnostik](application-gateway-diagnostics.md). Granska loggarna regelbundet för att förstå säkerhetstillståndet webbprogram på grund av säkerhet hur av en Brandvägg.

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Skapa en Programgateway med en brandvägg för webbaserade program

Följande steg leder dig genom hela processen med att skapa en Programgateway med en Brandvägg.

Kontrollera att du använder den senaste versionen av Azure PowerShell. Mer information finns i [använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

1. Logga in på Azure genom att köra `Login-AzureRmAccount`. Du uppmanas att autentisera med dina autentiseringsuppgifter.

2. Kontrollera prenumerationer för kontot genom att köra `Get-AzureRmSubscription`.

3. Välj vilka Azure-prenumeration ska användas.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp för programgatewayen.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

I föregående exempel, vi har skapat en resursgrupp med namnet ”appgw RG” för platsen ”USA, västra”.

> [!NOTE]
> Om du behöver konfigurera en anpassad avsökning för din programgateway läser du [Skapa en programgateway med anpassade avsökningar med hjälp av PowerShell](application-gateway-create-probe-ps.md). Mer information finns i [anpassad avsökningar, hälsoövervakning och](application-gateway-probe-overview.md).

### <a name="configure-a-virtual-network"></a>Konfigurera ett virtuellt nätverk

En Programgateway kräver ett undernät egna. I det här steget skapar du ett virtuellt nätverk med ett adressutrymme för 10.0.0.0/16 och två undernät, en för Programgateway och en för backend-medlemmar.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>Konfigurera offentliga IP-adress

Om du vill hantera externa begäranden kräver programgatewayen en offentlig IP-adress. Den här offentliga IP-adressen får inte ha en `DomainNameLabel` definieras som ska användas av programgatewayen.

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Konfigurera progamgatewayen

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Programgatewayer som skapats med den grundläggande konfigurationen av Brandvägg har konfigurerats med CR 3.0 för skydd.

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett program gateway DNS-namn

När du har skapat, är nästa steg att konfigurera klientdelen för kommunikation. När du använder en offentlig IP-adress, kräver en dynamiskt tilldelad DNS-namn som inte är eget programgatewayen. För att säkerställa att användarna kan träffa programgatewayen, använder du en CNAME-post för att peka till offentlig slutpunkt för programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn för en Azure-molntjänst](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Hämta information om programgatewayen och dess tillhörande IP DNS-namn med hjälp av PublicIPAddress-element som är kopplade till programgatewayen om du vill konfigurera ett alias. Använda den Programgateway DNS-namn för att skapa en CNAME-post som pekar på två webbprogram till DNS-namnet. Vi rekommenderar inte med poster, eftersom VIP kan ändras när programgatewayen startas om.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar diagnostikloggning för att logga händelser som upptäckts eller förhindras med en Brandvägg finns [Programgateway diagnostik](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
