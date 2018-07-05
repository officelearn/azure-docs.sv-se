---
title: Hur du använder Azure API Management i virtuellt nätverk med Application Gateway | Microsoft Docs
description: Lär dig att skapa och konfigurera en Azure API Management i internt virtuellt nätverk med Application Gateway (WAF) som klientdel
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: c7d4351a9691c9787c42107306220e075f8648a0
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37435131"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrera API Management i ett internt virtuellt nätverk med Application Gateway

##<a name="overview"> </a> Översikt över

API Management-tjänsten kan konfigureras i ett virtuellt nätverk i interna läge, vilket gör att det är enbart tillgänglig från det virtuella nätverket. Azure Application Gateway är en PAAS-tjänst som tillhandahåller Layer 7-belastningsutjämning. Den fungerar som en omvänd proxy-tjänst och ger bland erbjuder en Web Application Firewall (WAF).

Kombinera API Management som etablerats i ett internt virtuellt nätverk med Application Gateway-klientdel kan följande scenarier:

* Använd samma API Management-resurs för användning av både interna konsumenter och externa konsumenter.
* Använd en enda API Management-resurs och har en delmängd av API: er som definierats i API Management som är tillgängliga för externa användare.
* Ger en nyckelfärdig sätt att aktivera åtkomst till API Management från det offentliga Internet och inaktivera.

## <a name="prerequisites"></a>Förutsättningar

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instansen. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

##<a name="scenario"> </a> Scenario
Den här artikeln beskriver hur du använder ett enda API Management-tjänsten för både interna och externa konsumenter och gör det fungerar som en enda klientdel för både lokala och molnbaserade API: er. Du kan även se hur du kan exponera endast en delmängd av dina API: er (i exemplet som de är markerade i grönt) för extern användning med hjälp av PathBasedRouting funktionerna som är tillgängliga i Application Gateway.

I det första exemplet installationen hanteras alla API: er endast från inom ditt virtuella nätverk. Interna konsumenter (markerade i orange) kan komma åt alla dina interna och externa API: er. Trafiken överförs aldrig till Internet en högpresterande levereras via Expressroute-kretsar.

