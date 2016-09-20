<properties
   pageTitle="Skapa och konfigurera en programgateway med en intern belastningsutjämnare (ILB) med hjälp av Azure Resource Manager | Microsoft Azure"
   description="Den här sidan innehåller anvisningar för hur du skapar, konfigurerar, startar och tar bort en Azure-programgateway med en intern belastningsutjämnare (ILB) med hjälp av Azure Resource Manager"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# Skapa en programgateway med en intern belastningsutjämnare (ILB) med hjälp av Azure Resource Manager

> [AZURE.SELECTOR]
- [Steg för den klassiska Azure-portalen](application-gateway-ilb.md)
- [Steg för PowerShell med Resource Manager](application-gateway-ilb-arm.md)

Azure Application Gateway kan konfigureras med en Internetuppkopplad VIP eller med en intern slutpunkt som inte är exponerad för Internet, även kallad en ILB-slutpunkt (intern belastningsutjämnare). Det kan vara praktiskt att konfigurera gatewayen med en ILB för interna affärsprogram som inte är exponerade för Internet. Det är också användbart för tjänster och nivåer i ett affärsprogram med flera nivåer som finns vid en säkerhetsgräns som inte är exponerad för Internet men som fortfarande kräver distribution med resursallokering (round-robin), sessionsvaraktighet eller SSL-avslut (Secure Sockets Layer).

Den här artikeln beskriver steg för steg hur du konfigurerar en programgateway med en ILB.

## Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Du ska skapa ett virtuellt nätverk och ett undernät för Application Gateway. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Application Gateway måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för användning av programgatewayen måste finnas i det virtuella nätverket eller ha slutpunkter som skapats där eller tilldelats en offentlig IP-/VIP-adress.

## Vad krävs för att skapa en programgateway?


- **Backend-serverpool:** Listan med IP-adresser för backend-servrarna. IP-adresserna som anges måste antingen höra till det virtuella nätverket men i ett annat undernät för programgatewayen eller vara en offentlig IP/VIP.
- **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
- **Frontend-port:** Den här porten är den offentliga porten som är öppen på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
- **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
- **Regel:** Regeln binder lyssnaren och backend-serverpoolen och definierar vilken backend-serverpool som trafiken ska dirigeras till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.



## Skapa en programgateway

Skillnaden mellan att använda den klassiska Azure-portalen och Azure Resource Manager är i vilken ordning du skapar programgatewayen och de objekt som ska konfigureras.
Med Resource Manager konfigureras alla objekt som bildar en programgateway separat och sätts sedan ihop för att skapa en programgatewayresurs.


Här följer de steg som krävs för att skapa en programgateway:

1. Skapa en resursgrupp för Resource Manager
2. Skapa ett virtuellt nätverk och ett undernät för programgatewayen
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

Skapa en ny resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager kräver att alla resursgrupper definierar en plats. Den här platsen används som standardplats för resurser i resursgruppen. Se till att alla kommandon du använder för att skapa en programgateway använder samma resursgrupp.

I exemplet ovan skapade vi resursgruppen ”appgw-rg” och platsen ”West US”.

## Skapa ett virtuellt nätverk och ett undernät för programgatewayen

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager:

### Steg 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Den här koden tilldelar adressintervallet 10.0.0.0/24 till en undernätsvariabel som ska användas för att skapa ett virtuellt nätverk.

### Steg 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Den här koden skapar ett virtuellt nätverk med namnet ”appgwvnet” i resursgruppen ”appgw-rg” för regionen West US med prefixet 10.0.0.0/16 och undernätet 10.0.0.0/24.

### Steg 3

    $subnet = $vnet.subnets[0]

Den här koden tilldelar undernätsobjektet till variabeln $subnet för nästa steg.

## Skapa ett konfigurationsobjekt för programgatewayen

### Steg 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Den här koden skapar en IP-konfiguration för programgatewayen med namnet ”gatewayIP01”. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.


### Steg 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Den här koden konfigurerar backend-IP-adresspoolen med namnet ”pool01” med IP-adresserna ”134.170.185.46, 134.170.188.221,134.170.185.50”. Det här är IP-adresserna som tar emot nätverkstrafiken som kommer från frontend-IP-slutpunkten. Du ersätter IP-adresserna ovan och lägger till ditt eget programs IP-adresslutpunkter.

### Steg 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Den här koden konfigurerar programgatewayinställningen ”poolsetting01” för den belastningsutjämnade nätverkstrafiken i backend-poolen.

### Steg 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Den här koden konfigurerar en frontend-IP-port med namnet ”frontendport01” för ILB.

### Steg 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Den här koden skapar en frontend IP-konfiguration med namnet ”fipconfig01” och associerar den med en privat IP-adress från det aktuella virtuella nätverkets undernät.

### Steg 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Den här koden skapar en lyssnare med namnet ”listener01” och associerar frontend-porten med frontend-IP-konfigurationen.

### Steg 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Den här koden skapar en routningsregel för belastningsutjämnaren med namnet ”rule01” som konfigurerar belastningsutjämnarens beteende.

### Steg 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Den här koden konfigurerar programgatewayens instansstorlek.

>[AZURE.NOTE]  Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Du kan välja mellan Standard_Small, Standard_Medium och Standard_Large.

## Skapa en programgateway med hjälp av New-AzureApplicationGateway

Skapar en programgateway med alla konfigurationsobjekt från stegen ovan. I det här exemplet heter programgatewayen ”appgwtest”.


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Den här koden skapar en programgateway med alla konfigurationsobjekt från stegen ovan. I det här exemplet heter programgatewayen ”appgwtest”.


## Ta bort en programgateway

Om du vill ta bort en programgateway måste du göra följande i angiven ordning:

1. Använd cmdleten **Stop-AzureRmApplicationGateway** om du vill stoppa gatewayen.
2. Använd cmdleten **Remove-AzureRmApplicationGateway** om du vill ta bort gatewayen.
3. Kontrollera att gatewayen har tagits bort med hjälp av cmdleten **Get-AzureApplicationGateway**.


### Steg 1

Hämta objektet för programgatewayen och associera det med variabeln ”$getgw”.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Steg 2

Använd cmdleten **Stop-AzureRmApplicationGateway** om du vill stoppa programgatewayen. Det här exemplet illustrerar cmdleten **Stop-AzureRmApplicationGateway** på den första raden, följt av utdata.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

När programgatewayens status är Stopped tar du bort tjänsten med hjälp av cmdleten **Remove-AzureRmApplicationGateway**.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] Du kan använda växeln **-force** om du inte vill att några bekräftelsemeddelanden ska visas.


Om du vill kontrollera att tjänsten har tagits bort kan du använda cmdleten **Get-AzureRmApplicationGateway**. Det här steget är inte obligatoriskt.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## Nästa steg

Om du vill konfigurera SSL-avlastning läser du [Konfigurera en programgateway för SSL-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en programgateway för användning med en intern belastningsutjämnare läser du [Skapa en programgateway med en intern belastningsutjämnare (ILB)](application-gateway-ilb.md).

Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=sep16_HO1-->


