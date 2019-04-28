---
title: Konfigurera SSL-avlastning – Azure Application Gateway – PowerShell – klassisk | Microsoft Docs
description: Den här artikeln innehåller instruktioner för att skapa en Programgateway med SSL-avlastning med hjälp av den klassiska distributionsmodellen
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
ms.openlocfilehash: 89a88d79b6b93a233dbd4f335d0eb449e49d5289
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122208"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurera en Programgateway för SSL-avlastning med hjälp av den klassiska distributionsmodellen

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure klassiska PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Azure Application Gateway kan konfigureras att avsluta SSL-sessionen (Secure Sockets Layer) på gatewayen så att du undviker kostsamma SSL-dekrypteringsaktiviteter i webbservergruppen. SSL-avlastning förenklar också frontend-serverkonfigurationen och hanteringen av webbappen.

## <a name="before-you-begin"></a>Innan du börjar

1. Installera den senaste versionen av Azure PowerShell-cmdlets med hjälp av installationsprogrammet för webbplattform. Du kan hämta och installera den senaste versionen från avsnittet om **Windows PowerShell** på [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Kontrollera att du har ett fungerande virtuellt nätverk med ett giltigt undernät. Kontrollera att inga virtuella datorer eller molndistributioner använder undernätet. Programgatewayen måste vara fristående i ett virtuellt nätverks undernät.
3. De servrar som du konfigurerar för användning av programgatewayen måste finns eller ha slutpunkter som skapats i det virtuella nätverket eller med en offentlig IP-adress eller en virtuell IP-adress (VIP).

Om du vill konfigurera SSL-avlastning på application gateway gör du följande i angiven ordning:

1. [Skapa en application gateway](#create-an-application-gateway)
2. [Ladda upp SSL-certifikat](#upload-ssl-certificates)
3. [Konfigurera gatewayen](#configure-the-gateway)
4. [Ange gatewaykonfiguration](#set-the-gateway-configuration)
5. [Starta gatewayen](#start-the-gateway)
6. [Kontrollera statusen för gatewayen](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Du skapar en gateway, ange den `New-AzureApplicationGateway` cmdlet och Ersätt värdena med dina egna. Faktureringen för gatewayen startar inte i det här läget. Faktureringen börjar i ett senare skede när gatewayen har startats.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Kontrollera att gatewayen har skapats kan du ange den `Get-AzureApplicationGateway` cmdlet.

I det här exemplet **beskrivning**, **InstanceCount**, och **GatewaySize** är valfria parametrar. Standardvärdet för **InstanceCount** är **2**, med ett högsta värde på **10**. Standardvärdet för **GatewaySize** är **medel**. Små och stora är andra tillgängliga värden. **VirtualIPs** och **DnsName** visas som tomma eftersom gatewayen inte har startat ännu. Dessa värden skapas när gatewayen är i körläge.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Ladda upp SSL-certifikat

Ange `Add-AzureApplicationGatewaySslCertificate` att ladda upp certifikatet i PFX-format till application gateway. Certifikatets namn är ett namn som valts av användaren och måste vara unikt i application gateway. Det här certifikatet är refereras till av det här namnet i alla certifikatshanteringsåtgärder på application gateway.

I följande exempel visas cmdleten. Ersätt värdena i exemplet med dina egna.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Sedan Verifiera uppladdningen av certifikatet. Ange den `Get-AzureApplicationGatewayCertificate` cmdlet.

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
> Lösenordet för certifikatet måste vara mellan 4 och 12 tecken som består av bokstäver eller siffror. Specialtecken tillåts inte.

## <a name="configure-the-gateway"></a>Konfigurera gatewayen

En konfiguration för programgatewayen består av flera värden. Värdena kan kopplas ihop för att konstruera konfigurationen.

Värdena är:

* **Backend-serverpoolen**: Listan med IP-adresser för backend-servrarna. De IP-adresser som anges måste tillhöra det virtuella undernätet eller vara en offentlig IP-adress eller VIP-adress.
* **Inställningar för backend-serverpool**: Varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Frontend-porten**: Den här porten är den offentliga porten som är öppen på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnaren**: Lyssnaren har en frontend-port, ett protokoll (Http eller Https; dessa värden är skiftlägeskänsliga), och SSL-certifikatnamnet (om du konfigurerar en SSL-avlastning).
* **Regeln**: Regeln Binder lyssnaren och backend-serverpoolen och definierar vilken backend-serverpool för att dirigera trafiken till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

**Ytterligare konfigurationsanmärkningar**

För konfiguration av SSL-certifikat bör protokollet i **HttpListener** ändras till **Https** (skiftlägeskänsligt). Lägg till den **SslCert** elementet mot **HttpListener** med värdet satt till samma namn som används i den [ladda upp SSL-certifikat](#upload-ssl-certificates) avsnittet. Frontend-porten ska uppdateras till **443**.

**Aktivera Cookiebaserad tillhörighet**: Du kan konfigurera en Programgateway för att säkerställa att en begäran från en klientsession alltid dirigeras till samma virtuella dator i webbservergruppen. Du åstadkommer detta genom att infoga en sessions-cookie som ser till att gatewayen dirigerar trafiken på rätt sätt. Du kan aktivera cookiebaserad tillhörighet genom att ange **CookieBasedAffinity** till **Enabled** i elementet **BackendHttpSettings**.

Du kan skapa din konfiguration genom att skapa ett konfigurationsobjekt eller med hjälp av en XML-konfigurationsfilen.
Ange följande exempel för att konstruera konfigurationen med hjälp av en XML-konfigurationsfil:


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

Nu ska vi konfigurera programgatewayen. Du kan ange den `Set-AzureApplicationGatewayConfig` cmdlet med ett konfigurationsobjekt eller en XML-konfigurationsfilen.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Starta gatewayen

När gatewayen har konfigurerats, ange den `Start-AzureApplicationGateway` cmdlet för att starta gatewayen. Faktureringen för en programgateway börjar när gatewayen har startats.

> [!NOTE]
> Den `Start-AzureApplicationGateway` cmdlet kan ta 15-20 minuter att slutföra.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Kontrollera statusen för gatewayen

Ange den `Get-AzureApplicationGateway` cmdlet för att kontrollera status för gatewayen. Om `Start-AzureApplicationGateway` lyckades i föregående steg, den **tillstånd** ska vara **kör**, och **VirtualIPs** och **DnsName** bör ha giltiga poster.

Det här exemplet visar en Programgateway som är tillgänglig, körs och är redo att ta trafik:

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
