---
title: "Skapa en anpassad avsökning - Azure Application Gateway - PowerShell | Microsoft Docs"
description: "Lär dig hur du skapar en anpassad avsökningsåtgärd för Programgateway med PowerShell i Resource Manager"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: 344d6922d1649449e26f2500e538b5a0b440476d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Skapa en anpassad avsökningsåtgärd för Programgateway i Azure med hjälp av PowerShell för Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln, lägger du till en anpassad avsökning en befintlig Programgateway med PowerShell. Anpassade avsökningar är användbara för program som har ett visst hälsotillstånd Kontrollera sidan eller för program som inte tillhandahåller ett lyckat svar på standardwebbprogrammet.

> [!NOTE]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver Resource Manager-distributionsmodellen, som Microsoft rekommenderar för de flesta nya distributioner i stället för [den klassiska distributionsmodellen](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Skapa en Programgateway med en anpassad avsökningsåtgärd

### <a name="sign-in-and-create-resource-group"></a>Logga in och skapa resursgrupp

1. Använd `Login-AzureRmAccount` för autentisering.

  ```powershell
  Login-AzureRmAccount
  ```

1. Få prenumerationer för kontot.

  ```powershell
  Get-AzureRmSubscription
  ```

1. Välj vilka av dina Azure-prenumerationer som du vill använda.

  ```powershell
  Select-AzureRmSubscription -Subscriptionid '{subscriptionGuid}'
  ```

1. Skapa en resursgrupp. Du kan hoppa över det här steget om du har en befintlig resursgrupp.

  ```powershell
  New-AzureRmResourceGroup -Name appgw-rg -Location 'West US'
  ```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

Vi har skapat en resursgrupp med namnet i föregående exempel **appgw RG** plats **västra USA**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

I följande exempel skapas ett virtuellt nätverk och ett undernät för programgatewayen. Programgateway kräver sin egen undernät för användning. Undernät som skapats för Programgateway bör vara mindre än adressutrymmet för VNET så att andra undernät kan skapas och används därför.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs, **publicIP01**, i resursgruppen **appgw-rg** för regionen USA, västra. Det här exemplet används en offentlig IP-adress för programgatewayen frontend IP-adress.  Programgateway kräver offentliga IP-adressen har ett dynamiskt skapade DNS-namn därför den `-DomainNameLabel` kan inte anges under genereringen av den offentliga IP-adressen.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Skapa en programgateway

Du ställa in alla konfigurationsobjekt innan du skapar programgatewayen. I följande exempel skapar konfigurationsobjekt som behövs för en gateway programresursen.

| **Komponent** | **Beskrivning** |
|---|---|
| **Gateway-IP-konfiguration** | En IP-konfiguration för en Programgateway.|
| **Serverdelspool** | En pool med IP-adresser, FQDN eller nätverkskort som är till programservrar som värd för webbprogrammet|
| **Hälsoavsökningen** | En anpassad avsökningsåtgärd som används för att övervaka hälsotillståndet hos poolmedlemmar backend|
| **HTTP-inställningar** | En samling inställningar inklusive, port, protokoll, cookie-baserad tillhörighet, avsökning och timeout.  De här inställningarna avgör hur trafik dirigeras till backend-poolmedlemmar|
| **Klientdelsport** | Den port som programgatewayen lyssnar efter trafik på|
| **Lyssnare** | En kombination av protokoll, klientdelens IP-konfiguration och klientdelsport. Det här är vad lyssnar efter inkommande begäranden.
|**Regeln**| Vägar trafiken till lämplig serverdelen baserat på HTTP-inställningar.|

```powershell
# Creates a application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Lägg till en avsökning i en befintlig Programgateway

Följande kodavsnitt lägger till en avsökning i en befintlig gateway för programmet.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Ta bort en avsökning från en befintlig Programgateway

Följande kodavsnitt tar bort en avsökning från en befintlig gateway för programmet.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzureRmApplicationGateway.
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Hämta DNS-namn för programgatewayen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP-adress krävs ett dynamiskt tilldelat DNS-namn som inte är användarvänligt. För att säkerställa att slutanvändare kan nå programgatewayen kan en CNAME-post användas för att peka på den offentliga slutpunkten för programgatewayen. [Konfigurera ett eget domännamn i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Gör detta genom att hämta information om programgatewayen och dess associerade IP/DNS-namn med PublicIPAddress-elementet kopplat till programgatewayen. programgatewayens DNS-namn ska användas för att skapa en CNAME-post som leder de två webbapparna till detta DNS-namn. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av programgatewayen.

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

Lär dig hur du konfigurerar SSL-avlastning genom att besöka: [Konfigurera SSL-avlastning](application-gateway-ssl-arm.md)

