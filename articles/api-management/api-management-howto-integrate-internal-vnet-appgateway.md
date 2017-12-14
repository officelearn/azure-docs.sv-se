---
title: "Hur du använder Azure API Management i det virtuella nätverket med Programgateway | Microsoft Docs"
description: "Lär dig att installera och konfigurera Azure API Management i internt virtuellt nätverk med programmet Gateway (Brandvägg) som klientdel"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: sasolank
ms.openlocfilehash: e138241139329b8bb956157ab55b7d22dc2a9b67
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2017
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrera API Management i ett internt virtuellt nätverk med Programgateway 

##<a name="overview"></a> Översikt
 
API Management-tjänsten kan konfigureras i ett virtuellt nätverk i internt läge vilket gör att det är enbart tillgänglig från det virtuella nätverket. Azure Application Gateway är en PAAS-tjänst som tillhandahåller en lager 7 belastningsutjämnare. Den fungerar som en omvänd proxy-tjänst och innehåller bland erbjudande Web Application Firewall (Brandvägg).

Kombinera API Management etablerad på ett internt virtuellt nätverk med Programgateway klientdel kan följande scenarier:

* Använd samma API Management-resurs för förbrukning av både konsumenter interna och externa användare.
* Använda en enskild resurs för API Management och har en delmängd av API: er som definierats i API-hantering som är tillgänglig för externa användare.
* Ger en nyckelfärdig sätt att aktivera åtkomst till API-hantering från Internet och inaktivera. 

## <a name="prerequisites"></a>Krav

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instans. Mer information finns i [skapa en instans av Azure API Management](get-started-create-service-instance.md).

##<a name="scenario"></a> Scenario
Den här artikeln beskriver hur du använder en enda API Management-tjänst för både interna och externa användare och att den fungerar som en enda klientdel för både lokala och molntjänster API: er. Du kan även se hur du exponera en delmängd av dina API: er (i exemplet som de är markerade i grönt) för extern användning med PathBasedRouting tillgängliga funktioner i Application Gateway.

I det första exemplet installationsprogrammet hanteras alla API: er endast från i ditt virtuella nätverk. Interna användare (markerade i orange) kan komma åt alla dina interna och externa API: er. Trafik överförs aldrig till Internet, en hög prestanda levereras via Expressroute-kretsar.

