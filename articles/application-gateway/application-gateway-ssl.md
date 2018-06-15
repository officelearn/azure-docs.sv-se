---
title: Konfigurera SSL avlastning - Azure Application Gateway - PowerShell klassisk | Microsoft Docs
description: Den här artikeln innehåller instruktioner för att skapa en Programgateway med SSL avlasta med hjälp av Azure klassiska distributionsmodellen
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: e620730b86d648c1ac9db7a9e6faa7a2d206b46e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205388"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurera en Programgateway för SSL-avlastning med hjälp av den klassiska distributionsmodellen

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure klassiska PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway kan konfigureras att avsluta SSL-sessionen (Secure Sockets Layer) på gatewayen så att du undviker kostsamma SSL-dekrypteringsaktiviteter i webbservergruppen. SSL-avlastning förenklar också frontend-serverkonfigurationen och hanteringen av webbappen.

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Kontrollera att du har ett fungerande virtuellt nätverk med ett giltigt undernät. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Programgatewayen måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för att använda programmet gateway måste finns eller har de slutpunkter som skapas i det virtuella nätverket eller med en offentlig IP-adress eller en virtuell IP-adress (VIP).

Om du vill konfigurera en Programgateway SSL-avlastning, gör du följande i angiven ordning:

1. [Skapa en Programgateway](#create-an-application-gateway)
2. [Överför SSL-certifikat](#upload-ssl-certificates)
3. [Konfigurera gatewayen](#configure-the-gateway)
4. [Ange gateway-konfiguration](#set-the-gateway-configuration)
5. [Starta gatewayen](#start-the-gateway)
6. [Kontrollera gatewaystatusen](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Om du vill skapa en gateway, ange den `New-AzureApplicationGateway` cmdlet, där du ersätter värdena med dina egna. Faktureringen för gatewayen startar inte i det här läget. Faktureringen börjar i ett senare skede när gatewayen har startats.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Kontrollera att gatewayen har skapats kan du ange den `Get-AzureApplicationGateway` cmdlet.

I det här exemplet **beskrivning**, **InstanceCount**, och **GatewaySize** är valfria parametrar. Standardvärdet för **InstanceCount** är **2**, med ett maximalt värde för **10**. Standardvärdet för **GatewaySize** är **medel**. Små och stora är andra tillgängliga värden. **Virtualip:** och **DnsName** visas som tomt eftersom gatewayen inte har startat ännu. Dessa värden skapas när den är i körläge.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Överför SSL-certifikat

Ange `Add-AzureApplicationGatewaySslCertificate` att ladda upp certifikatet i PFX-format för programgatewayen. Certifikatets namn är ett namn som valts av användaren och måste vara unika inom programgatewayen. Det här certifikatet anges med detta namn i alla certifikat hanteringsåtgärder på programgatewayen.

I följande exempel visas cmdleten. Ersätt värdena i exemplet med din egen.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Därefter Validera uppladdningen av certifikatet. Ange den `Get-AzureApplicationGatewayCertificate` cmdlet.

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
> Lösenord för certifikatet måste innehålla mellan 4 och 12 tecken som består av bokstäver eller siffror. Specialtecken godkänns inte.

## <a name="configure-the-gateway"></a>Konfigurera gatewayen

En gateway programkonfigurationen består av flera värden. Värdena kan knytas samman för att konstruera konfigurationen.

Värdena är:

* **Backend-serverpoolen**: listan över IP-adresser för backend-servrar. IP-adresser som anges ska tillhöra undernät för virtuellt nätverk eller vara en offentlig IP- eller VIP-adress.
* **Backend-server poolinställningarna**: varje pool har inställningar som port och protokoll cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Frontend-port**: den här porten är den offentliga som öppnas på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare**: lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsligt), och SSL-certifikat (om du konfigurerar en SSL-avlastning).
* **Regeln**: regeln Binder lyssnaren och backend-serverpoolen och definierar vilka backend-server-pool för att dirigera trafik till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

**Information om ytterligare konfiguration**

För konfiguration av SSL-certifikat bör protokollet i **HttpListener** ändras till **Https** (skiftlägeskänsligt). Lägg till den **SslCert** elementet så att **HttpListener** med samma namn som används i värdet på [överför SSL-certifikat](#upload-ssl-certificates) avsnitt. Frontend-port som ska uppdateras till **443**.

**Så här aktiverar du cookie-baserad tillhörighet**: du kan konfigurera en Programgateway för att säkerställa att en begäran från en klientsession alltid dirigeras till samma virtuella dator i en webbservergrupp. Infoga en sessions-cookie som gör att en gateway att dirigera trafik på lämpligt sätt för att åstadkomma detta. Du kan aktivera cookiebaserad tillhörighet genom att ange **CookieBasedAffinity** till **Enabled** i elementet **BackendHttpSettings**.

Du kan skapa din konfiguration genom att skapa ett konfigurationsobjekt eller med hjälp av en XML-konfigurationsfilen.
Ange följande exempel för att skapa konfigurationen med hjälp av en XML-konfigurationsfilen:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

## <a name="set-the-gateway-configuration"></a>Ange gateway-konfiguration

Nu ska vi konfigurera programgatewayen. Du kan ange den `Set-AzureApplicationGatewayConfig` cmdlet med ett konfigurationsobjekt eller en XML-konfigurationsfilen.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Starta gatewayen

När gatewayen har konfigurerats, ange den `Start-AzureApplicationGateway` att starta gatewayen. Faktureringen för en programgateway börjar när gatewayen har startats.

> [!NOTE]
> Den `Start-AzureApplicationGateway` cmdlet kan ta 15-20 minuter.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Kontrollera statusen för gatewayen

Ange den `Get-AzureApplicationGateway` för att kontrollera status för gatewayen. Om `Start-AzureApplicationGateway` lyckades i föregående steg i **tillstånd** ska vara **kör**, och **virtualip:** och **DnsName** bör ha giltiga poster.

Det här exemplet visar en Programgateway som är igång, körs och är redo att ta trafik:

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

Mer information om belastningsutjämning i allmänhet finns i alternativ:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