![URL: en väg](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Skapa ett virtuellt nätverk och skapa separata undernät för API Management och Application Gateway.
3. Om du vill skapa en anpassad DNS-server för det virtuella nätverket kan du göra det innan du påbörjar distribueringen. Kontrollera det fungerar genom att se till att en virtuell dator som skapats i ett nytt undernät i det virtuella nätverket kan lösa och få åtkomst till alla Azure-tjänstslutpunkter.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Vad krävs för att skapa en integration mellan API Management och Application Gateway?

* **Backend-serverpool:** det här är den interna virtuella IP-adressen för API Management-tjänsten.
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. De här inställningarna tillämpas på alla servrar i poolen.
* **Frontend-port:** den offentliga porten som är öppen på programgatewayen. Trafik det omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
* **Regel:** regeln Binder en lyssnare till en backend-serverpoolen.
* **Anpassade Hälsoavsökning:** Application Gateway, som standard använder IP-adressbaserad avsökningar för att ta reda på vilka servrar i BackendAddressPool är aktiva. API-hanteringen tjänsten bara svarar på förfrågningar som har rätt värdhuvudet är därför standard-avsökningar misslyckas. En anpassad hälsoavsökning måste definieras för att bestämma att tjänsten är aktiv och att den ska vidarebefordra begäranden Programgateway.
* **Anpassad Domäncertifikat:** åtkomst till API Management från internet måste du skapa en CNAME-mappning av dess värdnamn till frontend-DNS-namn för Application Gateway. Detta säkerställer att värdnamnet huvud och certifikat som skickats till Application Gateway som vidarebefordras till API Management är en APIM kan identifiera som giltigt.

## <a name="overview-steps"> </a> Steg som krävs för att integrera API Management och Application Gateway

1. Skapa en resursgrupp för Resource Manager.
2. Skapa ett virtuellt nätverk, undernät och offentlig IP-adress för Application Gateway. Skapa ett annat undernät för API Management.
3. Skapa en API Management-tjänsten i VNET-undernät som skapades ovan och kontrollera att du använder det interna läget.
4. Konfigurera det anpassade domännamnet i API Management-tjänsten.
5. Skapa ett konfigurationsobjekt för Programgatewayen.
6. Skapa en Application Gateway-resurs.
7. Skapa en CNAME-post från det offentliga DNS-namnet på Application Gateway till värdnamn för API Management-proxy.

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

Kontrollera att du använder den senaste versionen av Azure PowerShell. Mer information finns i [Använda Windows PowerShell med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>Steg 1

Logga in på Azure

```powershell
Connect-AzureRmAccount
```

Autentisera med dina autentiseringsuppgifter.<BR>

### <a name="step-2"></a>Steg 2

Kontrollera prenumerationerna för kontot och välj den.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Steg 3

Skapa en resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för application gateway

I följande exempel visar hur du skapar ett virtuellt nätverk med resource manager.

### <a name="step-1"></a>Steg 1

Tilldela adressintervallet 10.0.0.0/24 till undernätsvariabeln som ska användas för Application Gateway när du skapar ett virtuellt nätverk.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Steg 2

Tilldela adressintervallet 10.0.1.0/24 till undernätsvariabeln som ska användas för API-hantering när du skapar ett virtuellt nätverk.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Steg 3

Skapa ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **apim-appGw-RG** för regionen West US med prefixet 10.0.0.0/16 med undernät 10.0.0.0/24 och 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Steg 4

Tilldela en undernätsvariabel för nästa steg

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Skapa en API Management-tjänst i ett virtuellt nätverk som konfigurerats i interna läge

I följande exempel visas hur du skapar en API Management-tjänsten i ett virtuellt nätverk som konfigurerats för intern åtkomst.

### <a name="step-1"></a>Steg 1
Skapa ett virtuellt nätverk för API Management-objekt med undernätet $apimsubnetdata skapade ovan.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Steg 2
Skapa en API Management-tjänsten i virtuella nätverk.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
När kommandot ovan lyckas referera till [DNS-konfiguration som krävs för att komma åt interna VNET API Management-tjänsten](api-management-using-with-internal-vnet.md#apim-dns-configuration) att komma åt den.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurera ett anpassat domännamn i API Management

### <a name="step-1"></a>Steg 1
Överför certifikatet med privat nyckel för domänen. I det här exemplet blir `*.contoso.net`.

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Steg 2
När certifikatet har överförts, skapa ett konfigurationsobjekt för värdnamn för proxy med ett värdnamn för `api.contoso.net`, enligt exemplet certifikatet innehåller utfärdaren av den `*.contoso.net` domän.

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-adressresurs **publicIP01** i resursgruppen **apim-appGw-RG** för regionen West US.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-application-gateway-configuration"></a>Skapa konfiguration för programgatewayen

Alla konfigurationsobjekt måste konfigureras innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet **gatewayIP01**. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Steg 2

Konfigurera frontend IP-porten för den offentliga IP-slutpunkten. Den här porten är den port som användarna ansluta till.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Steg 3

Konfigurera klientdelens IP med den offentliga IP-slutpunkten.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Steg 4

Konfigurera certifikatet för Application-Gateway som används för att dekryptera och kryptera trafik som passerar genom.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Steg 5

Skapa HTTP-lyssnare för Programgatewayen. Tilldela frontend IP-konfiguration, port och ssl certifikatet till den.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Steg 6

Skapa en anpassad avsökning för API Management-tjänsten `ContosoApi` proxy domän slutpunkt. Sökvägen `/status-0123456789abcdef` är en standardslutpunkt för hälsotillstånd som finns i API Management-tjänster. Ange `api.contoso.net` som en anpassad avsökning värdnamnet till att skydda den med SSL-certifikat.

> [!NOTE]
> Värdnamnet `contosoapi.azure-api.net` är proxy standardvärdnamnet konfigureras när en tjänst med namnet `contosoapi` har skapats i offentliga Azure.
>

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Steg 7

Ladda upp certifikat som ska användas för de resurser som SSL-aktiverad backend-pool. Är detta samma certifikat som du angav i steg 4 ovan.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Steg 8

Konfigurera inställningar för HTTP-serverdelen för Application Gateway. Detta innefattar att ställa in en timeoutgränsen för backend-förfrågan efter vilken de avbryts. Det här värdet skiljer sig från avsökningen timeout-värde.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Steg 9

Konfigurera backend-IP-adresspoolen med namnet **apimbackend** med den interna virtuella IP-adressen för API Management-tjänsten som skapades ovan.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Steg 10

Skapa inställningar för en dummy (icke-existerande)-serverdel. Begäranden till API-sökvägar som vi inte vill exponera från API Management via Application Gateway ska nå serverdelen och returnera 404.

Konfigurera HTTP-inställningar för dummy-serverdel.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Konfigurera en dummy serverdel **dummyBackendPool**, som pekar på ett FQDN-adressen **dummybackend.com**. Den här FQDN-adressen finns inte i det virtuella nätverket.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Skapa en regelinställning som Application Gateway ska använda som standard som pekar mot en obefintlig serverdelen **dummybackend.com** i det virtuella nätverket.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Steg 11

Konfigurera URL: en regel sökvägar för backend-adresspooler. Detta gör att välja endast några av de API: er i API Management för att exponeras offentligt. Till exempel om det finns `Echo API` (/ echo /), `Calculator API` (/calc/) o.s.v. se bara `Echo API` nås från Internet).

I följande exempel skapas en enkel regel för ”/ echo /” sökväg routning trafiken till backend-server ”apimProxyBackendPool”.

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Om sökvägen inte matchar sökvägsregler som vi vill aktivera från API Management, konfigurerar regelkonfigurationen sökväg kartan även en standard backend-adresspool med namnet **dummyBackendPool**. Till exempel http://api.contoso.net/calc/sum går till **dummyBackendPool** som den definieras som standardadresspool för icke matchade trafik.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

Ovanstående steg säkerställer att endast begäranden för sökvägen ”/ echo” tillåts passera Application Gateway. Begäranden till andra API: er som konfigurerats i API Management genereras 404-fel från Programgatewayen när nås från Internet.

### <a name="step-12"></a>Steg 12

Skapa en regelinställning för Application Gateway kan använda URL-sökvägsbaserad routning.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Steg 13

Konfigurera antalet instanser av och storleken för Application Gateway. Här använder vi den [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) för ökad säkerhet för API Management-resursen.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Steg 14

Konfigurera WAF i ”förhindringsläge”.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Skapa Programgateway

Skapa en Programgateway med alla konfigurationsobjekt från föregående steg.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME för API Management proxy värdnamnet till det offentliga DNS-namnet på Application Gateway-resursen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP-adress, kräver ett dynamiskt tilldelat DNS-namn, vilket inte kanske är lätt att använda Application Gateway.

Programgatewayens DNS-namn som ska användas för att skapa en CNAME-post som pekar APIM-proxyvärdnamn (t.ex. `api.contoso.net` i exemplen ovan) till den här DNS-namnet. Konfigurera frontend IP-CNAME-post genom att hämta information om Programgatewayen och dess associerade IP/DNS-namn med PublicIPAddress-elementet. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> Sammanfattning
Azure API Management som konfigurerats i ett virtuellt nätverk ger en enda gateway-gränssnitt för alla konfigurerade API: er, oavsett om de är värdbaserade lokalt eller i molnet. Integrera Application Gateway med API Management ger dig flexibilitet för att selektivt aktivera specifika API: er ska vara tillgänglig på Internet, samt att tillhandahålla en brandvägg för webbaserade program som en klientdel till API Management-instans.

##<a name="next-steps"> </a> Nästa steg
* Läs mer om Azure Application Gateway
  * [Översikt över Application Gateway](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway Web Application Firewall](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Programgateway med hjälp av sökvägsbaserad Routning](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Läs mer om API Management och virtuella nätverk
  * [Använda API Management tillgängligt endast inom det virtuella nätverket](api-management-using-with-internal-vnet.md)
  * [Använda API Management i ett virtuellt nätverk](api-management-using-with-vnet.md)