![URL-väg](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a> Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Skapa ett virtuellt nätverk och skapa separata undernät för API Management och Application Gateway. 
3. Om du vill skapa en anpassad DNS-server för det virtuella nätverket kan du göra det innan du påbörjar distribueringen. Kontrollera det fungerar genom att säkerställa att en virtuell dator som skapats i ett nytt undernät i det virtuella nätverket kan matcha och åtkomst till alla Azure-Tjänsteslutpunkter.

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Vad krävs för att skapa en integration mellan API Management och Programgateway?

* **Backend-serverpoolen:** interna virtuella IP-adress för API Management-tjänsten.
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. Dessa inställningar tillämpas på alla servrar i poolen.
* **Frontend-port:** den offentliga porten som öppnas på programgatewayen. Träffa den trafik omdirigeras till en backend-server.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
* **Regel:** regeln Binder en lyssnare till en pool med backend-server.
* **Anpassade Hälsoavsökningen:** Application Gateway som standard använder IP-adress baserat avsökningar för att ta reda på vilka servrar i BackendAddressPool är aktiva. API-hantering är tjänsten bara svarar på begäranden som har rätt värdadressen därför standard avsökningar misslyckas. En anpassad hälsoavsökningen måste definieras för att bestämma att tjänsten är igång och att den ska vidarebefordra begäranden Programgateway.
* **Certifikatet för domänen:** åtkomst till API-hantering från internet måste du skapa en CNAME-mappning av dess värdnamn Programgateway frontend DNS-namn. Detta garanterar att hostname sidhuvud och certifikat som skickats till Programgateway som vidarebefordras till API-hantering är en APIM kan identifiera som giltig.

## <a name="overview-steps"></a> Steg som krävs för att integrera API Management och Programgateway 

1. Skapa en resursgrupp för Resource Manager.
2. Skapa ett virtuellt nätverk och undernät offentliga IP-Adressen för Programgatewayen. Skapa ett annat undernät för API-hantering.
3. Skapa en API Management-tjänst i VNET-undernätet som skapade ovan och se till att du använder intern-läget.
4. Konfigurera det anpassade domännamnet i API Management-tjänsten.
5. Skapa ett konfigurationsobjekt för Application Gateway.
6. Skapa en Programgateway resurs.
7. Skapa en CNAME-post från det offentliga DNS-namnet för Programgateway till API-hantering proxy värdnamn.

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

Kontrollera att du använder den senaste versionen av Azure PowerShell. Mer information finns i [Använda Windows PowerShell med Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/powershell-azure-resource-manager).

### <a name="step-1"></a>Steg 1

Logga in på Azure

```powershell
Login-AzureRmAccount
```

Autentisera med dina autentiseringsuppgifter.<BR>

### <a name="step-2"></a>Steg 2

Kontrollera prenumerationerna för kontot och markera den.

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>Steg 3

Skapa en resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för Programgateway

I följande exempel visas hur du skapar ett virtuellt nätverk med resursen manager.

### <a name="step-1"></a>Steg 1

Tilldela variabeln undernät som ska användas för Programgateway när du skapar ett virtuellt nätverk adressintervallet 10.0.0.0/24.

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Steg 2

Tilldela adressintervallet 10.0.1.0/24 variabeln undernät som ska användas för API Management när du skapar ett virtuellt nätverk.

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Steg 3

Skapa ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **apim-appGw-RG** för regionen USA, västra med prefixet 10.0.0.0/16 med undernät 10.0.0.0/24 och 10.0.1.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Steg 4

Tilldela en variabel för undernätet i nästa steg

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Skapa en API Management-tjänst i ett virtuellt nätverk som konfigurerats i internt läge

I följande exempel visas hur du skapar en API Management-tjänst i ett virtuellt nätverk som konfigurerats för intern åtkomst.

### <a name="step-1"></a>Steg 1
Skapa ett virtuellt nätverk för API Management-objekt med undernätet $apimsubnetdata skapade ovan.

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>Steg 2
Skapa en API Management-tjänsten i det virtuella nätverket.

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```
När ovanstående kommando lyckas avser [DNS-konfiguration som krävs för att komma åt interna virtuella nätverk API Management-tjänsten](api-management-using-with-internal-vnet.md#apim-dns-configuration) att komma åt den.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Ställa in ett anpassat domännamn i API Management

### <a name="step-1"></a>Steg 1
Överför certifikatet med privat nyckel för domänen. Det här exemplet blir `*.contoso.net`. 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>Steg 2
När certifikatet har överförts, skapa ett konfigurationsobjekt för värdnamn för proxy med ett värdnamn för `api.contoso.net`, enligt exemplet certifikatet ger behörighet för den `*.contoso.net` domän. 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs **publicIP01** i resursgruppen **apim-appGw-RG** för regionen USA, västra.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-application-gateway-configuration"></a>Skapa program gateway-konfiguration

Alla konfigurationsobjekt måste konfigureras innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet **gatewayIP01**. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Steg 2

Konfigurera frontend IP-port för den offentliga IP-slutpunkten. Den här porten är den port som användarna ansluter till.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>Steg 3

Konfigurera klientdelens IP med den offentliga IP-slutpunkten.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Steg 4

Konfigurera certifikatet för Programgateway används för att dekryptera och kryptera trafik som passerar genom.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>Steg 5

Skapa HTTP-lyssnare för Programgatewayen. Tilldela frontend IP-konfiguration, port och ssl-certifikatet till den.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>Steg 6

Skapa en anpassad avsökning till API Management-tjänsten `ContosoApi` proxy domän slutpunkt. Sökvägen `/status-0123456789abcdef` är en standardslutpunkt hälsa som är värd för API Management-tjänster. Ange `api.contoso.net` som ett värdnamn för anpassad avsökningsåtgärd för säker med SSL-certifikat.

> [!NOTE]
> Värdnamnet `contosoapi.azure-api.net` är standard proxy värdnamnet konfigureras när en tjänst med namnet `contosoapi` skapas i offentliga Azure. 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Steg 7

Överför certifikatet som ska användas på resurserna som SSL-aktiverade backend-pool. Det här är samma certifikat som du angav i steg 4 ovan.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>Steg 8

Konfigurera inställningar för HTTP-serverdelen för Programgateway. Detta innefattar att ställa in en timeout-gränsen för backend-begäran som de avbryts. Det här värdet skiljer sig från avsökningen timeout-värdet.

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Steg 9

Konfigurera backend-IP-adresspool med namnet **apimbackend** med den interna virtuella IP-adressen för API Management-tjänsten skapade ovan.

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>Steg 10

Skapa inställningar för en dummy (obefintligt)-serverdel. Begäranden till API-sökvägar som vi inte vill exponera från API-hantering via Programgateway nått den här backend och returnera 404.

Konfigurera HTTP-inställningar för dummy backend.

```powershell
$dummyBackendSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "dummySetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Konfigurera en dummy serverdel **dummyBackendPool**, som pekar på ett FQDN-adressen **dummybackend.com**. Den här FQDN-adressen finns inte i det virtuella nätverket.

```powershell
$dummyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "dummyBackendPool" -BackendFqdns "dummybackend.com"
```

Skapa en regel inställning som Application Gateway ska använda som standard som pekar på obefintligt serverdelen **dummybackend.com** i det virtuella nätverket.

```powershell
$dummyPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "nonexistentapis" -Paths "/*" -BackendAddressPool $dummyBackendPool -BackendHttpSettings $dummyBackendSetting
```

### <a name="step-11"></a>Steg 11

Konfigurera URL: en regel sökvägar för backend-pooler. Detta gör att välja endast en del av API: er i API-hantering för att exponeras för allmänheten. Till exempel om det finns `Echo API` (echo-), `Calculator API` (/calc/) o.s.v. Kontrollera endast `Echo API` tillgänglig från Internet). 

