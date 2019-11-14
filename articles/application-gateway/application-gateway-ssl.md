---
title: SSL-avlastning med PowerShell – Azure Application Gateway
description: Den här artikeln innehåller anvisningar för att skapa en Programgateway med SSL-avlastning med hjälp av den klassiska Azure-distributions modellen
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047909"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurera en Application Gateway för SSL-avlastning med hjälp av den klassiska distributions modellen

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Klassisk Azure-PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Azure Application Gateway kan konfigureras att avsluta SSL-sessionen (Secure Sockets Layer) på gatewayen så att du undviker kostsamma SSL-dekrypteringsaktiviteter i webbservergruppen. SSL-avlastning förenklar också frontend-serverkonfigurationen och hanteringen av webbappen.

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Kontrollera att du har ett fungerande virtuellt nätverk med ett giltigt undernät. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Programgatewayen måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för att använda Application Gateway måste finnas eller ha slut punkterna som skapas antingen i det virtuella nätverket eller med en offentlig IP-adress eller virtuell IP-adress (VIP) tilldelad.

Konfigurera SSL-avlastning på en Programgateway genom att utföra följande steg i angiven ordning:

1. [Skapa en Application Gateway](#create-an-application-gateway)
2. [Ladda upp SSL-certifikat](#upload-ssl-certificates)
3. [Konfigurera gatewayen](#configure-the-gateway)
4. [Ange Gateway-konfigurationen](#set-the-gateway-configuration)
5. [Starta gatewayen](#start-the-gateway)
6. [Verifiera Gateway-status](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Skapa gatewayen genom att ange `New-AzureApplicationGateway`-cmdleten och ersätta värdena med dina egna. Faktureringen för gatewayen startar inte i det här läget. Faktureringen börjar i ett senare skede när gatewayen har startats.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Du kan kontrol lera att gatewayen har skapats genom att ange `Get-AzureApplicationGateway`-cmdlet.

I exemplet är **Description**, **InstanceCount**och **GatewaySize** valfria parametrar. Standardvärdet för **InstanceCount** är **2**, med max värdet **10**. Standardvärdet för **GatewaySize** är **medium**. Små och stora är andra tillgängliga värden. **VirtualIPs** och **DnsName** visas som tomma eftersom gatewayen ännu inte har startats. Dessa värden skapas när gatewayen är i körnings läge.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Ladda upp SSL-certifikat

Ange `Add-AzureApplicationGatewaySslCertificate` för att ladda upp Server certifikatet i PFX-format till Application Gateway. Certifikat namnet är ett namn som användaren väljer och måste vara unikt inom programgatewayen. Det här certifikatet kallas för alla certifikat hanterings åtgärder på Application Gateway.

I följande exempel visas cmdleten. Ersätt värdena i exemplet med dina egna.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Verifiera sedan certifikat uppladdning. Ange `Get-AzureApplicationGatewayCertificate`-cmdlet.

I följande exempel visas cmdleten på den första raden, följt av utdata:

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
> Certifikat lösen ordet måste innehålla mellan 4 och 12 tecken som består av bokstäver eller siffror. Specialtecken accepteras inte.

## <a name="configure-the-gateway"></a>Konfigurera gatewayen

En Application Gateway-konfiguration består av flera värden. Värdena kan vara kopplade till varandra för att skapa konfigurationen.

Värdena är:

* **Backend-serverpoolen**: listan med IP-adresser för backend-servrarna. IP-adresserna i listan ska tillhöra det virtuella nätverkets undernät eller vara en offentlig IP-adress eller en VIP-adress.
* **Server dels inställningar för Server**delar: varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Klient dels port**: den här porten är den offentliga porten som öppnas på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare**: lyssnaren har en frontend-port, ett protokoll (http eller https; dessa värden är Skift läges känsliga) och SSL-certifikatets namn (om du konfigurerar en SSL-avlastning).
* **Regel**: regeln binder lyssnaren och backend-serverpoolen och definierar vilken backend-serverpoolen som ska dirigera trafiken till när den träffar en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

**Ytterligare konfigurations anmärkningar**

För konfiguration av SSL-certifikat bör protokollet i **HttpListener** ändras till **Https** (skiftlägeskänsligt). Lägg till **SslCert** -elementet i **HttpListener** med värdet ange samma namn som används i avsnittet [Ladda upp SSL-certifikat](#upload-ssl-certificates) . Klient dels porten bör uppdateras till **443**.

**För att aktivera cookie-baserad tillhörighet**: du kan konfigurera en Programgateway för att säkerställa att en begäran från en klientsession alltid dirigeras till samma virtuella dator i webb server gruppen. Det gör du genom att sätta i en sessions-cookie som tillåter att gatewayen dirigerar trafiken på rätt sätt. Du kan aktivera cookiebaserad tillhörighet genom att ange **CookieBasedAffinity** till **Enabled** i elementet **BackendHttpSettings**.

Du kan skapa konfigurationen antingen genom att skapa ett konfigurations objekt eller genom att använda en konfigurations-XML-fil.
Ange följande exempel för att skapa konfigurationen med hjälp av en konfigurations-XML-fil:


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

## <a name="set-the-gateway-configuration"></a>Ange Gateway-konfigurationen

Nu ska vi konfigurera programgatewayen. Du kan ange `Set-AzureApplicationGatewayConfig`-cmdlet med antingen ett konfigurations objekt eller en konfigurations-XML-fil.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Starta gatewayen

När gatewayen har kon figurer ATS anger du `Start-AzureApplicationGateway` cmdlet för att starta gatewayen. Faktureringen för en programgateway börjar när gatewayen har startats.

> [!NOTE]
> `Start-AzureApplicationGateway` cmdleten kan ta 15-20 minuter att slutföra.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Kontrollera statusen för gatewayen

Kontrol lera gatewayens status genom att ange `Get-AzureApplicationGateway`-cmdlet. Om `Start-AzureApplicationGateway` lyckades i föregående steg, ska **statusen** vara **igång**och **VirtualIPs** och **DnsName** ska ha giltiga poster.

Det här exemplet visar en Programgateway som är igång, körs och redo att ta trafik:

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

Mer information om alternativ för belastnings utjämning i allmänhet finns i:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
