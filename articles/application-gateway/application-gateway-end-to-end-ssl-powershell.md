---
title: Konfigurera heltäckande SSL med Azure Application Gateway
description: I den hÃ¤r artikeln beskrivs hur du konfigurerar ssl som är från slutpunkt till slutpunkt med Azure Application Gateway med powershell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/8/2019
ms.author: victorh
ms.openlocfilehash: 7ba273cddb6cf41872c4db1c34560c104b992787
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72286461"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Konfigurera heltäckande SSL med Application Gateway och PowerShell

## <a name="overview"></a>Översikt

Azure Application Gateway stöder end-to-end-kryptering av trafik. Application Gateway avslutar SSL-anslutningen vid programgatewayen. Gatewayen tillämpar sedan routningsreglerna på trafiken, krypterar om paketet och vidarebefordrar paketet till lämplig server för server för server för serverding baserat på de routningsregler som definierats. Eventuella svar från webbservern genomgår samma process på väg tillbaka till användaren.

Application Gateway stöder definition av anpassade SSL-alternativ. Den stöder också inaktivera följande protokollversioner: **TLSv1.0,** **TLSv1.1**och **TLSv1.2**, samt definiera vilka chiffersviter som ska användas och prioritetsordningen. Mer information om konfigurerbara SSL-alternativ finns i [översikten över SSL-principen](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 och SSL 3.0 är inaktiverade som standard och kan inte aktiveras. De anses osäkra och kan inte användas med Application Gateway.

![scenariobild][scenario]

## <a name="scenario"></a>Scenario

I det här fallet lär du dig hur du skapar en programgateway med hjälp av end-to-end SSL med PowerShell.

Detta scenario kommer att:

* Skapa en resursgrupp med namnet **appgw-rg**.
* Skapa ett virtuellt nätverk med namnet **appgwvnet** med ett adressutrymme **på 10.0.0.0/16**.
* Skapa två undernät som kallas **appgwsubnet** och **appsubnet**.
* Skapa en liten programgateway som stöder SSL-kryptering från slutpunkt till slutpunkt som begränsar SSL-protokollversioner och chiffersviter.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att konfigurera ssl från slutpunkt till slutpunkt krävs ett certifikat för gatewayen och certifikat krävs för backend-servrarna. Gateway-certifikatet används för att härleda en symmetrisk nyckel enligt SSL-protokollspecifikationen. Den symmetriska nyckeln används sedan kryptera och dekryptera trafiken som skickas till gatewayen. Gateway-certifikatet måste vara i PFX-format (Personal Information Exchange). Med det här filformatet kan du exportera den privata nyckeln som krävs av programgatewayen för att utföra kryptering och dekryptering av trafik.

För ssl-kryptering från slutpunkt till slutpunkt måste den bakre delen uttryckligen tillåtas av programgatewayen. Ladda upp det offentliga certifikatet för backend-servrarna till programgatewayen. Genom att lägga till certifikatet säkerställs att programgatewayen endast kommunicerar med kända backend-instanser. Detta säkerställer ytterligare kommunikation från slutna till.

Konfigurationsprocessen beskrivs i följande avsnitt.

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

I det här avsnittet får du hjälp med att skapa en resursgrupp som innehåller programgatewayen.

1. Logga in på ditt Azure-konto.

   ```powershell
   Connect-AzAccount
   ```

2. Välj den prenumeration som ska användas för det här scenariot.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Skapa en resursgrupp. (Hoppa över det här steget om du använder en befintlig resursgrupp.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Skapa ett virtuellt nätverk och ett undernät för programgatewayen

I följande exempel skapas ett virtuellt nätverk och två undernät. Ett undernät används för att lagra programgatewayen. Det andra undernätet används för de bakre ändar som är värd för webbprogrammet.

1. Tilldela ett adressintervall för det undernät som ska användas för programgatewayen.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Undernät som konfigurerats för en programgateway bör ha rätt storlek. En programgateway kan konfigureras för upp till 10 instanser. Varje instans tar en IP-adress från undernätet. För liten av ett undernät kan påverka skalning av en programgateway negativt.
   >

2. Tilldela ett adressintervall som ska användas för backend-adresspoolen.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Skapa ett virtuellt nätverk med de undernät som definierats i föregående steg.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Hämta den virtuella nätverksresursen och undernätsresurserna som ska användas i de steg som följer.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs som ska användas för programgatewayen. Den här offentliga IP-adressen används i ett av stegen som följer.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway stöder inte användning av en offentlig IP-adress som skapats med en definierad domänetikett. Endast en offentlig IP-adress med en dynamiskt skapad domänetikett stöds. Om du behöver ett eget DNS-namn för programgatewayen rekommenderar vi att du använder en CNAME-post som ett alias.

## <a name="create-an-application-gateway-configuration-object"></a>Skapa ett konfigurationsobjekt för programgatewayen

Alla konfigurationsobjekt anges innan programgatewayen skapas. Följande steg skapar konfigurationsobjekten som behövs för en programgatewayresurs.

1. Skapa en IP-konfiguration för programgateway. Den här inställningen konfigurerar vilket av undernäten som programgatewayen använder. När programgatewayen startar hämtar den en IP-adress från det konfigurerade undernätet och dirigerar nätverkstrafik till IP-adresserna i den bakre IP-poolen. Tänk på att varje instans använder en IP-adress.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Skapa en ip-konfiguration för frontend. Den här inställningen mappar en privat eller offentlig IP-adress till klientdelen av programgatewayen. Följande steg associerar den offentliga IP-adressen i föregående steg med frontend IP-konfigurationen.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Konfigurera ip-adresspoolen för backend med IP-adresserna för backend-webbservrarna. De här IP-adreserna är de IP-adresser som tar emot den nätverkstrafik som kommer från frontend-IP-slutpunkten. Ersätt IP-adresserna i exemplet med dina egna ip-adressslutpunkter för programmet.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Ett fullständigt kvalificerat domännamn (FQDN) är också ett giltigt värde som ska användas i stället för en IP-adress för backend-servrarna. Du aktiverar den med **växeln -BackendFqdns.** 

4. Konfigurera ip-porten för frontend för den offentliga IP-slutpunkten. Den här porten är den port som slutanvändare ansluter till.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Konfigurera certifikatet för programgatewayen. Det här certifikatet används för att dekryptera och återkryptera trafiken på programgatewayen.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Det här exemplet konfigurerar certifikatet som används för SSL-anslutningen. Certifikatet måste vara i PFX-format och lösenordet måste vara 4 till 12 tecken.

6. Skapa HTTP-lyssnaren för programgatewayen. Tilldela klient-END IP-konfiguration, port och SSL-certifikat som ska användas.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Ladda upp certifikatet som ska användas på de SSL-aktiverade backend-poolresurserna.

   > [!NOTE]
   > Standardavsökningen hämtar den *default* offentliga nyckeln från standard-SSL-bindningen på backend-adressen och jämför det offentliga nyckelvärdet som den får med det offentliga nyckelvärde som du anger här. 
   > 
   > Om du använder värdhuvuden och SNI (Server Name Indication) på serverdelen kanske den hämtade offentliga nyckeln inte är den avsedda platsen som trafiken flödar till. Om du är osäker, https://127.0.0.1/ besök på backend-servrar för att bekräfta vilket certifikat som används för *standard* SSL-bindning. Använd den offentliga nyckeln från den begäran i det här avsnittet. Om du använder värdhuvuden och SNI på HTTPS-bindningar och du inte får https://127.0.0.1/ något svar och certifikat från en manuell webbläsarbegäran till på backend-servrarna måste du ställa in en SSL-standardbindning för dem. Om du inte gör det misslyckas avsökningarna och den bakre delen är inte vitlistad.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Certifikatet som anges i föregående steg bör vara den offentliga nyckeln till PFX-certifikatet som finns på serverdelen. Exportera certifikatet (inte rotcertifikatet) som är installerat på serverdelsservern i formatet Anspråk, Bevis och Resonemang (CER) och använd det i det här steget. Det här steget vitlistar baksidan med programgatewayen.

   Om du använder Application Gateway v2 SKU skapar du ett betrott rotcertifikat i stället för ett autentiseringscertifikat. Mer information finns i [Översikt över SSL från till med Application Gateway:](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Konfigurera HTTP-inställningarna för startservern för programgatewayen. Tilldela certifikatet som överförts i föregående steg till HTTP-inställningarna.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   För Application Gateway v2 SKU använder du följande kommando:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Skapa en routningsregel för belastningsutjämnare som konfigurerar belastningsutjämnarbeteendet. I det här exemplet skapas en grundläggande round-robin-regel.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Konfigurera programgatewayens instansstorlek. De tillgängliga storlekarna är **Standard\_Small,** **Standard\_Medium**och Standard **\_Large**.  För kapacitet är de tillgängliga värdena **1** till **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Ett instansantal på 1 kan väljas för testning. Det är viktigt att veta att alla instansantal under två instanser inte omfattas av serviceavtalet och därför inte rekommenderas. Små gateways ska användas för utvecklingstest och inte för produktionsändamål.

11. Konfigurera SSL-principen som ska användas på programgatewayen. Application Gateway stöder möjligheten att ange en minimiversion för SSL-protokollversioner.

    Följande värden är en lista över protokollversioner som kan definieras:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    I följande exempel anges den minsta protokollversionen som **ska TLSv1_2** och aktiverar **TLS\_ECDHE\_ECDSA\_MED\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_MED\_AES\_256\_GCM\_SHA384**och **TLS\_RSA\_MED\_ENDAST AES\_128\_GCM\_SHA256.**

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>Skapa programgatewayen

Skapa programgatewayen med alla föregående steg. Skapandet av gatewayen är en process som tar lång tid att köra.

För V1 SKU använd kommandot nedan
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

För V2 SKU använd kommandot nedan
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Använda ett nytt certifikat om backend-certifikatet har upphört att gälla

Använd den här proceduren om du vill använda ett nytt certifikat om backend-certifikatet har upphört att gälla.

1. Hämta programgatewayen för att uppdatera.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Lägg till den nya certifikatresursen från .cer-filen, som innehåller certifikatets offentliga nyckel och kan också vara samma certifikat som läggs till i lyssnaren för SSL-avslutning vid programgatewayen.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Hämta det nya autentiseringscertifikatobjektet till en variabel (TypeName: Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Tilldela det nya certifikatet till inställningen **Serverdelhttp** och referera det med variabeln $AuthCert. (Ange det HTTP-inställningsnamn som du vill ändra.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Genomför ändringen i programgatewayen och skicka den nya konfigurationen som finns i variabeln $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Ta bort ett oanvänt utgånget certifikat från HTTP-inställningar

Använd den här proceduren om du vill ta bort ett oanvänt utgånget certifikat från HTTP-inställningar.

1. Hämta programgatewayen för att uppdatera.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Ange namnet på det autentiseringscertifikat som du vill ta bort.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Ta bort autentiseringscertifikatet från en programgateway.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Genomför ändringen.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Begränsa SSL-protokollversioner på en befintlig programgateway

De föregående stegen tog dig igenom att skapa ett program med end-to-end SSL och inaktivera vissa SSL-protokollversioner. I följande exempel inaktiveras vissa SSL-principer för en befintlig programgateway.

1. Hämta programgatewayen för att uppdatera.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definiera en SSL-princip. I följande exempel är **TLSv1.0** och **TLSv1.1** inaktiverade och chiffersviterna **\_TLS ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**och **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** är de enda tillåtna.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Slutligen uppdaterar du gatewayen. Det sista steget är en tidskrävande uppgift. När det är klart konfigureras SSL från slutpunkt till slutpunkt på programgatewayen.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Hämta ett DNS-namn för programgateway

När gatewayen har skapats är nästa steg att konfigurera klientdelen för kommunikation. Application Gateway kräver ett dynamiskt tilldelat DNS-namn när du använder en offentlig IP, vilket inte är användarvänligt. Om du vill vara säker på att slutanvändarna kan nå programgatewayen kan du använda en CNAME-post för att peka på den offentliga slutpunkten för programgatewayen. Mer information finns i [Konfigurera ett eget domännamn för i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Om du vill konfigurera ett alias hämtar du information om programgatewayen och dess associerade IP/DNS-namn med hjälp av elementet **PublicIPAddress** som är kopplat till programgatewayen. Använd programgatewayens DNS-namn för att skapa en CNAME-post som pekar de två webbprogrammen till det här DNS-namnet. Vi rekommenderar inte användning av A-poster, eftersom VIP kan ändras vid omstart av programgatewayen.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
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

Mer information om hur du härdar säkerheten för dina webbprogram med brandvägg för webbprogram via Application Gateway finns i [översikten över brandväggen för webbprogram](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png