I följande exempel skapas en enkel regel för ”echo-” sökväg routning trafiken till backend ”apimProxyBackendPool”.

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
```

Om sökvägen inte matchar sökvägsregler som vi vill aktivera API-hantering, konfigurerar regelkonfigurationen sökväg kartan också en standard backend-adresspool med namnet **dummyBackendPool**. Till exempel http://api.contoso.net/calc/ * går till **dummyBackendPool** som har definierats som standard för icke matchade trafik.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule, $dummyPathRule -DefaultBackendAddressPool $dummyBackendPool -DefaultBackendHttpSettings $dummyBackendSetting
```

Ovanstående steg garanterar som endast begäranden om sökvägen ”/ echo” tillåts via Programgatewayen. Förfrågningar till andra API: er som konfigurerats i API Management genereras 404-fel från Programgateway när nås från Internet. 

### <a name="step-12"></a>Steg 12

Skapa en regel inställning för Programgateway att använda URL-sökväg-baserad routning.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-13"></a>Steg 13

Konfigurera antalet instanser och storlek för Programgatewayen. Här använder vi den [Brandvägg SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) för ökad säkerhet för API Management-resurs.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-14"></a>Steg 14

Konfigurera Brandvägg i ”förebyggande” läge.
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Skapa Programgateway

Skapa en Programgateway med alla konfigurationsobjekt från föregående steg.

```powershell
$appgw = New-AzureRmApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName  -Location $location -BackendAddressPools $apimProxyBackendPool, $dummyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $dummyBackendSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME värdnamnet för API Management-proxy till det offentliga DNS-namnet på resursen Programgateway

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP-adress, kräver en dynamiskt tilldelad DNS-namn, vilket inte kanske är enkla att använda Application Gateway. 

Den Programgateway DNS-namn som ska användas för att skapa en CNAME-post som pekar APIM proxyvärdnamn (t.ex. `api.contoso.net` i exemplen ovan) till DNS-namnet. Hämta information om Application Gateway och dess associerade IP DNS-namn med PublicIPAddress-element för att konfigurera klientdelens IP-CNAME-post. Användning av A-poster rekommenderas inte eftersom VIP ändras vid omstart för gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"></a> Sammanfattning
Azure API-hantering som konfigurerats i ett virtuellt nätverk innehåller en enda gateway-gränssnitt för alla konfigurerade API: er, oavsett om de är värdbaserade lokalt eller i molnet. Integrera Programgateway med API Management ger flexibilitet för att selektivt aktivera specifika API: er ska vara tillgänglig på Internet, samt att tillhandahålla en brandvägg för webbaserade program som en klientdel till API Management-instans.

##<a name="next-steps"></a> Nästa steg
* Lär dig mer om Azure Programgateway
  * [Översikt över Gateway](../application-gateway/application-gateway-introduction.md)
  * [Programmet Gateway Brandvägg för webbaserade program](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Programgateway med sökväg-baserade Routning](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Lär dig mer om API-hantering och Vnet
  * [Använda API Management tillgänglig endast inom VNET](api-management-using-with-internal-vnet.md)
  * [Med hjälp av API-hantering i virtuella nätverk](api-management-using-with-vnet.md)
