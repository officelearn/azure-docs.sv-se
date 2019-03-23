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
ms.date: 06/26/2018
ms.author: sasolank
ms.openlocfilehash: 46678590366021ff0f3ddb714d439c18addc578b
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361087"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrera API Management i ett internt virtuellt nätverk med Application Gateway

## <a name="overview"> </a> Översikt över

API Management-tjänsten kan konfigureras i ett virtuellt nätverk i interna läge, vilket gör att det är enbart tillgänglig från det virtuella nätverket. Azure Application Gateway är en PAAS-tjänst som tillhandahåller Layer 7-belastningsutjämning. Den fungerar som en omvänd proxy-tjänst och ger bland erbjuder en Web Application Firewall (WAF).

Kombinera API Management som etablerats i ett internt virtuellt nätverk med Application Gateway-klientdel kan följande scenarier:

* Använd samma API Management-resurs för användning av både interna konsumenter och externa konsumenter.
* Använd en enda API Management-resurs och har en delmängd av API: er som definierats i API Management som är tillgängliga för externa användare.
* Ger en nyckelfärdig sätt att aktivera åtkomst till API Management från det offentliga Internet och inaktivera.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill följa stegen som beskrivs i den här artikeln, måste du ha:

* En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certifikat - PFX-filen och cer för API-värdnamnet och pfx för developer-portalen värdnamn.

## <a name="scenario"> </a> Scenario

Den här artikeln beskriver hur du använder ett enda API Management-tjänsten för både interna och externa konsumenter och gör det fungerar som en enda klientdel för både lokalt och API: er i molnet. Du kan även se hur du kan exponera endast en delmängd av dina API: er (i exemplet som de är markerade i grönt) för extern användning med hjälp av Routning funktioner som är tillgängliga i Application Gateway.

I det första exemplet installationen hanteras alla API: er endast från inom ditt virtuella nätverk. Interna konsumenter (markerade i orange) kan komma åt alla dina interna och externa API: er. Trafiken överförs aldrig till Internet en högpresterande levereras via Expressroute-kretsar.

