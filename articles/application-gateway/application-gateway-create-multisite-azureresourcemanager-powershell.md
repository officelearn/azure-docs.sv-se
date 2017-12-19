---
title: "Skapa en Programgateway som värd för flera platser | Microsoft Docs"
description: "Den här sidan innehåller instruktioner för att skapa, konfigurera en gateway för Azure-program som värd för flera webbprogram på samma gateway."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
ms.openlocfilehash: d42efa7d359f5c87c14afbfd138328b37c8ae6c2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>Skapa en Programgateway som värd för flera webbprogram

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-multisite-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Värd för flera plats kan du distribuera flera webbprogram på samma programgatewayen. Det är beroende av förekomsten av värdhuvudet i den inkommande HTTP-begäranden att avgöra vilka lyssnare skulle ta emot trafik. Lyssnaren dirigerar sedan trafik till lämplig serverdelspool som konfigurerats i regler definitionen av gatewayen. I SSL aktiverat webbprogram Programgateway förlitar sig på servern Servernamnsindikation (SNI)-tillägg för att välja rätt lyssnaren för Internet-trafik. Ett vanligt användningsområde för värd för flera plats är att belastningsutjämna förfrågningar för olika webbdomäner till olika backend-serverpooler. På samma sätt kan flera underdomäner på samma rotdomänen också finnas på samma programgatewayen.

## <a name="scenario"></a>Scenario

I följande exempel Programgateway betjäna trafik för contoso.com och fabrikam.com med två backend-server-adresspooler: contoso-serverpoolen och fabrikam-serverpoolen. Liknande installationsprogrammet kan användas för att värden underdomäner som app.contoso.com och blog.contoso.com.

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Servrar som läggs till backend-poolen som ska användas programgatewayen måste finnas eller har skapat sina slutpunkter i det virtuella nätverket i ett separat undernät eller med en offentlig IP-adress/VIP tilldelad.

## <a name="requirements"></a>Krav

* **Backend-serverpool:** Listan med IP-adresser för backend-servrarna. IP-adresserna som anges bör antingen tillhöra det virtuella undernätet eller vara en offentlig IP-/VIP-adress. FQDN kan också användas.
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Frontend-port:** Den här porten är den offentliga porten som är öppen på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning). För flera platser aktiverade programmet gateway läggs värdnamn och SNI indikatorer till.
* **Regel:** regeln Binder lyssnaren poolen backend-server och definierar vilka backend-serverpoolen trafiken ska dirigeras till när den når en viss lyssnare. Regler bearbetas i angiven ordning och trafik dirigeras via den första regeln som matchar oavsett särskilda egenskaper. Till exempel om du har en regel med hjälp av en grundläggande lyssnare och en regel med en flera platser lyssnare båda på samma port måste regeln med flera platser lyssnaren anges innan en regel med grundläggande lyssnare för flera platser regeln för att fungera som förväntat.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Följande är de steg som behövs för att skapa en Programgateway:

1. Skapa en resursgrupp för Resource Manager.
2. Skapa ett virtuellt nätverk, undernät och offentliga IP för programgatewayen.
3. Skapa ett konfigurationsobjekt för programgatewayen.
4. Skapa en resurs för en programgateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

