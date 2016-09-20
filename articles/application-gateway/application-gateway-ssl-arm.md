<properties
   pageTitle="Konfigurera en programgateway för SSL-avlastning med hjälp av Azure Resource Manager | Microsoft Azure"
   description="Den här sidan innehåller anvisningar för hur du skapar en programgateway med SSL-avlastning med hjälp av Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>

# Konfigurera en programgateway för SSL-avlastning med hjälp av Azure Resource Manager

> [AZURE.SELECTOR]
-[Azure Portal](application-gateway-ssl-portal.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
-[Azure Classic PowerShell](application-gateway-ssl.md)

 Azure Application Gateway kan konfigureras att avsluta SSL-sessionen (Secure Sockets Layer) på gatewayen så att du undviker kostsamma SSL-dekrypteringsaktiviteter i webbservergruppen. SSL-avlastning förenklar också frontend-serverkonfigurationen och hanteringen av webbappen.


## Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Du skapar ett virtuellt nätverk och ett undernät för programgatewayen. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Application Gateway måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för användning av programgatewayen måste finnas eller ha slutpunkter som skapats i antingen det virtuella nätverket eller med en tilldelad offentlig IP-/VIP-adress.

## Vad krävs för att skapa en programgateway?


- **Backend-serverpool:** Listan med IP-adresser för backend-servrarna. IP-adresserna som anges bör antingen tillhöra det virtuella undernätet eller vara en offentlig IP-/VIP-adress.
- **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
- **Frontend-port:** Den här porten är den offentliga porten som är öppen på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
- **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa inställningar är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
- **Regel:** Regeln binder lyssnaren och backend-serverpoolen och definierar vilken backend-serverpool som trafiken ska dirigeras till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

**Ytterligare konfigurationsanmärkningar**

För konfiguration av SSL-certifikat bör protokollet i **HttpListener** ändras till *Https* (skiftlägeskänsligt). Elementet **SslCertificate** läggs till i **HttpListener** med variabelvärdet som har konfigurerats för SSL-certifikatet. Frontend-porten måste uppdateras till 443.

**Aktivera cookiebaserad tillhörighet**: En programgateway kan konfigureras att säkerställa att en begäran från en klientsession alltid dirigeras till samma virtuella dator i webbservergruppen. Detta görs med en sessions-cookie som ser till att gatewayen dirigerar trafiken på rätt sätt. Du kan aktivera cookiebaserad tillhörighet genom att ange **CookieBasedAffinity** till *Enabled* i elementet **BackendHttpSettings**.


## Skapa en programgateway

Skillnaden mellan att använda den klassiska Azure-distributionsmodellen och Azure Resource Manager är i vilken ordning du skapar en programgateway och de objekt som måste konfigureras.

Med Resource Manager konfigureras alla objekt som bildar en programgateway separat och sätts sedan ihop för att skapa en programgatewayresurs.


Här är de steg du följer när du skapar en programgateway:

1. Skapa en resursgrupp för Resource Manager
2. Skapa ett virtuellt nätverk, ett undernät och en offentlig IP för programgatewayen
3. Skapa ett konfigurationsobjekt för programgatewayen
4. Skapa en resurs för programgatewayen


## Skapa en resursgrupp för Resource Manager

Glöm inte att byta PowerShell-läge så att du kan använda cmdlets för Azure Resource Manager. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

### Steg 1

    Login-AzureRmAccount



### Steg 2

Kontrollera prenumerationerna för kontot.

    Get-AzureRmSubscription

Du ombeds att autentisera dig med dina autentiseringsuppgifter.<BR>

### Steg 3

Välj vilka av dina Azure-prenumerationer som du vill använda. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Steg 4

Skapa en resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure Resource Manager kräver att alla resursgrupper definierar en plats. Denna inställning används som standardplatsen för resurser i resursgruppen. Se till att alla kommandon du använder för att skapa en programgateway använder samma resursgrupp.

I exemplet ovan skapade vi resursgruppen ”appgw-RG” och platsen ”West US”.

## Skapa ett virtuellt nätverk och ett undernät för programgatewayen

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager:

### Steg 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Den här koden tilldelar adressintervallet 10.0.0.0/24 till en undernätsvariabel som ska användas för att skapa ett virtuellt nätverk.

### Steg 2
    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Den här koden skapar ett virtuellt nätverk med namnet ”appgwvnet” i resursgruppen ”appgw-rg” för regionen West US med prefixet 10.0.0.0/16 och undernätet 10.0.0.0/24.

### Steg 3

    $subnet = $vnet.Subnets[0]

Den här koden tilldelar undernätsobjektet till variabeln $subnet för nästa steg.

## Skapa en offentlig IP-adress för frontend-konfigurationen

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Den här koden skapar en offentlig IP-resurs med namnet ”publicIP01” i resursgruppen ”appgw-rg” för regionen West US.


## Skapa ett konfigurationsobjekt för programgatewayen

### Steg 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Den här koden skapar en IP-konfiguration för programgatewayen med namnet ”gatewayIP01”. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

### Steg 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Den här koden konfigurerar backend-IP-adresspoolen med namnet ”pool01” med IP-adresserna ”134.170.185.46, 134.170.188.221,134.170.185.50”. Det här är IP-adresserna som tar emot nätverkstrafiken som kommer från frontend-IP-slutpunkten. Ersätt IP-adresserna i exemplet ovan med IP-adresserna för slutpunkterna för din webbapp.

### Steg 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Den här koden konfigurerar programgatewayinställningen ”poolsetting01” för den belastningsutjämnade nätverkstrafiken i backend-poolen.

### Steg 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Den här koden konfigurerar frontend-IP-porten med namnet ”frontendport01” för den offentliga IP-adressens slutpunkt.

### Steg 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Den här koden konfigurerar certifikatet som används för SSL-anslutning. Certifikatet måste ha PFX-format och lösenordet måste innehålla mellan 4 och 12 tecken.

### Steg 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Den här koden skapar frontend-IP-konfigurationen med namnet ”fipconfig01” och associera den offentliga IP-adressen med frontend-IP-konfigurationen.

### Steg 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Den här koden skapar lyssnaren ”listener01” och associerar frontend-porten med frontend-IP-konfigurationen och certifikatet.

### Steg 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Den här koden skapar en routningsregel för belastningsutjämnaren med namnet ”rule01” som konfigurerar belastningsutjämnarens beteende.

### Steg 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Den här koden konfigurerar programgatewayens instansstorlek.

>[AZURE.NOTE]  Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Du kan välja mellan Standard_Small, Standard_Medium och Standard_Large.

## Skapa en programgateway med hjälp av New-AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Den här koden skapar en programgateway med alla konfigurationsobjekt från stegen ovan. I det här exemplet heter programgatewayen ”appgwtest”.

## Nästa steg

Om du vill konfigurera en programgateway för användning med en intern belastningsutjämnare läser du [Skapa en programgateway med en intern belastningsutjämnare (ILB)](application-gateway-ilb.md).

Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=sep16_HO1-->


