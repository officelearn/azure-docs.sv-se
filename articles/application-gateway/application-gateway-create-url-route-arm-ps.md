---
title: "Skapa en Programgateway med hjälp av URL: en routningsregler | Microsoft Docs"
description: "Den här sidan innehåller instruktioner för att skapa och konfigurera en gateway för Azure-program med hjälp av regler för routning av URL: en."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: f0b085ebf922cd5b14acd91bf86b9262a6921e9e
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Skapa en Programgateway med sökväg-baserad Routning

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Sökväg-baserad routning associerar rutter baserat på en URL-sökväg för en HTTP-begäran. Den kontrollerar om det finns en väg till en backend-adresspool som konfigurerats för den URL som visas i programgatewayen och skickar sedan nätverkstrafiken till definierade backend-poolen. Ett vanligt användningsområde för URL-baserade routning är att belastningsutjämna förfrågningar för olika typer av innehåll till olika backend-serverpooler.

Azure Application Gateway har två regeltyper: grundläggande Routning och sökväg-baserad routning. Basic innehåller resursallokering för backend-pooler. Sökväg-baserade routning, förutom resursallokering, använder också sökvägar för den begärda Webbadressen välja backend-poolen.

## <a name="scenario"></a>Scenario

I följande exempel Programgateway hanterar trafik för contoso.com med två backend-server-adresspooler: en video serverpool och en bild serverpoolen.

Begäranden för http://contoso.com/image * dirigeras till serverpoolen bild (**pool1**), och begäranden för http://contoso.com/video * dirigeras till video serverpoolen (**pool2**). Om ingen sökväg mönster matchar poolen server standard (**pool1**) är markerad.

