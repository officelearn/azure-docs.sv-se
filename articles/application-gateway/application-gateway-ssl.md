---
title: TLS avlastning med PowerShell - Azure Application Gateway
description: Den här artikeln innehåller instruktioner för att skapa en programgateway med TLS-avlastning med hjälp av azure-klassiska distributionsmodellen
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 2ead16b61784b8073d50b7e0e6079805a1e48e9b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312331"
---
# <a name="configure-an-application-gateway-for-tls-offload-by-using-the-classic-deployment-model"></a>Konfigurera en programgateway för TLS-avlastning med hjälp av den klassiska distributionsmodellen

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Klassiska Azure-PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Azure Application Gateway kan konfigureras för att avsluta Transport Layer Security (TLS), tidigare känd som Secure Sockets Layer (SSL), session vid gatewayen för att undvika kostsamma TLS-dekrypteringsuppgifter som ska utföras på webbgruppen. TLS-avlastning förenklar också klientserverinstallationen och hanteringen av webbprogrammet.

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Kontrollera att du har ett fungerande virtuellt nätverk med ett giltigt undernät. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Programgatewayen måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för att använda programgatewayen måste finnas eller ha sina slutpunkter som skapas antingen i det virtuella nätverket eller med en offentlig IP-adress eller virtuell IP-adress (VIP) tilldelad.

Så här konfigurerar du TLS-avlastning på en programgateway:

1. [Skapa en programgateway](#create-an-application-gateway)
2. [Ladda upp TLS/SSL-certifikat](#upload-tlsssl-certificates)
3. [Konfigurera en gateway](#configure-the-gateway)
4. [Ange gatewaykonfiguration](#set-the-gateway-configuration)
5. [Starta gatewayen](#start-the-gateway)
6. [Kontrollera statusen för gatewayen](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Om du vill skapa `New-AzureApplicationGateway` gatewayen anger du cmdleten och ersätter värdena med din egen. Faktureringen för gatewayen startar inte i det här läget. Faktureringen börjar i ett senare skede när gatewayen har startats.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Om du vill verifiera att gatewayen `Get-AzureApplicationGateway` har skapats kan du ange cmdleten.

I exemplet är **Description,** **InstanceCount**och **GatewaySize** valfria parametrar. Standardvärdet för **InstanceCount** är **2**, med ett maximalt värde **på 10**. Standardvärdet för **GatewaySize** är **Medium**. Små och stora är andra tillgängliga värden. **VirtualIPs** och **DnsName** visas som tomma eftersom gatewayen inte har startat ännu. Dessa värden skapas när gatewayen är i körläge.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-tlsssl-certificates"></a>Ladda upp TLS/SSL-certifikat

Ange `Add-AzureApplicationGatewaySslCertificate` att överföra servercertifikatet i PFX-format till programgatewayen. Certifikatnamnet är ett användarvalt namn och måste vara unikt i programgatewayen. Det här certifikatet refereras till med det här namnet i alla certifikathanteringsåtgärder på programgatewayen.

Följande prov visar cmdleten. Ersätt värdena i exemplet med dina egna.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Validera sedan certifikatöverföringen. Gå `Get-AzureApplicationGatewayCertificate` in i cmdleten.

Följande prov visar cmdleten på den första raden, följt av utdata:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> Certifikatets lösenord måste vara mellan 4 och 12 tecken som består av bokstäver eller siffror. Specialtecken accepteras inte.

## <a name="configure-the-gateway"></a>Konfigurera en gateway

En konfiguration av programgateway består av flera värden. Värdena kan kopplas ihop för att konstruera konfigurationen.

Värdena är:

* **Backend-serverpool:** Listan över IP-adresser för backend-servrar. IP-adresserna som anges ska tillhöra det virtuella nätverksundernätet eller vara en offentlig IP- eller VIP-adress.
* **Serverpoolinställningar för backend:** Varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Front-end port**: Den här porten är den offentliga porten som öppnas på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare**: Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga) och TLS/SSL-certifikatnamnet (om du konfigurerar en TLS-avlastning).
* **Regel**: Regeln binder lyssnaren och serverpoolen för backend och definierar vilken serverpool som ska styras när den träffar en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

**Ytterligare konfigurationsanmärkningar**

För konfigurationen av TLS/SSL-certifikat ska protokollet i **HttpListener** ändras till **Https** (skiftlägeskänslig). Lägg till **SslCert-elementet** i **HttpListener** med värdet inställt på samma namn som används i avsnittet [Ladda upp TLS/SSL-certifikat.](#upload-tlsssl-certificates) Front-end-porten bör uppdateras till **443**.

**Så här aktiverar du cookie-baserad tillhörighet:** Du kan konfigurera en programgateway för att säkerställa att en begäran från en klientsession alltid dirigeras till samma virtuella dator i webbgruppen. För att åstadkomma detta, infoga en sessionscookie som gör att gatewayen kan dirigera trafiken på rätt sätt. Du kan aktivera cookiebaserad tillhörighet genom att ange **CookieBasedAffinity** till **Enabled** i elementet **BackendHttpSettings**.

Du kan skapa konfigurationen antingen genom att skapa ett konfigurationsobjekt eller med hjälp av en XML-fil för konfiguration.
Om du vill konstruera konfigurationen med hjälp av en XML-fil för konfiguration anger du följande exempel:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Ange gatewaykonfiguration

Nu ska vi konfigurera programgatewayen. Du kan `Set-AzureApplicationGatewayConfig` ange cmdlet med antingen ett konfigurationsobjekt eller en konfigurations-XML-fil.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Starta gatewayen

När gatewayen har konfigurerats `Start-AzureApplicationGateway` anger du cmdleten för att starta gatewayen. Faktureringen för en programgateway börjar när gatewayen har startats.

> [!NOTE]
> Cmdleten `Start-AzureApplicationGateway` kan ta 15-20 minuter att avsluta.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Kontrollera statusen för gatewayen

Ange `Get-AzureApplicationGateway` cmdlet för att kontrollera status för gatewayen. Om `Start-AzureApplicationGateway` lyckades i föregående steg ska **tillståndet** **köras**och **VirtualIPs** och **DnsName** ska ha giltiga poster.

Det här exemplet visar en programgateway som är igång och redo att ta trafik:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Nästa steg

Mer information om belastningsutjämningsalternativ i allmänhet finns i:

* [Azure belastningsutjämning](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
