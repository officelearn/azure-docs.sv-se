---
title: Konfigurera SSL-avlastning - Azure Application Gateway - PowerShell | Microsoft Docs
description: "Den här artikeln innehåller instruktioner för att skapa en Programgateway med SSL avlasta med hjälp av Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.openlocfilehash: ee48ca45ae0d337b5b919dbbb28341caf8af0d45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Konfigurera en programgateway för SSL-avlastning med hjälp av Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure klassiska PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway kan konfigureras att avsluta SSL-sessionen (Secure Sockets Layer) på gatewayen så att du undviker kostsamma SSL-dekrypteringsaktiviteter i webbservergruppen. SSL-avlastning förenklar också frontend-serverkonfigurationen och hanteringen av webbappen.

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Skapa ett virtuellt nätverk och ett undernät för programgatewayen. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Programgatewayen måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för att använda programmet gatewayen måste finnas eller har skapat sina slutpunkter i det virtuella nätverket eller med en offentlig IP-adress eller en virtuell IP-adress (VIP).

## <a name="what-is-required-to-create-an-application-gateway"></a>Vad krävs för att skapa en programgateway?

* **Backend-serverpoolen**: listan över IP-adresser för backend-servrar. IP-adresser i listan ska tillhöra undernät för virtuellt nätverk eller ska vara en offentlig IP-adress/VIP.
* **Backend-server poolinställningarna**: varje pool har inställningar som port och protokoll cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Frontend-port**: den här porten är den offentliga som öppnas på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare**: lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa inställningar är skiftlägeskänsligt), och namnet på SSL (om hur du konfigurerar SSL-avlastning).
* **Regeln**: regeln Binder lyssnaren och backend-serverpoolen och definierar vilka backend-server-pool för att dirigera trafik till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

**Information om ytterligare konfiguration**

För konfiguration av SSL-certifikat bör protokollet i **HttpListener** ändras till **Https** (skiftlägeskänsligt). Lägg till den **SslCertificate** elementet så att **HttpListener** med variabelns värde som konfigurerats för SSL-certifikatet. Frontend-port som ska uppdateras till **443**.

**Så här aktiverar du cookie-baserad tillhörighet**: du kan konfigurera en Programgateway för att säkerställa att en begäran från en klientsession alltid dirigeras till samma virtuella dator i en webbservergrupp. Infoga en sessions-cookie som gör att en gateway att dirigera trafik på lämpligt sätt för att åstadkomma detta. Du kan aktivera cookiebaserad tillhörighet genom att ange **CookieBasedAffinity** till **Enabled** i elementet **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Skillnaden mellan att använda Azure klassiska distributionsmodellen och med Azure Resource Manager är den ordning som du kan skapa en Programgateway och de objekt som ska konfigureras.

Med Resource Manager kan alla komponenter i en Programgateway konfigureras separat, och publicera sedan tillsammans för att skapa en gateway programresursen.

Här är de steg du följer när du skapar en programgateway:

