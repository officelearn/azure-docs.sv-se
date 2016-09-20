<properties
   pageTitle="Skapa, starta eller ta bort en programgateway med hjälp av Azure Resource Manager | Microsoft Azure"
   description="Den här sidan innehåller anvisningar för hur du skapar, konfigurerar, startar och tar bort en programgateway i Azure med hjälp av Azure Resource Manager"
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


# Skapa, starta eller ta bort en programgateway med Azure Resource Manager

Azure Application Gateway är en Layer 7-belastningsutjämnare. Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt. Application Gateway tillhandahåller följande funktioner för programleverans: HTTP-belastningsutjämning, cookiebaserad sessionstillhörighet och SSL-avlastning (Secure Sockets Layer).


> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-gateway-portal.md)
- [PowerShell och Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [PowerShell och den klassiska Azure-portalen](application-gateway-create-gateway.md)
- [Azure Resource Manager-mall](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

<BR>


Den här artikeln beskriver steg för steg hur du skapar, konfigurerar, startar och tar bort en programgateway.


>[AZURE.IMPORTANT] Innan du börjar arbeta med Azure-resurser är det viktigt att du förstår att Azure för närvarande har två distributionsmodeller: Resource Manager och klassisk. Det är viktigt att du förstår [distributionsmodellerna och verktygen](../azure-classic-rm.md) innan du börjar arbeta med Azure-resurser. Du kan granska dokumentationen för olika verktyg genom att klicka på flikarna överst i den här artikeln. I det här dokumentet beskrivs hur du kan skapa en programgateway med hjälp av Azure Resource Manager. Om du vill använda den klassiska versionen går du till [Skapa en programgateway med den klassiska programdistributionen med hjälp av PowerShell](application-gateway-create-gateway.md).



## Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Om du har ett befintligt virtuellt nätverk väljer du antingen ett befintligt tomt undernät eller skapar ett undernät i det befintliga virtuella nätverket som enbart är avsett för att användas av programgatewayen. Du kan inte distribuera programgatewayen till något annat virtuellt nätverk än vad de resurser som du avser att distribuera bakom programgatewayen tillåter. 
3. De servrar som du konfigurerar för användning av programgatewayen måste finnas i det virtuella nätverket eller ha slutpunkter som skapats där eller tilldelats en offentlig IP-/VIP-adress.

## Vad krävs för att skapa en programgateway?


- **Backend-serverpool:** Listan med IP-adresser för backend-servrarna. IP-adresserna som anges bör antingen tillhöra det virtuella undernätet eller vara en offentlig IP-/VIP-adress.
- **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
- **Frontend-port:** Den här porten är den offentliga porten som är öppen på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
- **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
- **Regel:** Regeln binder lyssnaren och backend-serverpoolen och definierar vilken backend-serverpool som trafiken ska dirigeras till när den når en viss lyssnare. 



## Skapa en programgateway

Skillnaden mellan att använda den klassiska Azure-portalen och Azure Resource Manager är i vilken ordning du skapar programgatewayen och de objekt som ska konfigureras.

Med Resource Manager konfigureras alla objekt som bildar en programgateway separat och sätts sedan ihop för att skapa en programgatewayresurs.


I det följande anges de steg som krävs för att skapa en programgateway.

## Skapa en resursgrupp för Resource Manager

Kontrollera att du använder den senaste versionen av Azure PowerShell. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

### Steg 1
Logga in till Azure Login-AzureRmAccount

Du ombeds att autentisera dig med dina autentiseringsuppgifter.<BR>
### Steg 2
Kontrollera prenumerationerna för kontot.

        Get-AzureRmSubscription

### Steg 3
Välj vilka av dina Azure-prenumerationer som du vill använda. <BR>

        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Steg 4
Skapa en ny resursgrupp (hoppa över detta steg om du använder en befintlig resursgrupp).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager kräver att alla resursgrupper definierar en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Se till att alla kommandon du använder för att skapa en programgateway använder samma resursgrupp.

I exemplet ovan skapade vi resursgruppen ”appgw-RG” och platsen ”West US”.

>[AZURE.NOTE] Om du behöver konfigurera en anpassad avsökning för din programgateway läser du [Skapa en programgateway med anpassade avsökningar med hjälp av PowerShell](application-gateway-create-probe-ps.md). Mer information finns i [Anpassade avsökningar och hälsoövervakning](application-gateway-probe-overview.md).



## Skapa ett virtuellt nätverk och ett undernät för programgatewayen

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager.

### Steg 1

Tilldela adressintervallet 10.0.0.0/24 till undernätsvariabeln som ska användas för att skapa ett virtuellt nätverk.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Steg 2

Skapa ett virtuellt nätverk med namnet ”appgwvnet” i resursgruppen ”appgw-rg” för regionen West US med prefixet 10.0.0.0/16 och undernätet 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Steg 3

Tilldela en undernätsvariabel för nästa steg, som skapar en programgateway.

    $subnet=$vnet.Subnets[0]

## Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs, ”publicIP01”, i resursgruppen ”appgw-rg” för regionen West US.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Skapa ett konfigurationsobjekt för programgatewayen

Du måste konfigurera alla konfigurationsobjekt innan du skapar programgatewayen. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet ”gatewayIP01”. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.


    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Steg 2

Konfigurera backend-IP-adresspoolen med namnet ”pool01” med IP-adresserna ”134.170.185.46, 134.170.188.221,134.170.185.50”. De här IP-adreserna är de IP-adresser som tar emot den nätverkstrafik som kommer från frontend-IP-slutpunkten. Du ersätter de omnämnda IP-adresserna och lägger till ditt eget programs IP-adresslutpunkter.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Steg 3

Konfigurera programgatewayinställningen ”poolsetting01” för den belastningsutjämnade nätverkstrafiken i backend-poolen.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Steg 4

Konfigurera frontend-IP-porten med namnet ”frontendport01” för den offentliga IP-adressens slutpunkt.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Steg 5

Skapa frontend-IP-konfigurationen med namnet ”fipconfig01” och associera den offentliga IP-adressen med frontend-IP-konfigurationen.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Steg 6

Skapa lyssnaren med namnet ”listener01” och associera frontend-porten till frontend-IP-konfigurationen.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Steg 7

Skapa en hanteringsregel för belastningsutjämnaren med namnet ”rule01” som konfigurerar belastningsutjämnarens beteende.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Steg 8

Konfigurera programgatewayens instansstorlek.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Du kan välja mellan Standard_Small, Standard_Medium och Standard_Large.

## Skapa en programgateway med hjälp av New-AzureRmApplicationGateway

Skapa en programgateway med alla konfigurationsobjekt från föregående steg. I det här exemplet heter programgatewayen ”appgwtest”.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### Steg 9
Hämta DNS- och VIP-information för programgatewayen från den offentliga IP-resurs som är kopplad till programgatewayen.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

    Name                     : publicIP01
    ResourceGroupName        : appgwtest 
    Location                 : westus
    Id                       : /subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"12302060-78d6-4a33-942b-a494d6323767"
    ResourceGuid             : ee9gd76a-3gf6-4236-aca4-gc1f4gf14171
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : 137.116.26.16
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fipconfig01"
                               }
    DnsSettings              : {
                                 "Fqdn": "ee7aca47-4344-4810-a999-2c631b73e3cd.cloudapp.net"
                               } 



## Ta bort en programgateway

Följ dessa anvisningar om du vill ta bort en programgateway:

### Steg 1

Hämta objektet för programgatewayen och associera det med variabeln ”$getgw”.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Steg 2

Använd cmdleten **Stop-AzureRmApplicationGateway** om du vill stoppa programgatewayen.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


När programgatewayens status är Stopped tar du bort tjänsten med hjälp av cmdleten **Remove-AzureRmApplicationGateway**.


    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] Du kan använda växeln **-force** om du inte vill att några bekräftelsemeddelanden ska visas.


Om du vill kontrollera att tjänsten har tagits bort kan du använda cmdleten **Get-AzureRmApplicationGateway**. Det här steget är inte obligatoriskt.


    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Nästa steg

Om du vill konfigurera SSL-avlastning läser du [Konfigurera en programgateway för SSL-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en programgateway för användning med en intern belastningsutjämnare läser du [Skapa en programgateway med en intern belastningsutjämnare (ILB)](application-gateway-ilb.md).

Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=sep16_HO1-->


