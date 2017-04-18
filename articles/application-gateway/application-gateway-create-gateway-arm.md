---
title: "Skapa och hantera Azure Application Gateway – PowerShell | Microsoft Docs"
description: "Den här sidan innehåller anvisningar för hur du skapar, konfigurerar, startar och tar bort en programgateway i Azure med hjälp av Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: 11ecfc993f17c89d4ac4431e9a835000d30afe76
ms.lasthandoff: 04/05/2017


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Skapa, starta eller ta bort en programgateway med Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-gateway.md)
> * [Azure Resource Manager-mall](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway är en Layer 7-belastningsutjämnare. Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt.
Application Gateway innehåller många ADC-funktioner (Application Delivery Controller), inklusive HTTP-belastningsutjämning, cookie-baserad sessionstilldelning, SSL-avlastning (Secure Sockets Layer), anpassade hälsoavsökningar, stöd för flera platser och mycket mer.

En fullständig lista över funktioner som stöds finns i [Översikt över Application Gateway](application-gateway-introduction.md)

Den här artikeln beskriver steg för steg hur du skapar, konfigurerar, startar och tar bort en programgateway.

> [!IMPORTANT]
> Innan du börjar arbeta med Azure-resurser är det viktigt att du förstår att Azure för närvarande har två distributionsmodeller: Resource Manager och klassisk. Det är viktigt att du förstår [distributionsmodellerna och verktygen](../azure-classic-rm.md) innan du börjar arbeta med Azure-resurser. Du kan granska dokumentationen för olika verktyg genom att klicka på flikarna överst i den här artikeln. I det här dokumentet beskrivs hur du kan skapa en programgateway med hjälp av Azure Resource Manager. Om du vill använda den klassiska versionen går du till [Skapa en programgateway med den klassiska programdistributionen med hjälp av PowerShell](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
1. Om du har ett befintligt virtuellt nätverk väljer du antingen ett befintligt tomt undernät eller skapar ett undernät i det befintliga virtuella nätverket som enbart är avsett för att användas av programgatewayen. Du kan inte distribuera programgatewayen till något annat virtuellt nätverk än vad de resurser som du avser att distribuera bakom programgatewayen tillåter.
1. De servrar som du konfigurerar för användning av programgatewayen måste finnas i det virtuella nätverket eller ha slutpunkter som skapats där eller tilldelats en offentlig IP-/VIP-adress.

## <a name="what-is-required-to-create-an-application-gateway"></a>Vad krävs för att skapa en programgateway?

* **Serverdelspool:** Listan med IP-adresser, fullständiga domännamn eller nätverkskort för backend-servrarna. Om IP-adresser används bör de antingen tillhöra det virtuella undernätet eller vara offentliga IP-/VIP-adresser.
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookiebaserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Frontend-port:** Den här porten är den offentliga porten som är öppen på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare:** Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och SSL-certifikatnamnet (om du konfigurerar SSL-avlastning).
* **Regel:** Regeln binder lyssnaren och backend-serverpoolen och definierar vilken backend-serverpool som trafiken ska dirigeras till när den når en viss lyssnare.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Skillnaden mellan att använda den klassiska Azure-portalen och Azure Resource Manager är i vilken ordning du skapar programgatewayen och de objekt som ska konfigureras.

Med Resource Manager konfigureras alla objekt som bildar en programgateway separat och sätts sedan ihop för att skapa en programgatewayresurs.

I det följande anges de steg som krävs för att skapa en programgateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

Kontrollera att du använder den senaste versionen av Azure PowerShell. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

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
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Se till att alla kommandon du använder för att skapa en programgateway använder samma resursgrupp.

I exemplet ovan skapade vi resursgruppen **appgw-RG** och platsen **USA, västra**.

> [!NOTE]
> Om du behöver konfigurera en anpassad avsökning för din programgateway läser du [Skapa en programgateway med anpassade avsökningar med hjälp av PowerShell](application-gateway-create-probe-ps.md). Mer information finns i [Anpassade avsökningar och hälsoövervakning](application-gateway-probe-overview.md).

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager. Det här exemplet skapar ett virtuellt nätverk för Application Gateway. Application Gateway kräver ett eget undernät. Därför är undernätet som skapas för Application Gateway mindre än adressutrymmet för det virtuella nätverket. Genom att använda ett mindre undernätverk kan andra resurser, inklusive men inte begränsat till webbservrar, konfigureras i samma virtuella nätverk.

### <a name="step-1"></a>Steg 1

Tilldela adressintervallet 10.0.0.0/24 till undernätsvariabeln som ska användas för att skapa ett virtuellt nätverk. Det här steget skapar konfigurationsobjektet för undernätet för Application Gateway, som används i nästa exempel.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Steg 2

Skapa ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **appgw-rg** för regionen USA, västra med prefixet 10.0.0.0/16 och undernätet 10.0.0.0/24. Det här steget slutför konfigurationen av det virtuella nätverket med ett enda undernät som Application Gateway kan använda.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Steg 3

Tilldela undernätsvariabeln för nästa steg. Den här variabeln används med cmdleten `New-AzureRMApplicationGateway` i ett steg längre fram.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs, **publicIP01**, i resursgruppen **appgw-rg** för regionen USA, västra. Application Gateway kan använda en offentlig IP-adress, en intern IP-adress eller båda för att ta emot förfrågningar för belastningsutjämning.  I det här exemplet används endast en offentlig IP-adress. I följande exempel konfigureras inget DNS-namn när den offentliga IP-adressen skapas.  Application Gateway stöder inte anpassade DNS-namn med offentliga IP-adresser.  Om ett anpassat namn krävs för den offentliga slutpunkten bör en CNAME-post skapas som pekar på det automatiskt genererade DNS-namnet för den offentliga IP-adressen.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-the-application-gateway-configuration-objects"></a>Skapa konfigurationsobjekt för programgatewayen

Alla konfigurationsobjekt måste konfigureras innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet **gatewayIP01**. När Application Gateway startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i backend-IP-poolen. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Steg 2

Konfigurera IP-adresspoolen för serverdelen med namnet **pool01** med IP-adresser för **pool1**. Dessa IP-adresser är IP-adresserna för de resurser som är värdar för webbprogrammet som ska skyddas av programgatewayen. Hälsotillståndet för dessa medlemmar i serverdelspoolen verifieras genom grundläggande eller anpassade avsökningar.  Sedan dirigeras trafiken till dem när begäranden kommer till programgatewayen. Serverdelspooler kan användas av flera regler i programgatewayen, vilket innebär att en serverdelspool kan användas för flera webbprogram som finns på samma värd.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

I det här exemplet finns det två serverdelspooler för dirigering av nätverkstrafik baserat på URL-sökvägen. En pool tar emot trafik från URL-sökvägen ”/video” och en annan pool tar emot trafik från sökvägen ”/image”. Ersätt IP-adresserna med IP-adresslutpunkterna för dina egna program.

### <a name="step-3"></a>Steg 3

Konfigurera inställningen **poolsetting01** för programgatewayen för den belastningsutjämnade nätverkstrafiken i backend-poolen. Varje serverdelspool kan ha sin egen serverdelspoolinställning.  Serverdelens HTTP-inställningar används av regler för att dirigera trafik till rätt medlemmar i serverdelspoolen. Serverdelens HTTP-inställningar avgör vilket protokoll och vilken port som används när trafik skickas till medlemmarna i serverdelspoolen. Cookie-baserade sessioner bestäms också av serverdelens HTTP-inställningar.  Om cookie-baserad sessionstillhörighet är aktiverat skickas trafiken till samma serverdel som tidigare begäranden för varje paket.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>Steg 4

Konfigurera klientdelsporten för en programgateway. Konfigurationsobjektet för klientdelsporten används av en lyssnare för att definiera vilken port som Application Gateway lyssnar efter trafik på.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Steg 5

Konfigurera klientdelens IP med den offentliga IP-slutpunkten. Konfigurationsobjektet för klientdelens IP används av en lyssnare för att relatera den utåtgående IP-adressen med lyssnaren.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Steg 6

Konfigurera lyssnaren. Det här steget konfigurerar lyssnaren för den offentliga IP-adressen och porten som används för att ta emot inkommande nätverkstrafik. Följande exempel använder den tidigare konfigurerade konfigurationen av klientdelens IP, konfigurationen av klientdelens port och ett protokoll (http eller https) och konfigurerar lyssnaren. I det här exemplet lyssnar lyssnaren efter HTTP-trafik på port 80 på den offentliga IP-adressen som skapades tidigare.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Steg 7

Skapa en hanteringsregel för belastningsutjämnaren med namnet **rule01** som konfigurerar belastningsutjämnarens beteende. Inställningarna för serverdelspoolen, lyssnaren och serverdelspoolen som skapades i föregående steg bildar tillsammans regeln. Baserat på kriterierna som definierats dirigeras trafiken till lämplig serverdel.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting01 -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Steg 8

Konfigurera antalet instanser av och storleken på programgatewayen.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> Standardvärdet för **InstanceCount** är 2, och det högsta värdet är 10. Standardvärdet för **GatewaySize** är Medium. Du kan välja mellan **Standard_Small**, **Standard_Medium** och **Standard_Large**.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Skapa en programgateway med hjälp av New-AzureRmApplicationGateway

Skapa en programgateway med alla konfigurationsobjekt från föregående steg. I det här exemplet heter programgatewayen **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Hämta DNS- och VIP-information för programgatewayen från den offentliga IP-resurs som är kopplad till programgatewayen.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-an-application-gateway"></a>Ta bort en programgateway

Följ dessa anvisningar om du vill ta bort en programgateway:

### <a name="step-1"></a>Steg 1

Hämta objektet för programgatewayen och associera det med variabeln `$getgw`.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Steg 2

Använd `Stop-AzureRmApplicationGateway` för att stoppa programgatewayen.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

När programgatewayen är i ett stoppat läge kan du använda cmdleten `Remove-AzureRmApplicationGateway` för att ta bort tjänsten.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> Du kan använda växeln **-force** om du inte vill att några bekräftelsemeddelanden ska visas.

Du kan kontrollera att tjänsten har tagits bort genom att använda cmdleten `Get-AzureRmApplicationGateway`. Det här steget är inte obligatoriskt.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Hämta DNS-namn för programgatewayen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP-adress krävs ett dynamiskt tilldelat DNS-namn som inte är användarvänligt. För att säkerställa att slutanvändare kan nå programgatewayen kan en CNAME-post användas för att peka på den offentliga slutpunkten för programgatewayen. [Konfigurera ett eget domännamn i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Du hittar det dynamiskt skapade DNS-namnet genom att hämta informationen om programgatewayen och dess associerade IP/DNS-namn med hjälp av PublicIPAddress-elementet som är kopplat till programgatewayen. programgatewayens DNS-namn ska användas för att skapa en CNAME-post som leder de två webbapparna till detta DNS-namn. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av programgatewayen.

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

Om du vill konfigurera SSL-avlastning läser du [Konfigurera en programgateway för SSL-avlastning](application-gateway-ssl.md).

Om du vill konfigurera en programgateway för användning med en intern belastningsutjämnare läser du [Skapa en programgateway med en intern belastningsutjämnare (ILB)](application-gateway-ilb.md).

Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


