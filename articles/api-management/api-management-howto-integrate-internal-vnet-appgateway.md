---
title: Så här använder du API Management i virtuellt nätverk med Application Gateway
titleSuffix: Azure API Management
description: Lär dig hur du konfigurerar och konfigurerar Azure API Management i internt virtuellt nätverk med Application Gateway (WAF) som FrontEnd
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 2b8cf66afa1d8aa592d5755ebab70cd6ad2e75fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298067"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrera API-hantering i ett internt VNET med Application Gateway

## <a name="overview"></a><a name="overview"> </a> Översikt

API Management-tjänsten kan konfigureras i ett virtuellt nätverk i internt läge, vilket gör den endast tillgänglig från det virtuella nätverket. Azure Application Gateway är en PAAS-tjänst som tillhandahåller en belastningsutjämnare för Layer-7. Det fungerar som en omvänd proxy tjänst och ger bland sitt erbjudande en webbprogram Brandvägg (WAF).

Genom att kombinera API Management-etablerat i ett internt VNET med klientdelen Application Gateway kan följande scenarier:

* Använd samma API Management-resurs för förbrukning av både interna konsumenter och externa konsumenter.
* Använd en enda API Management-resurs och har en delmängd API:er som definierats i API-hantering som är tillgänglig för externa konsumenter.
* Ange ett nyckelfärdigt sätt att växla åtkomst till API-hantering från det offentliga Internet till och från.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill följa stegen som beskrivs i den här artikeln måste du ha:

* En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certifikat - pfx och cer för API-värdnamnet och pfx för utvecklarportalens värdnamn.

## <a name="scenario"></a><a name="scenario"> </a> Scenario

Den här artikeln beskriver hur du använder en enda API Management-tjänst för både interna och externa konsumenter och gör det fungera som en enda frontend för både lokala och moln-API:er. Du kommer också att se hur du bara exponerar en delmängd av dina API:er (i exemplet är de markerade i grönt) för extern förbrukning med hjälp av routningsfunktioner som är tillgängliga i Programgateway.

I det första installationsexemplet hanteras alla API:er endast från det virtuella nätverket. Interna konsumenter (markerade i orange) kan komma åt alla dina interna och externa API:er. Trafiken går aldrig ut till Internet. Högpresterande anslutning levereras via Express Route-kretsar.