![URL: en väg](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> Innan du börjar

* Kontrollera att du använder den senaste versionen av Azure PowerShell. Se installationsinstruktionerna på [installera Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Vad krävs för att skapa en integration mellan API Management och Application Gateway?

* **Backend-serverpool:** Det här är den interna virtuella IP-adressen för API Management-tjänsten.
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. De här inställningarna tillämpas på alla servrar i poolen.
* **Klientdelsport:** Det här är den offentliga porten som är öppen på programgatewayen. Trafik det omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
* **Regel:** Regeln Binder en lyssnare till en backend-serverpoolen.
* **Anpassade Hälsoavsökning:** Application Gateway, använder som standard IP-adressbaserad avsökningar för att ta reda på vilka servrar i BackendAddressPool är aktiva. API-hanteringen tjänsten bara svarar på begäranden med rätt värdhuvudet är därför standard-avsökningar misslyckas. En anpassad hälsoavsökning måste definieras för att bestämma att tjänsten är aktiv och att den ska vidarebefordra begäranden Programgateway.
* **Anpassad Domäncertifikat:** För att komma åt API Management från internet, måste du skapa en CNAME-mappning av dess värdnamn till frontend-DNS-namn för Application Gateway. Detta säkerställer att värdnamnet huvud och certifikat som skickats till Application Gateway som vidarebefordras till API Management är en APIM kan identifiera som giltigt. I det här exemplet använder vi två certifikat – för serverdelen och developer-portalen.  

## <a name="overview-steps"> </a> Steg som krävs för att integrera API Management och Application Gateway

1. Skapa en resursgrupp för Resource Manager.
2. Skapa ett virtuellt nätverk, undernät och offentlig IP-adress för Application Gateway. Skapa ett annat undernät för API Management.
3. Skapa en API Management-tjänsten i VNET-undernät som skapades ovan och kontrollera att du använder det interna läget.
4. Konfigurera ett anpassat domännamn i API Management-tjänsten.
5. Skapa ett konfigurationsobjekt för Programgatewayen.
6. Skapa en Application Gateway-resurs.
7. Skapa en CNAME-post från det offentliga DNS-namnet på Application Gateway till värdnamn för API Management-proxy.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Exponera utvecklarportalen externt via Application Gateway

I den här handboken vi kommer också att innehålla den **utvecklarportalen** till externa publik via Application Gateway. Det krävs ytterligare steg för att skapa developer-portalen lyssnare, avsökning, inställningar och regler. All information finns i respektive steg.

> [!WARNING]
> Om du använder Azure AD eller tredjeparts-autentisering, aktivera [Cookiebaserad sessionstillhörighet](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity) funktion i Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

### <a name="step-1"></a>Steg 1

Logga in på Azure

```powershell
Connect-AzAccount
```

Autentisera med dina autentiseringsuppgifter.

### <a name="step-2"></a>Steg 2

Välj den önskade prenumerationen.

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

Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för application gateway

I följande exempel visar hur du skapar ett virtuellt nätverk med resource manager.

### <a name="step-1"></a>Steg 1

Tilldela adressintervallet 10.0.0.0/24 till undernätsvariabeln som ska användas för Application Gateway när du skapar ett virtuellt nätverk.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Steg 2

Tilldela adressintervallet 10.0.1.0/24 till undernätsvariabeln som ska användas för API-hantering när du skapar ett virtuellt nätverk.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Steg 3

Skapa ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **apim-appGw-RG** för regionen West US. Använd prefixet 10.0.0.0/16 med undernät 10.0.0.0/24 och 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Steg 4

Tilldela en undernätsvariabel för nästa steg

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Skapa en API Management-tjänst i ett virtuellt nätverk som konfigurerats i interna läge

I följande exempel visas hur du skapar en API Management-tjänsten i ett virtuellt nätverk som konfigurerats för intern åtkomst.

### <a name="step-1"></a>Steg 1

Skapa ett virtuellt nätverk för API Management-objekt med undernätet $apimsubnetdata skapade ovan.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Steg 2

Skapa en API Management-tjänsten i virtuella nätverk.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

När kommandot ovan lyckas referera till [DNS-konfiguration som krävs för att komma åt interna VNET API Management-tjänsten](api-management-using-with-internal-vnet.md#apim-dns-configuration) att komma åt den. Det här steget kan ta mer än en halvtimme.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Konfigurera ett anpassat domännamn i API Management

### <a name="step-1"></a>Steg 1

Initiera följande variabler med information om certifikaten med privata nycklar för domänerna. I det här exemplet ska vi använda `api.contoso.net` och `portal.contoso.net`.  

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

Skapa och ange värdnamnet konfigurationsobjekt för proxy och för portalen.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType Portal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-adressresurs **publicIP01** i resursgruppen.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-application-gateway-configuration"></a>Skapa konfiguration för programgatewayen

Alla konfigurationsobjekt måste konfigureras innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet **gatewayIP01**. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Steg 2

Konfigurera frontend IP-porten för den offentliga IP-slutpunkten. Den här porten är den port som användarna ansluta till.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Steg 3

Konfigurera klientdelens IP med den offentliga IP-slutpunkten.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Steg 4

Konfigurera certifikat för Application Gateway, som används för att dekryptera och kryptera trafik som passerar genom.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Steg 5

Skapa HTTP-lyssnare för Programgatewayen. Tilldela dem för de frontend IP-konfiguration, port och ssl certifikat.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Steg 6

Skapa anpassade avsökningar till API Management-tjänsten `ContosoApi` proxy domän slutpunkt. Sökvägen `/status-0123456789abcdef` är en standardslutpunkt för hälsotillstånd som finns i API Management-tjänster. Ange `api.contoso.net` som en anpassad avsökning värdnamnet till att skydda den med SSL-certifikat.

> [!NOTE]
> Värdnamnet `contosoapi.azure-api.net` är proxy standardvärdnamnet konfigureras när en tjänst med namnet `contosoapi` har skapats i offentliga Azure.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Steg 7

Ladda upp certifikat som ska användas för de resurser som SSL-aktiverad backend-pool. Är detta samma certifikat som du angav i steg 4 ovan.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Steg 8

Konfigurera inställningar för HTTP-serverdelen för Application Gateway. Detta innefattar att ställa in en timeoutgränsen för backend-begäran, varefter de är avbruten. Det här värdet skiljer sig från avsökningen timeout-värde.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Steg 9

Konfigurera backend-IP-adresspoolen med namnet **apimbackend** med den interna virtuella IP-adressen för API Management-tjänsten som skapades ovan.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Steg 10

Skapa regler för Application Gateway kan använda grundläggande routning.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Ändra regeltyp - och routning för att begränsa åtkomsten till vissa sidor i developer-portalen.

### <a name="step-11"></a>Steg 11

Konfigurera antalet instanser av och storleken för Application Gateway. I det här exemplet använder vi den [WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) för ökad säkerhet för API Management-resursen.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Steg 12

Konfigurera WAF i ”förhindringsläge”.

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Skapa Programgateway

Skapa en Programgateway med alla konfigurationsobjekt från föregående steg.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>CNAME för API Management proxy värdnamnet till det offentliga DNS-namnet på Application Gateway-resursen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP-adress, kräver ett dynamiskt tilldelat DNS-namn, vilket inte kanske är lätt att använda Application Gateway.

Programgatewayens DNS-namn som ska användas för att skapa en CNAME-post som pekar APIM-proxyvärdnamn (t.ex. `api.contoso.net` i exemplen ovan) till den här DNS-namnet. Konfigurera frontend IP-CNAME-post genom att hämta information om Programgatewayen och dess associerade IP/DNS-namn med PublicIPAddress-elementet. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"> </a> Sammanfattning
Azure API Management som konfigurerats i ett virtuellt nätverk ger en enda gateway-gränssnitt för alla konfigurerade API: er, oavsett om de finns lokalt eller i molnet. Integrera Application Gateway med API Management ger dig flexibilitet för att selektivt aktivera specifika API: er ska vara tillgänglig på Internet, samt att tillhandahålla en brandvägg för webbaserade program som en klientdel till API Management-instans.

## <a name="next-steps"> </a> Nästa steg
* Läs mer om Azure Application Gateway
  * [Översikt över Application Gateway](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway Web Application Firewall](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Programgateway med hjälp av sökvägsbaserad Routning](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Läs mer om API Management och virtuella nätverk
  * [Använda API Management tillgängligt endast inom det virtuella nätverket](api-management-using-with-internal-vnet.md)
  * [Använda API Management i ett virtuellt nätverk](api-management-using-with-vnet.md)