1. [Skapa en resursgrupp för Resource Manager](#create-a-resource-group-for-resource-manager)
2. [Skapa virtuella nätverk, undernät och offentliga IP för Programgateway](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [Skapa ett konfigurationsobjekt för program-gateway](#create-an-application-gateway-configuration-object)
4. [Skapa ett program gateway-resurs](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>Skapa en resursgrupp för Resource Manager

Glöm inte att byta PowerShell-läge så att du kan använda cmdlets för Azure Resource Manager. Mer information finns i [Använda Windows PowerShell med Resource Manager](../powershell-azure-resource-manager.md).

   1. Ange följande kommando:

   ```powershell
   Login-AzureRmAccount
   ```

   2. Ange följande kommandon för att kontrollera prenumerationer för kontot:

   ```powershell
   Get-AzureRmSubscription
   ```

   Du ombeds att autentisera dig med dina autentiseringsuppgifter.

   3. Om du vill välja vilka av dina Azure-prenumerationer att använda, anger du följande kommando:

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Ange följande kommando för att skapa en resursgrupp. (Hoppa över det här steget om du använder en befintlig resursgrupp.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Denna inställning används som standardplatsen för resurser i resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

Vi har skapat en resursgrupp med namnet i det förra exemplet **appgw RG** och platsen är **västra USA**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

Följande exempel illustrerar hur du skapar ett virtuellt nätverk med hjälp av Resource Manager:

   1. Ange följande kommando:

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   Det här exemplet tilldelas adressintervallet **10.0.0.0/24** till en variabel för undernät som används för att skapa ett virtuellt nätverk.

   2. Ange följande kommando:

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   Det här exemplet skapar ett virtuellt nätverk med namnet **appgwvnet** i resursgruppen **appgw rg** för den **västra USA** region med prefixet **10.0.0.0/16** med undernät **10.0.0.0/24**.

   3. Ange följande kommando:

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   Det här exemplet tilldelas variabeln undernätets objekt **$subnet** i nästa steg.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Ange följande kommando för att skapa en offentlig IP-adress för frontend-konfiguration:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Det här exemplet skapar en offentlig IP-resurs **publicIP01** i resursgruppen **appgw rg** för den **västra USA** region.

## <a name="create-an-application-gateway-configuration-object"></a>Skapa ett konfigurationsobjekt för programgatewayen

   1. Ange följande kommando för att skapa ett konfigurationsobjekt för program-gateway:

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   Det här exemplet skapar ett program gateway IP-konfiguration med namnet **gatewayIP01**. När Programgateway startar hämtar en IP-adress från undernätet som är konfigurerade och dirigerar nätverkstrafik till IP-adresser i backend-IP-adresspool. Tänk på att varje instans använder en IP-adress.

   2. Ange följande kommando:

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   Det här exemplet konfigurerar backend-IP-adresspool med namnet **pool01** med IP-adresser **134.170.185.46**, **134.170.188.221**, och **134.170.185.50** . Dessa värden är IP-adresserna som tar emot nätverkstrafiken som kommer från frontend-IP-slutpunkten. Ersätt IP-adresserna i exemplet ovan med IP-adresserna för slutpunkterna för din webbapp.

   3. Ange följande kommando:

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   Det här exemplet konfigurerar gateway programinställning **poolsetting01** att belastningsutjämna nätverkstrafik i backend-poolen.

   4. Ange följande kommando:

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   Det här exemplet konfigurerar frontend IP-porten **frontendport01** för den offentliga IP-slutpunkten.

   5. Ange följande kommando:

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   I det här exemplet konfigureras certifikatet som används för SSL-anslutning. Certifikatet måste vara i PFX-format och lösenordet måste innehålla mellan 4 och 12 tecken.

   6. Ange följande kommando:

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   Det här exemplet skapar frontend IP-konfigurationen med namnet **fipconfig01** och associerar den offentliga IP-adressen med frontend IP-konfigurationen.

   7. Ange följande kommando:

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   Det här exemplet skapar lyssnaren med namnet **listener01** och associerar frontend-port till frontend IP-konfiguration och certifikat.

   8. Ange följande kommando:

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   Det här exemplet skapar belastningsutjämnare routningsregel med namnet **rule01** som konfigurerar belastningsutjämning.

   9. Ange följande kommando:

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   I det här exemplet konfigureras programgatewayens instansstorlek.

   > [!NOTE]
   > Standardvärdet för **InstanceCount** är **2**, med ett maximalt värde 10. Standardvärdet för **GatewaySize** är **medel**. Du kan välja mellan Standard_Small, Standard_Medium och Standard_Large.

   10. Ange följande kommando:

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   Det här steget definierar SSL-policy på programgatewayen. Mer information finns i [Konfigurera SSL princip versioner och krypteringssviter på Programgateway](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Skapa en programgateway med hjälp av New-AzureApplicationGateway

Ange följande kommando:

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

Det här exemplet skapar en Programgateway med alla konfigurationsobjekt från föregående steg. I det här exemplet programgatewayen kallas **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Hämta DNS-namnet på programmet gateway

När du har skapat, är nästa steg att konfigurera klientdelen för kommunikation. Programgateway kräver en dynamiskt tilldelad DNS-namn när du använder en offentlig IP-adress som inte är eget. För att säkerställa att användarna kan trycka programgatewayen, kan du använda en CNAME-post så att den pekar till offentlig slutpunkt för programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

För att få DNS-namnet på programmet gateway kan hämta information om programgatewayen och dess associerade IP DNS-namn med hjälp av den **PublicIPAddress** element som är kopplade till programgatewayen. Använda den Programgateway DNS-namn för att skapa en CNAME-post som pekar på två webbprogram till DNS-namnet. Vi inte rekommenderar användning av A-poster, eftersom VIP kan ändra på omstart av programgatewayen.


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

Om du vill konfigurera en Programgateway du använder med en intern belastningsutjämnare finns [skapa en Programgateway med en intern belastningsutjämnare](application-gateway-ilb.md).

Mer information om belastningsutjämning i allmänhet finns i alternativ:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