Kontrollera att du använder den senaste versionen av Azure PowerShell. Mer information finns på [med hjälp av Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Steg 1

Logga in på Azure

```powershell
Login-AzureRmAccount
```
Du ombeds att autentisera dig med dina autentiseringsuppgifter.

### <a name="step-2"></a>Steg 2

Kontrollera prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Steg 3

Välj vilka av dina Azure-prenumerationer som du vill använda.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Steg 4

Skapa en resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

```powershell
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

Alternativt kan du också skapa taggar för en resursgrupp för Programgateway:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

I exemplet ovan kan vi skapa en resursgrupp med namnet **appgw RG** med en plats för **västra USA**.

> [!NOTE]
> Om du behöver konfigurera en anpassad avsökning för din programgateway läser du [Skapa en programgateway med anpassade avsökningar med hjälp av PowerShell](application-gateway-create-probe-ps.md). Besök [anpassade avsökningar, hälsoövervakning och](application-gateway-probe-overview.md) för mer information.

## <a name="create-a-virtual-network-and-subnets"></a>Skapa ett virtuellt nätverk och undernät

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager. Två undernät skapas i det här steget. Det första undernätet är för Programgateway sig själv. Programgateway kräver sin egen undernät för instanser. Andra programgatewayer kan distribueras i det undernätet. Andra undernätet används för att hålla program backend-servrarna.

### <a name="step-1"></a>Steg 1

Tilldela variabeln undernät som används för att hålla programgatewayen adressintervallet 10.0.0.0/24.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>Steg 2

Tilldela variabeln Undernät2 som ska användas för backend-pooler adressintervallet 10.0.1.0/24.

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>Steg 3

Skapa ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **appgw rg** för regionen USA, västra med undernätet 10.0.0.0/24 prefixet 10.0.0.0/16 och 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>Steg 4

Tilldela en variabel för nästa steg, undernät som skapar en Programgateway.

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs, **publicIP01**, i resursgruppen **appgw-rg** för regionen USA, västra.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-application-gateway-configuration"></a>Skapa program gateway-konfiguration

Du måste konfigurera alla konfigurationsobjekt innan du skapar programgatewayen. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet **gatewayIP01**. När Programgateway startar hämtar en IP-adress från det undernät som konfigurerats och dirigera nätverkstrafik till IP-adresser i backend-IP-adresspool. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>Steg 2

Konfigurera backend-IP-adresspool med namnet **pool01** och **pool2** med IP-adresser **134.170.185.46**, **134.170.188.221**, **134.170.185.50** för **pool1** och **134.170.186.46**, **134.170.189.221**, **134.170.186.50** för **pool2**.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

I det här exemplet finns det två backend-adresspooler för routning av nätverkstrafik baserat på den begärda webbplatsen. En pool tar emot trafik från platsen ”contoso.com” och andra poolen tar emot trafik från platsen ”fabrikam.com”. Du måste ersätta föregående IP-adresser för att lägga till egna programslutpunkter IP-adress. I stället för interna IP-adresser, kan du också använda offentliga IP-adresser, FQDN eller en virtuell dators nätverkskort för backend-instanser. Ange FQDN: er i stället för IP-adresser i PowerShell Använd ”-BackendFQDNs” parametern.

### <a name="step-3"></a>Steg 3

Konfigurera gateway programinställning **poolsetting01** och **poolsetting02** för nätverkstrafik Utjämning av nätverksbelastning i backend-poolen. I det här exemplet kan du konfigurera inställningarna för olika backend-pool för backend-pooler. Varje serverdelspool kan ha sin egen serverdelspoolinställning.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Steg 4

Konfigurera klientdelens IP med den offentliga IP-slutpunkten.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Steg 5

Konfigurera klientdelsporten för en programgateway.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>Steg 6

Konfigurera två SSL-certifikat för de två webbplatserna ska stödja i det här exemplet. Ett certifikat är för contoso.com trafik och den andra för fabrikam.com trafik. Dessa certifikat ska vara en certifikatutfärdare som utfärdade certifikat för webbplatser. Självsignerade certifikat stöds men rekommenderas inte för produktion trafik.

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>Steg 7

Konfigurera två lyssnare för två webbplatser i det här exemplet. Det här steget konfigurerar lyssnare för offentlig IP-adress, port och värdnamn används för att ta emot inkommande trafik. HostName parametern krävs för stöd för flera plats och ska vara inställd på lämplig webbplats för vilken trafiken som tas emot. RequireServerNameIndication parameter ska vara inställd på true för webbplatser som behöver stöd för SSL i ett scenario med flera värden. Om SSL-stöd krävs, måste du också ange SSL-certifikatet som används för att skydda trafik för att webbprogrammet. Kombinationen av FrontendIPConfiguration och FrontendPort värdnamn måste vara unikt för en lyssnare. Varje lyssnare har stöd för ett certifikat.

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>Steg 8

Skapa två inställning för regel för två webbprogram i det här exemplet. En regel innehåller lyssnare, serverdelspooler och http-inställningar. Det här steget konfigurerar Programgateway om du vill använda grundläggande routningsregel, ett för varje webbplats. Trafik till varje webbplats tas emot av dess konfigurerade lyssnare och omdirigeras sedan till dess konfigurerade serverdelspool med hjälp av egenskaper som anges i BackendHttpSettings.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>Steg 9

Konfigurera antalet instanser av och storleken på programgatewayen.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Skapa Programgateway

Skapa en Programgateway med alla konfigurationsobjekt från föregående steg.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> Programmet Gateway etablering är en tidskrävande åtgärd och kan ta lite tid att slutföra.
> 
> 

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

Lär dig att skydda dina webbplatser med [Programgateway - Brandvägg för webbaserade program](application-gateway-webapplicationfirewall-overview.md)

