---
title: Konfigurera SSL för slutpunkt till slutpunkt med Azure Programgateway
description: Den här artikeln beskriver hur du konfigurerar SSL för slutpunkt till slutpunkt med Azure Application Gateway med hjälp av PowerShell
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/27/2018
ms.author: victorh
ms.openlocfilehash: 7e259936dce433683dd135171ee1c5626bf23739
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32154202"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurera SSL för slutpunkt till slutpunkt med hjälp av Application Gateway med PowerShell

## <a name="overview"></a>Översikt

Azure Application Gateway har stöd för slutpunkt till slutpunkt kryptering av trafik. Programgateway avbryter SSL-anslutningen vid programgatewayen. Gatewayen sedan tillämpar regler för vidarebefordran på trafik, omkrypteras paketet och vidarebefordrar paketet till rätt backend-server baserat på regler för vidarebefordran definierats. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren.

Programgateway stöder definiera anpassade SSL-alternativ. Det stöder också inaktivering av följande protokollversioner: **TLSv1.0**, **TLSv1.1**, och **TLSv1.2**och definiera vilka krypteringssviter som ska användas och efter prioritet . Mer information om konfigurerbara alternativ som SSL finns i [översikt över SSL-policy](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 och SSL 3.0 är inaktiverade som standard och kan inte aktiveras. De betraktas som osäkra och kan inte användas med Application Gateway.

![scenario-bild][scenario]

## <a name="scenario"></a>Scenario

Lär dig hur du skapar en Programgateway med hjälp av SSL för slutpunkt till slutpunkt med PowerShell i det här scenariot.

Det här scenariot kommer:

* Skapa en resursgrupp med namnet **appgw rg**.
* Skapa ett virtuellt nätverk med namnet **appgwvnet** med ett adressutrymme för **10.0.0.0/16**.
* Skapa två undernät kallas **appgwsubnet** och **appsubnet**.
* Skapa ett litet program gateway stöder slutpunkt till slutpunkt SSL-kryptering som gränser SSL-protokollversioner och krypteringssviter.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera SSL för slutpunkt till slutpunkt med en Programgateway ett certifikat krävs för gatewayen och certifikat krävs för backend-servrar. Gateway-certifikat används för att kryptera och dekryptera den trafik som skickas till den via SSL. Gateway-certifikat måste vara i formatet Personal Information Exchange (PFX). Det här filformatet kan du exportera den privata nyckeln som krävs av programgatewayen att genomföra kryptering och dekryptering av trafik.

Serverdelen måste vara godkända med programgatewayen för SSL-kryptering för slutpunkt till slutpunkt. Du måste överföra offentliga certifikatet för backend-servrar till programgatewayen. Lägger till certifikatet garanterar att programgatewayen endast kommunicerar med kända backend-instanser. Detta skyddar ytterligare slutpunkt till slutpunkt-kommunikation.

Konfigurationen beskrivs i följande avsnitt.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Det här avsnittet vägleder dig genom att skapa en resursgrupp som innehåller programgatewayen.


   1. Logga in på ditt Azure-konto.

   ```powershell
   Connect-AzureRmAccount
   ```


   2. Välj prenumerationen som ska användas för det här scenariot.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Skapa en resursgrupp. (Hoppa över det här steget om du använder en befintlig resursgrupp.)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

I följande exempel skapas ett virtuellt nätverk och två undernät. Ett undernät används för programgatewayen. Det andra undernätet används för de servrar som är värdar för webbprogrammet.


   1. Tilldela ett adressintervall för undernätet som ska användas för Programgateway.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Undernät som konfigurerats för en Programgateway bör vara rätt storlek. En Programgateway kan konfigureras för upp till 10 instanser. Varje instans tar en IP-adress från undernätet. För litet för ett undernät som kan påverkas negativt och skala ut en Programgateway.
   > 
   > 

   2. Tilldela ett adressintervall som ska användas för backend-adresspool.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Skapa ett virtuellt nätverk med undernät som definierats i föregående steg.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Hämta virtuella nätverksresurs och undernätverksresurser som ska användas i de steg som följer.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs som ska användas för Programgateway. Den här offentliga IP-adressen används i någon av de steg som följer.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Programgateway stöder inte användning av en offentlig IP-adress som skapats med en angiven domänetikett. Endast en offentlig IP-adress med en dynamiskt skapade domänetikett stöds. Om du behöver ett eget DNS-namn för Programgateway rekommenderar vi du använder en CNAME-post som ett alias.

## <a name="create-an-application-gateway-configuration-object"></a>Skapa ett konfigurationsobjekt för programgatewayen

Alla konfigurationsobjekt som ställs innan du skapar programgatewayen. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

   1. Skapa ett program gateway IP-konfigurationen. Den här inställningen konfigurerar som undernät använder programgatewayen. När Programgateway startar den hämtar en IP-adress från undernätet som är konfigurerade och skickar nätverkstrafik till IP-adresser i backend-IP-adresspool. Tänk på att varje instans använder en IP-adress.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Skapa en frontend IP-konfiguration. Den här inställningen mappar en privat eller offentlig IP-adress till klientdelen för programgatewayen. Följande steg associerar den offentliga IP-adressen i föregående steg med frontend IP-konfigurationen.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Konfigurera backend-IP-adresspool med IP-adresserna för backend-webbservrar. De här IP-adreserna är de IP-adresser som tar emot den nätverkstrafik som kommer från frontend-IP-slutpunkten. Ersätt IP-adresser i exemplet med din egen programslutpunkter IP-adress.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Ett fullständigt kvalificerat domännamn (FQDN) är också ett giltigt värde ska användas i stället för en IP-adress för backend-servrar. Du kan aktivera det med hjälp av den **- BackendFqdns** växla. 


   4. Konfigurera frontend IP-port för den offentliga IP-slutpunkten. Den här porten är den port som användarna ansluter till.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. Konfigurera certifikat för programgatewayen. Det här certifikatet används för att dekryptera och reencrypt trafiken i programgatewayen.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Det här exemplet konfigurerar certifikatet som används för SSL-anslutning. Certifikatet måste vara i PFX-format och lösenordet måste vara 4 till 12 tecken.

   6. Skapa HTTP-lyssnare för programgatewayen. Tilldela frontend IP-konfiguration, port och SSL-certifikat som ska användas.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Överför certifikatet som ska användas på resurserna som SSL-aktiverade backend-adresspool.

   > [!NOTE]
   > Standard-avsökningen hämtar den offentliga nyckeln från den *standard* SSL-bindning på serverdelens IP-adress och jämför den offentliga nyckel/värde tas emot till värdet för offentliga nyckeln anger här. 
   
   > Om du använder värdhuvuden och Server Servernamnsindikation (SNI) på serverdelen kanske inte platsen till vilka trafikflöden hämtade offentliga nyckel. Om du är osäker besöker https://127.0.0.1/ på backend-servrarna och bekräfta vilket certifikat som används för den *standard* SSL-bindning. Använd den offentliga nyckeln från denna begäran i det här avsnittet. Om du använder värdhuvuden och SNI på HTTPS-bindningar och du får ett svar och certifikat från en manuell webbläsarbegäran till https://127.0.0.1/ på backend-servrar, måste du ställa in en standard-SSL-bindning med aktiviteterna. Om du inte gör det avsökningar misslyckas och serverdelen är inte godkända.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Certifikatet som angavs i det här steget ska vara den offentliga nyckeln för PFX-certifikatet finns på serverdelen. Exportera certifikat (inte rotcertifikat) installerat på backend-servern i anspråk, bevis och skäl (CER)-format och använda den i det här steget. Det här steget whitelists serverdel med programgatewayen.

   8. Konfigurera HTTP-inställningar för serverdelen application gateway. Tilldela det certifikat som har överförts i det föregående steget för att HTTP-inställningar.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Skapa en regel för vidarebefordran av belastningsutjämnare som konfigurerar belastningsutjämning belastningen. I det här exemplet skapas en grundläggande resursallokeringsregel.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Konfigurera programgatewayens instansstorlek. Tillgängliga storlekar är **Standard\_små**, **Standard\_medel**, och **Standard\_stor**.  För kapacitet, tillgängliga värden är **1** via **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > Du kan välja ett instansantal på 1 för testning. Det är viktigt att du vet att alla instanser under två instanser inte som omfattas av SLA och därför rekommenderas inte. Liten gateways ska användas för utveckling testning och inte för produktion.

   11. Konfigurera SSL-policy som ska användas på programgatewayen. Programgateway stöder möjligheten att ange en lägsta version för SSL-protokollversioner.

   Följande värden visas en lista över protokollversioner som kan definieras:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   I följande exempel anger den minsta Protokollversionen till **TLSv1_2** och möjliggör **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, och **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** endast.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Använd de föregående stegen för att skapa programgatewayen. Skapandet av gatewayen är en process som tar lång tid att köra.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Begränsa SSL-protokoll version på en befintlig Programgateway

Ovanstående steg tog dig genom att skapa ett program med SSL för slutpunkt till slutpunkt och inaktivera vissa versioner för SSL-protokollet. I följande exempel inaktiverar vissa SSL-principer på en befintlig gateway för programmet.

   1. Hämta Programgateway att uppdatera.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Definiera ett SSL-policy. I följande exempel **TLSv1.0** och **TLSv1.1** är inaktiverad och krypteringssviterna **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, och **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** är de enda de som tillåts.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Slutligen uppdatera gatewayen. Observera att det sista steget är en tidskrävande uppgift. När du är klar konfigureras slutpunkt till slutpunkt SSL på programgatewayen.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett program gateway DNS-namn

När du har skapat, är nästa steg att konfigurera klientdelen för kommunikation. Programgateway kräver en dynamiskt tilldelad DNS-namn när du använder en offentlig IP-adress som inte är eget. För att säkerställa att användarna kan trycka programgatewayen, kan du använda en CNAME-post så att den pekar till offentlig slutpunkt för programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn för i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Hämta information om programgatewayen och dess tillhörande IP DNS-namn för att konfigurera ett alias, med hjälp av den **PublicIPAddress** element som är kopplade till programgatewayen. Använda den Programgateway DNS-namn för att skapa en CNAME-post som pekar på två webbprogram till DNS-namnet. Vi inte rekommenderar användning av A-poster, eftersom VIP kan ändra på omstart av programgatewayen.

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

Mer information om Härdning av säkerheten för ditt webbprogram med Brandvägg för webbaserade program via Application Gateway finns på [översikt över webbprogram brandväggen](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