![URL-väg](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Skapa ett virtuellt nätverk och undernät för en Programgateway. Se till att inga virtuella datorer eller molndistributioner använder det undernätet. Programgatewayen måste vara fristående i ett virtuellt nätverks undernät.
3. Se till att de servrar som har lagts till i backend-poolen för Programgateway finns eller att de har sina slutpunkter har skapats i det virtuella nätverket eller med en offentlig IP-adress/VIP tilldelad.

## <a name="requirements-to-create-an-application-gateway"></a>Krav för att skapa en Programgateway

* **Backend-serverpoolen**: listan över IP-adresser för backend-servrar. IP-adresser som anges bör tillhöra undernät för virtuellt nätverk eller vara en offentlig IP-adress/VIP.
* **Backend-server poolinställningarna**: till exempel port och protokoll cookie-baserad tillhörighet. Dessa är knutna till poolen och tillämpas på alla servrar i poolen.
* **Frontend-port**: den offentliga porten som öppnas på programgatewayen. Trafik träffar den här porten och sedan omdirigeras till en backend-server.
* **Lyssnare**: lyssnaren har en frontend-port, ett protokoll (Http eller Https, som är skiftlägeskänsliga) och SSL-certifikatets namn (om hur du konfigurerar SSL-avlastning).
* **Regeln**: regeln Binder lyssnaren och backend-serverpoolen och definierar vilka poolen trafiken ska dirigeras till när den når en lyssnare.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Skillnaden mellan att använda den klassiska distributionsmodellen och Azure Resource Manager är den ordning som du kan skapa programgatewayen och de objekt som ska konfigureras.

Med Resource Manager konfigureras alla objekt som bildar en programgateway separat och sätts sedan ihop för att skapa en programgatewayresurs.

Följ dessa steg om du vill skapa en Programgateway:

1. Skapa en resursgrupp för Resource Manager.
2. Skapa ett virtuellt nätverk, ett undernät och en offentlig IP för programgatewayen.
3. Skapa ett konfigurationsobjekt för programgatewayen.
4. Skapa en resurs för en programgateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

Kontrollera att du använder den senaste versionen av Azure PowerShell. Hitta mer information på [med hjälp av Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Steg 1

Logga in på Azure.

```powershell
Login-AzureRmAccount
```

Du uppmanas att autentisera med dina autentiseringsuppgifter.<BR>

### <a name="step-2"></a>Steg 2

Kontrollera prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Steg 3

Välj vilka av dina Azure-prenumerationer som du vill använda. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Steg 4

Skapa en resursgrupp. (Hoppa över det här steget om du använder en befintlig resursgrupp.)

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

Du kan också skapa taggar för en resursgrupp för en Programgateway:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager kräver att resursgrupper anger standardplatsen, som används för alla resurser i gruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

I föregående exempel vi skapa en resursgrupp med namnet ”appgw RG” och använde ”USA, västra”.

> [!NOTE]
> Om du behöver konfigurera en anpassad avsökningsåtgärd för din Programgateway går du till [skapa en Programgateway med anpassade avsökningar med hjälp av PowerShell](application-gateway-create-probe-ps.md). Se [ Programgateway hälsoövervakning översikt](application-gateway-probe-overview.md) för mer information.
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager. Det här exemplet skapar ett virtuellt nätverk för programgatewayen. Programgateway kräver sin egen undernät. Undernät som skapats för Programgateway är mindre än virtuella nätverkets adressutrymme. Detta gör att andra resurser, inklusive men inte begränsat till webbservrar, konfigureras i samma virtuella nätverk.

### <a name="step-1"></a>Steg 1

Tilldela adressintervallet 10.0.0.0/24 till undernätsvariabeln som ska användas för att skapa ett virtuellt nätverk.  Detta skapar undernät konfigurationsobjektet för Programgateway som används i nästa exempel.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Steg 2

Skapa ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **appgw rg** för regionen USA, västra med prefixet 10.0.0.0/16 med undernätet 10.0.0.0/24. Nu är du klar med konfigurationen av det virtuella nätverket med ett enda undernät för Programgateway finns.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Steg 3

Tilldela variabeln undernät i nästa steg. Detta har överförts till den `New-AzureRMApplicationGateway` cmdlet i ett kommande steg.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs, **publicIP01**, i resursgruppen **appgw-rg** för regionen USA, västra. Programgateway kan använda en offentlig IP-adress, en intern IP-adress eller båda för att ta emot begäranden för belastningsutjämning.  Det här exemplet används endast en offentlig IP-adress. I följande exempel konfigureras inget DNS-namn för att skapa den offentliga IP-adressen eftersom Programgateway inte stöder anpassade DNS-namn på den offentliga IP-adresser.  Om det krävs ett eget namn för offentlig slutpunkt, skapa en CNAME-post för att peka DNS-namn som skapas automatiskt för den offentliga IP-adressen.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

En IP-adress tilldelas till programgatewayen när tjänsten startas.

## <a name="create-the-application-gateway-configuration"></a>Skapa program gateway-konfiguration

Alla konfigurationsobjekt måste ställas in innan du skapar programgatewayen. Följande steg skapar du konfigurationsobjekt som behövs för en gateway programresursen.

### <a name="step-1"></a>Steg 1

Skapa en IP-konfiguration för programgatewayen med namnet **gatewayIP01**. När Programgateway startar den hämtar en IP-adress från undernätet som är konfigurerade och skickar nätverkstrafik till IP-adresser i backend-IP-adresspool. Tänk på att varje instans använder en IP-adress.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Steg 2

Konfigurera backend-IP-adresspool med namnet **pool1** och **pool2** med IP-adresser för **pool1** och **pool2**. Detta är IP-adresserna för de resurser som värd för webbprogram som ska skyddas av programgatewayen. Dessa backend-poolmedlemmar verifieras alla som felfri av grundläggande eller anpassat avsökningar. Sedan dirigeras trafiken till dem när begäranden kommer till programgatewayen. Backend-pooler kan användas av flera regler i programgatewayen. Detta innebär en backend-adresspool kan användas för flera webbprogram som finns på samma värd.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

I det här exemplet vidarebefordra två backend-adresspooler nätverkstrafik baserat på en URL-sökväg. En pool tar emot trafik från URL-sökvägen ”/ video-” och andra poolen tar emot trafik från sökvägen ”/ image”. Ersätt IP-adresserna med IP-adresslutpunkterna för dina egna program. 

### <a name="step-3"></a>Steg 3

Konfigurera inställningar för Programgateway **poolsetting01** och **poolsetting02** för nätverkstrafik Utjämning av nätverksbelastning i backend-poolen. I det här exemplet kan du konfigurera inställningarna för olika backend-pool för backend-pooler. Varje backend-pool kan ha sina egna inställningar.  Regler använder backend-HTTP-inställningar för att dirigera trafik till rätt backend-poolmedlemmar. Anger protokoll och port som används för att skicka trafik till backend-poolmedlemmar. Cookie-baserad sessioner också bestäms av backend-HTTP-inställningar. Om aktiverad, skickar cookie-baserad session tillhörighet trafik till samma serverdelen som tidigare begäranden för varje paket.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Steg 4

Konfigurera frontend IP-Adressen med den offentliga IP-slutpunkter. En lyssnare använder frontend IP-konfigurationsobjekt för att relatera passiv-riktade IP-adress med lyssnaren.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Steg 5

Konfigurera klientdelsporten för en programgateway. Lyssnaren använder konfigurationsobjektet frontend-port för att definiera vilken port som programgatewayen lyssnar efter trafik på lyssnaren.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Steg 6

Konfigurera lyssnaren för offentliga IP-adressen och porten som används för att ta emot inkommande nätverkstrafik. I följande exempel tar tidigare konfigurerade frontend IP-konfigurationen och frontend portkonfiguration ett protokoll (Http eller Https, som är skiftlägeskänsliga) och konfigurerar lyssnaren. I det här exemplet lyssnar lyssnaren efter HTTP-trafik på port 80 på den offentliga IP-adressen som skapades tidigare.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Steg 7

Konfigurera URL: en regel sökvägar för backend-pooler. Det här steget konfigurerar du den relativa sökvägen som används av Programgateway och definierar mappningen mellan en URL-sökväg och backend-poolen som har tilldelats för den inkommande trafiken.

> [!IMPORTANT]
> Varje sökväg måste börja med ”/” och en asterisk tillåts endast i slutet. Giltiga exempel är /xyz /xyz*, eller /xyz/*. Strängen som skickas till sökvägen matcher innehåller inte någon text efter först ””? eller ”#”, och dessa tecken är inte tillåtna. 

I följande exempel skapas två regler: en för en ”/ bild /” sökväg routning trafik till backend- **pool1**, och en annan för en ”/ video /” sökväg dirigera trafiken till backend- **pool2**. Dessa regler kan du kontrollera att trafik för varje uppsättning URL-adresser dirigeras till serverdelen. Till exempel http://contoso.com/image/figure1.jpg går till **pool1** och http://contoso.com/video/example.mp4 **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

Om sökvägen inte matchar någon av de fördefinierade sökvägsreglerna, konfigurerar regelkonfigurationen sökväg kartan även en standard backend-adresspool. Till exempel http://contoso.com/shoppingcart/test.html går till **pool1** eftersom det har definierats som standard för omatchade trafik.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Steg 8

Skapa en regel inställning. Det här steget konfigurerar Programgateway för att använda URL-sökväg-baserade routning. Den `$urlPathMap` variabel som anges i tidigare steg nu användas för att skapa regeln baserat på sökvägen. Vi associera regeln med en lyssnare och URL: en sökvägsmappning skapade tidigare i det här steget.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Steg 9

Konfigurera antalet instanser av och storleken på programgatewayen.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Skapa en Programgateway med alla konfigurationsobjekt från föregående steg.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett program gateway DNS-namn

När du har skapat en gateway, konfigurerar du klientdelen för kommunikation. När du använder en offentlig IP-adress, kräver en dynamiskt tilldelad DNS-namn som inte är eget Application Gateway. Du kan använda en CNAME-post så att den pekar till offentlig slutpunkt för programgatewayen så kunder kan träffa programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn för en Azure-molntjänst](../cloud-services/cloud-services-custom-domain-name-portal.md).

Hämta information om programgatewayen och dess tillhörande IP DNS-namn om du vill konfigurera frontend IP CNAME-posten med hjälp av PublicIPAddress-element som är kopplade till programgatewayen. Använda den Programgateway DNS-namn för att skapa en CNAME-post. Vi rekommenderar inte användning av A-poster eftersom VIP kan ändras på omstart av Application Gateway.

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

Om du vill veta om Secure Sockets Layer (SSL)-avlastning, se [konfigurera en Programgateway för SSL-avlastning med hjälp av Azure Resource Manager](application-gateway-ssl-arm.md).