![url-rutt](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a> Innan du börjar

* Kontrollera att du använder den senaste versionen av Azure PowerShell. Se installationsinstruktionerna på [Installera Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Vad krävs för att skapa en integrering mellan API Management och Application Gateway?

* **Serverpool för server med slut:** Det här är den interna virtuella IP-adressen för API Management-tjänsten.
* **Inställningar för serverpool för serverserver:** Varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. Dessa inställningar tillämpas på alla servrar i poolen.
* **Front-end-port:** Det här är den offentliga porten som öppnas på programgatewayen. Trafik slår det får omdirigeras till en av backend-servrar.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https, dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om ssl-avlastning konfigureras).
* **Regel:** Regeln binder en lyssnare till en serverpool med en server.
* **Anpassad hälsoavsökning:** Application Gateway använder som standard IP-adressbaserade avsökningar för att ta reda på vilka servrar i BackendAddressPool som är aktiva. API Management-tjänsten svarar bara på begäranden med rätt värdhuvud, därav standardavsökningarna misslyckas. En anpassad hälsoavsökning måste definieras för att hjälpa programgateway att avgöra att tjänsten är vid liv och den bör vidarebefordra begäranden.
* **Anpassade domäncertifikat:** Om du vill komma åt API Management från internet måste du skapa en CNAME-mappning av dess värdnamn till klientnamnet Application Gateway. Detta säkerställer att värdnamnshuvudet och certifikatet som skickas till Application Gateway som vidarebefordras till API Management är en APIM kan känna igen som giltig. I det här exemplet använder vi två certifikat - för serverda och för utvecklarportalen.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a> Steg som krävs för att integrera API Management och Application Gateway

1. Skapa en resursgrupp för Resource Manager.
2. Skapa ett virtuellt nätverk, ett undernät och en offentlig IP för programgatewayen. Skapa ett annat undernät för API Management.
3. Skapa en API Management-tjänst i det virtuella nätverksundernätet som skapats ovan och se till att du använder det interna läget.
4. Konfigurera ett anpassat domännamn i API Management-tjänsten.
5. Skapa ett konfigurationsobjekt för Application Gateway.
6. Skapa en application gateway-resurs.
7. Skapa ett CNAME från det offentliga DNS-namnet för programgatewayen till API Management proxyvärden.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Exponera utvecklarportalen externt via Application Gateway

I den här guiden kommer vi också att exponera **utvecklarportalen** för externa målgrupper via Application Gateway. Det kräver ytterligare steg för att skapa utvecklarportalens lyssnare, avsökning, inställningar och regler. Alla detaljer finns i respektive steg.

> [!WARNING]
> Om du använder Azure AD- eller tredjepartsautentisering aktiverar du [cookie-baserad sessionstillhörighetsfunktion](../application-gateway/features.md#session-affinity) i Application Gateway.

> [!WARNING]
> Om du vill förhindra att Application Gateway WAF bryter ned hämtningen av `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`OpenAPI-specifikationen i utvecklarportalen måste du inaktivera brandväggsregeln .

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

### <a name="step-1"></a>Steg 1

Logga in på Azure

```powershell
Connect-AzAccount
```

Autentisera med dina referenser.

### <a name="step-2"></a>Steg 2

Välj önskad prenumeration.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Steg 3

Skapa en resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurser i resursgruppen. Kontrollera att alla kommandon för att skapa en programgateway använder samma resursgrupp.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

I följande exempel visas hur du skapar ett virtuellt nätverk med Hjälp av Resource Manager.

### <a name="step-1"></a>Steg 1

Tilldela adressintervallet 10.0.0.0/24 till den undernätsvariabel som ska användas för Application Gateway när du skapar ett virtuellt nätverk.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Steg 2

Tilldela adressintervallet 10.0.1.0/24 till den undernätsvariabel som ska användas för API-hantering när du skapar ett virtuellt nätverk.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Steg 3

Skapa ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **apim-appGw-RG** för regionen Västra USA. Använd prefixet 10.0.0.0/16 med undernäten 10.0.0.0/24 och 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Steg 4

Tilldela en undernätsvariabel för nästa steg

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Skapa en API Management-tjänst i ett VNET som konfigurerats i internt läge

I följande exempel visas hur du skapar en API Management-tjänst i ett VNET som endast konfigurerats för intern åtkomst.

### <a name="step-1"></a>Steg 1

Skapa ett virtuellt nätverk för API Management med hjälp av undernätet $apimsubnetdata som skapats ovan.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Steg 2

Skapa en API Management-tjänst i det virtuella nätverket.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

När kommandot ovan lyckas referera till [DNS-konfiguration som krävs för att komma åt interna VNET API Management-tjänsten](api-management-using-with-internal-vnet.md#apim-dns-configuration) för att komma åt den. Det här steget kan ta mer än en halvtimme.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurera ett eget domännamn i API Management

> [!IMPORTANT]
> Den [nya utvecklarportalen](api-management-howto-developer-portal.md) kräver också att anslutningen till API-hanteringens slutpunkt aktiveras utöver stegen nedan.

### <a name="step-1"></a>Steg 1

Initiera följande variabler med information om certifikaten med privata nycklar för domänerna. I det här exemplet `api.contoso.net` `portal.contoso.net`kommer vi att använda och .  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Steg 2

Skapa och ange konfigurationsobjekt för värdnamn för proxyn och för portalen.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Om du vill konfigurera den äldre `-HostnameType DeveloperPortal` utvecklarportalanslutningen måste du ersätta med `-HostnameType Portal`.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs **publicIP01** i resursgruppen.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-application-gateway-configuration"></a>Skapa konfiguration av programgateway

Alla konfigurationsobjekt måste konfigureras innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgateway med namnet **gatewayIP01**. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Steg 2

Konfigurera ip-porten för frontend för den offentliga IP-slutpunkten. Den här porten är den port som slutanvändare ansluter till.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Steg 3

Konfigurera klientdelens IP med den offentliga IP-slutpunkten.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Steg 4

Konfigurera certifikaten för Programgatewayen, som ska användas för att dekryptera och kryptera om trafiken som passerar.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Steg 5

Skapa HTTP-lyssnarna för programgatewayen. Tilldela frontend IP-konfiguration, port och ssl-certifikat till dem.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Steg 6

Skapa anpassade avsökningar till `ContosoApi` API Management-tjänstproxy domänslutpunkten. Sökvägen `/status-0123456789abcdef` är en standardhälsoslutpunkt som finns på alla API Management-tjänster. Ange `api.contoso.net` som ett anpassat avsökningsvärdnamn för att skydda det med SSL-certifikat.

> [!NOTE]
> Värdnamnet `contosoapi.azure-api.net` är standardprogramnamnet för proxy som `contosoapi` konfigurerats när en tjänst som heter skapas i offentliga Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Steg 7

Ladda upp certifikatet som ska användas på de SSL-aktiverade serverda poolresurserna. Detta är samma certifikat som du angav i steg 4 ovan.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Steg 8

Konfigurera HTTP-serverningsinställningar för Programgateway. Detta inkluderar att ange en time-out-gräns för backend-begäran, varefter de avbryts. Det här värdet skiljer sig från time-outen av avsökningen.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Steg 9

Konfigurera en IP-adresspool med namnet **apimbackend** med den interna virtuella IP-adressen för API Management-tjänsten som skapats ovan.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Steg 10

Skapa regler för programgatewayen för att använda grundläggande routning.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Ändra -RuleType och routning, för att begränsa åtkomsten till vissa sidor i utvecklarportalen.

### <a name="step-11"></a>Steg 11

Konfigurera antalet instanser och storlek för Programgatewayen. I det här exemplet använder vi [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) för ökad säkerhet för API Management-resursen.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Steg 12

Konfigurera WAF så att den är i "Förebyggande" läge.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Skapa programgateway

Skapa en programgateway med alla konfigurationsobjekt från föregående steg.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME API Management proxy värdnamn till det offentliga DNS-namnet på Application Gateway-resursen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP kräver Application Gateway ett dynamiskt tilldelat DNS-namn, vilket kanske inte är lätt att använda.

Application Gateways DNS-namn ska användas för att skapa en CNAME-post som pekar `api.contoso.net` APIM-proxyvärdens namn (t.ex. i exemplen ovan) till det här DNS-namnet. Om du vill konfigurera ip-CNAME-posten för klientsidan hämtar du information om Programgateway och dess associerade IP/DNS-namn med hjälp av elementet PublicIPAddress. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a> Sammanfattning
Azure API Management som konfigurerats i ett VNET tillhandahåller ett enda gateway-gränssnitt för alla konfigurerade API:er, oavsett om de finns lokalt eller i molnet. Genom att integrera Application Gateway med API Management kan du flexibiliteten att selektivt aktivera vissa API:er för att vara tillgängliga på Internet, samt att tillhandahålla en brandvägg för webbprogram som ett frontend till DIN API Management-instans.

## <a name="next-steps"></a><a name="next-steps"> </a> Nästa steg
* Läs mer om Azure Application Gateway
  * [Översikt över programgateway](../application-gateway/application-gateway-introduction.md)
  * [Brandvägg för webbprogram för programgateway](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Programgateway med hjälp av sökvägsbaserad routning](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Läs mer om API Management och VNETs
  * [Använda API-hantering endast tillgänglig i VNET](api-management-using-with-internal-vnet.md)
  * [Använda API Management i VNET](api-management-using-with-vnet.md)
