---
title: Konfigurera SSL för slutpunkt till slutpunkt med Azure Application Gateway
description: Den här artikeln beskrivs hur du konfigurerar SSL för slutpunkt till slutpunkt med Azure Application Gateway med hjälp av PowerShell
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2018
ms.author: victorh
ms.openlocfilehash: f5b63c7448a502d681bbafb0ac80237a8e2c63c6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650102"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurera SSL för slutpunkt till slutpunkt med hjälp av Application Gateway med PowerShell

## <a name="overview"></a>Översikt

Azure Application Gateway stöder slutpunkt till slutpunkt-kryptering av trafik. Application Gateway avbryter SSL-anslutningen vid application gateway. Gatewayen sedan tillämper routingregler på trafik, omkrypteras paketet och vidarebefordrar paketet till rätt backend-server baserat på de routningsregler som definierats. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren.

Application Gateway stöder definiera anpassade SSL-alternativ. Det stöder också inaktivera följande protokollversioner: **TLSv1.0**, **TLSv1.1**, och **TLSv1.2**och definiera vilka krypteringssviter som du använder och i prioritetsordning efter . Om du vill veta mer om konfigurerbara alternativ som SSL kan se den [översikt över SSL-princip](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 och SSL 3.0 är inaktiverade som standard och kan inte aktiveras. De anses vara osäkra och kan inte användas med Programgatewayen.

![scenario-bild][scenario]

## <a name="scenario"></a>Scenario

I det här scenariot kan du lära dig hur du skapar en Programgateway med hjälp av SSL för slutpunkt till slutpunkt med PowerShell.

Det här scenariot kommer att:

* Skapa en resursgrupp med namnet **appgw-rg**.
* Skapa ett virtuellt nätverk med namnet **appgwvnet** med ett adressutrymme för **10.0.0.0/16**.
* Skapa två undernät som kallas **appgwsubnet** och **appsubnet**.
* Skapa ett litet program gateway stödjande slutpunkt till slutpunkt SSL-kryptering som gränser SSL-protokollsversioner och krypteringssviter.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera SSL för slutpunkt till slutpunkt med application gateway, krävs ett certifikat för gateway och certifikat krävs för backend servrarna. Gateway-certifikatet används för att kryptera och dekryptera trafiken som skickas till den via SSL. Gateway-certifikatet måste ha formatet Personal Information Exchange (PFX). Det här filformatet kan du exportera den privata nyckeln som krävs av application gateway för att utföra kryptering och dekryptering av trafik.

För slutpunkt till slutpunkt SSL-kryptering måste backend-servern också vitlistas med application gateway. Du måste överföra det offentliga certifikatet backend-servrar till application gateway. Lägger till certifikatet säkerställer att den application gatewayen kommunicerar bara med kända serverdelsinstanser. Detta skyddar ytterligare slutpunkt till slutpunkt-kommunikation.

Konfigurationsprocessen beskrivs i följande avsnitt.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Det här avsnittet beskriver hur du skapar en resursgrupp som innehåller application gateway.


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

I följande exempel skapas ett virtuellt nätverk och två undernät. Ett undernät används för application gateway. Andra undernätet används för de servrar som är värdar för webbprogrammet.


   1. Tilldela ett adressintervall för undernätet som ska användas för application gateway.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Undernät som konfigurerats för en Programgateway bör ändras korrekt. En application gateway kan konfigureras för upp till 10 instanser. Varje instans använder en IP-adress från undernätet. För liten för ett undernät som kan påverkas negativt och skala ut en Programgateway.
   > 
   > 

   2. Tilldela ett adressintervall som ska användas för backend-adresspoolen.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Skapa ett virtuellt nätverk med undernät som definierats i föregående steg.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Hämta resurs för virtuella nätverk och undernätverksresurser som ska användas i stegen nedan.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs som ska användas för application gateway. Den här offentliga IP-adressen används i någon av de steg som följer.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway stöder inte användning av en offentlig IP-adress som skapats med en definierad domänetiketten. Endast en offentlig IP-adress med en dynamiskt skapade domänetiketten stöds. Om du behöver ett eget DNS-namn för application gateway, rekommenderar vi du använder en CNAME-post som ett alias.

## <a name="create-an-application-gateway-configuration-object"></a>Skapa ett konfigurationsobjekt för programgatewayen

Alla konfigurationsobjekt anges innan du skapar programgatewayen. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

1. Skapa en IP-konfiguration för programgatewayen. Den här inställningen konfigurerar som undernät som application gateway använder. När application gateway startar hämtar den en IP-adress från det konfigurera undernätet och skickar nätverkstrafik till IP-adresserna i backend-IP-adresspool. Tänk på att varje instans använder en IP-adress.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


2. Skapa en frontend-IP-konfigurationen. Den här inställningen motsvarar klientdelen för application gateway en privat eller offentlig IP-adress. Följande steg associerar offentliga IP-adress i föregående steg med IP-konfigurationen.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Konfigurera backend-IP-adresspool med IP-adresserna i backend-webbservrar. De här IP-adreserna är de IP-adresser som tar emot den nätverkstrafik som kommer från frontend-IP-slutpunkten. Ersätt IP-adresserna i exemplet med ditt eget programs IP-adresslutpunkter.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Ett fullständigt kvalificerat domännamn (FQDN) är också ett giltigt värde ska användas i stället för en IP-adress för backend servrarna. Du aktiverar det genom att använda den **- BackendFqdns** växla. 


4. Konfigurera frontend IP-porten för den offentliga IP-slutpunkten. Den här porten är den port som användarna ansluta till.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurera certifikat för application gateway. Det här certifikatet används för att dekryptera och omkryptera trafik på application gateway.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Det här exemplet konfigureras certifikatet som används för SSL-anslutningen. Certifikatet måste vara i PFX-format och lösenordet måste innehålla 4 och 12 tecken.

6. Skapa HTTP-lyssnare för programgatewayen. Tilldela den frontend IP-konfiguration, port och SSL-certifikat som ska användas.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Ladda upp certifikat som ska användas för de resurser som SSL-aktiverad backend-poolen.

   > [!NOTE]
   > Standard-avsökningen hämtar den offentliga nyckeln från den *standard* SSL-bindning på backend-server IP-adress och jämför offentliga nyckelvärde som den tar emot till värdet för offentliga nyckeln du anger här. 
   
   > Om du använder värdhuvuden och (Servernamnindikator) på serverdelen, kanske inte platsen till vilken trafikflöden i den offentliga nyckeln hämtades. Om du är osäker, besök https://127.0.0.1/ på backend-servrarna och bekräfta vilket certifikat som används för den *standard* SSL-bindning. Använd den offentliga nyckeln från denna förfrågan i det här avsnittet. Om du använder värdhuvuden och SNI på HTTPS-bindningar och du inte får ett svar och certifikat från en manuell webbläsarbegäran till https://127.0.0.1/ på backend-servrar, måste du ställa in en standard-SSL-bindning med aktiviteterna. Om du inte gör det, avsökningar misslyckas och backend-servern är inte vitlistat.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Certifikatet som angavs i det här steget ska vara den offentliga nyckeln för .pfx-certifikat finns på serverdelen. Exportera certifikat (inte rotcertifikatet) installerat på backend-servern i anspråk, bevis och skäl till (CER)-format och använda den i det här steget. Det här steget vitlistor serverdelen med application gateway.

   Om du använder Application Gateway v2-SKU, skapar du ett betrott rotcertifikat i stället för ett certifikat för serverautentisering. Mer information finns i [översikt över slutpunkt till slutpunkt-SSL med Programgateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku):

   ```powershell
   $trustedRootCert01 = New-AzureRmApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurera HTTP-inställningar för application gateway för backend-servern. Tilldela certifikatet laddades upp i det föregående steget för att HTTP-inställningarna.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Använd följande kommando för SKU: N för Application Gateway v2:

   ```powershell
   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Skapa en hanteringsregel för belastningsutjämnaren som konfigurerar belastningsutjämnarens beteende. I det här exemplet skapas en grundläggande resursallokeringsregel.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurera programgatewayens instansstorlek. Tillgängliga storlekar är **Standard\_små**, **Standard\_medel**, och **Standard\_stor**.  För kapacitet, tillgängliga värden är **1** via **10**.

    ```powershell
    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Du kan välja ett instansantal på 1 för testning. Det är viktigt att veta att alla instansantalet under två instanser omfattas inte av serviceavtalet och rekommenderas därför inte. Det är små gateways som ska användas för utveckling, testning och inte för produktion.

11. Konfigurera SSL-princip som ska användas på application gateway. Application Gateway stöder möjligheten att ange en lägsta version för SSL-protokollsversioner.

   Följande värden är en lista över protokollversioner som kan definieras:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   I följande exempel anger den minsta Protokollversionen till **TLSv1_2** och möjliggör **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, och **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** endast.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Med alla steg ovan kan skapa programgatewayen. Skapandet av gatewayen är en process som tar lång tid att köra.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Begränsa SSL-protokollsversioner på en befintlig Programgateway

Föregående steg tog du skapar ett program med slutpunkt-till-slutpunkt SSL och inaktiverar vissa SSL-protokollsversioner. I följande exempel inaktiverar vissa SSL-principer på en befintlig application gateway.

   1. Hämta application-gateway för att uppdatera.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Definiera en SSL-princip. I följande exempel **TLSv1.0** och **TLSv1.1** är inaktiverad och de krypteringssviter som **TLS\_ECDHE\_ECDSA\_WITH\_ AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**, och **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** är den enda de som tillåts.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Slutligen uppdaterar du gatewayen. Observera att det sista steget är en tidskrävande uppgift. När den är klar konfigureras slutpunkt till slutpunkt SSL på application gateway.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett application gateway DNS-namn

När gatewayen har skapats är nästa steg att konfigurera klientprogrammet för kommunikation. Application Gateway kräver ett dynamiskt tilldelat DNS-namn när du använder en offentlig IP-adress som inte är användarvänligt. För att säkerställa att slutanvändare kan nå programgatewayen, kan du använda en CNAME-post så att den pekar på den offentliga slutpunkten för programgatewayen. Mer information finns i [konfigurera ett anpassat domännamn för i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Om du vill konfigurera ett alias, hämta information om programgatewayen och dess associerade IP/DNS-namn med hjälp av den **PublicIPAddress** elementet kopplat till programgatewayen. Använda DNS-namn för application gateway för att skapa en CNAME-post som pekar på två webbapparna till detta DNS-namn. Vi inte rekommenderar användning av A-poster, eftersom VIP kan ändra på omstart av programgatewayen.

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

Mer information om Härdning av säkerheten för dina webbprogram med Brandvägg för webbaserade program via Application Gateway finns i den [översikt över Web application firewall](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
