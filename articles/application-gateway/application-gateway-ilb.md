---
title: "Med intern belastningsutjämnare Azure Programgateway | Microsoft Docs"
description: "Den här sidan innehåller instruktioner för att konfigurera en Gateway för Azure-program med en intern belastningsutjämnade slutpunkt"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 7403d28e-909f-46a2-b282-43a8e942f53c
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 7ca9307e8a78f6dade4b231fa3a0d83a68af3f21
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Skapa en Application Gateway med en intern belastningsutjämnare (ILB)

> [!div class="op_single_selector"]
> * [PowerShell och den klassiska Azure-portalen](application-gateway-ilb.md)
> * [PowerShell och Azure Resource Manager](application-gateway-ilb-arm.md)

Programgateway kan konfigureras med en internetuppkopplad virtuell IP-adress eller en intern slutpunkt exponeras inte till internet, även kallat inre belastningen belastningsutjämnare (ILB) slutpunkt. Konfigurera gateway med en ILB är användbart för interna av branschspecifika program inte utsätts för internet. Det är också användbart för servicenivåerna inom en flernivåapp som placeras i en säkerhetsgräns inte utsätts för internet, men kräver fortfarande resursallokering belastningsdistribution, varaktighet för sessionen eller SSL-avslutning. Den här artikeln beskriver steg för steg hur du konfigurerar en programgateway med en ILB.

## <a name="before-you-begin"></a>Innan du börjar

1. Installera senaste versionen av Azure PowerShell-cmdlets med hjälp av Web Platform Installer. Du kan hämta och installera den senaste versionen från den **Windows PowerShell** avsnitt i den [hämtningssidan](https://azure.microsoft.com/downloads/).
2. Kontrollera att du har ett virtuellt nätverk fungerar med giltig nätmask.
3. Kontrollera att du har backend-servrar i det virtuella nätverket eller med en offentlig IP-adress/VIP tilldelad.

Utför följande steg för att skapa en Programgateway i angiven ordning. 

1. [Skapa en Programgateway](#create-a-new-application-gateway)
2. [Konfigurera gatewayen](#configure-the-gateway)
3. [Ange gateway-konfiguration](#set-the-gateway-configuration)
4. [Starta gatewayen](#start-the-gateway)
5. [Kontrollera gatewayen](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Skapa en Programgateway:

**Att skapa gatewayen**, använda den `New-AzureApplicationGateway` cmdlet, där du ersätter värdena med dina egna. Observera att faktureringen för gatewayen inte startar i det här läget. Faktureringen börjar i ett senare skede när gatewayen har startats.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

```
VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399
```

**Validera** att gatewayen har skapats kan du använda den `Get-AzureApplicationGateway` cmdlet. 

I det här exemplet *beskrivning*, *InstanceCount*, och *GatewaySize* är valfria parametrar. Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Små och stora är andra tillgängliga värden. *VIP* och *DnsName* visas som tomt eftersom gatewayen inte har startat ännu. De skapas när gatewayen är i körläge. 

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 4:39:39 PM - Begin Operation:
Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
Operation: Get-AzureApplicationGateway
Name: AppGwTest    
Description: 
VnetName: testvnet1 
Subnets: {Subnet-1} 
InstanceCount: 2 
GatewaySize: Medium 
State: Stopped 
VirtualIPs: 
DnsName:
```

## <a name="configure-the-gateway"></a>Konfigurera gatewayen
En gateway programkonfigurationen består av flera värden. Värdena kan knytas samman för att konstruera konfigurationen.

Värdena är:

* **Backend-serverpoolen:** listan över IP-adresser på backend-servrarna. IP-adresser som anges antingen ska tillhöra VNet subnet eller ska vara en offentlig IP-adress/VIP. 
* **Inställningar för backend-serverpool:** Varje pool har inställningar som port, protokoll och cookie-baserad tillhörighet. Dessa inställningar är knutna till en pool och tillämpas på alla servrar i poolen.
* **Klientdelsport:** den här porten är den offentliga öppnas på programgatewayen. Trafiken kommer till den här porten och omdirigeras till en av backend-servrarna.
* **Lyssnare:** lyssnaren har en klientdelsport, ett protokoll (Http eller Https, dessa är skiftlägeskänsligt), och namnet på SSL (om hur du konfigurerar SSL-avlastning). 
* **Regel:** regeln Binder lyssnaren och backend-serverpoolen och definierar vilka backend-serverpoolen trafiken ska dirigeras till när den når en viss lyssnare. För närvarande stöds endast regeln *basic*. Regeln *basic* använder belastningsutjämning med resursallokering.

Du kan skapa din konfiguration genom att skapa ett konfigurationsobjekt eller genom att använda en XML-konfigurationsfilen. Använd exemplet nedan för att skapa konfigurationen med hjälp av en XML-konfigurationsfilen.

Observera följande:

* Den *FrontendIPConfigurations* element beskriver vad ILB som är relevanta för att konfigurera Application Gateway med en ILB. 
* Frontend IP *typen* ska anges till ”privat”
* Den *StaticIPAddress* ska anges till den önskade intern IP-adress som gatewayen tar emot trafik. Observera att den *StaticIPAddress* element är valfria. Om du inte kan en tillgänglig intern IP-adress från undernätet som är distribuerade väljs. 
* Värdet för den *namn* element som anges i *FrontendIPConfiguration* ska användas i HTTPListener *FrontendIP* element att referera till FrontendIPConfiguration.
  
  **Konfiguration av XML-exempel**
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name> 
            <Type>Private</Type> 
            <StaticIPAddress>10.0.0.10</StaticIPAddress> 
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
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
            <FrontendIP>fip1</FrontendIP>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Http</Protocol>
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
Sedan ställer du programgatewayen. Du kan använda den `Set-AzureApplicationGatewayConfig` cmdlet med ett konfigurationsobjekt eller med en XML-konfigurationsfilen. 

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

```
VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d
```

## <a name="start-the-gateway"></a>Starta gatewayen

När gatewayen har konfigurerats kan du använda cmdleten `Start-AzureApplicationGateway` för att starta gatewayen. Faktureringen för en programgateway börjar när gatewayen har startats. 

> [!NOTE]
> Den `Start-AzureApplicationGateway` cmdlet kan ta upp till 15-20 minuter att slutföra. 
> 
> 

```powershell
Start-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b
```

## <a name="verify-the-gateway-status"></a>Kontrollera statusen för gatewayen

Använd den `Get-AzureApplicationGateway` för att kontrollera status för gateway. Om `Start-AzureApplicationGateway` lyckades i föregående steg, tillståndet ska vara *kör*, och Vip och DNS-namn ska ha giltiga poster. Det här exemplet visar cmdleten på den första raden, följt av utdata. I det här exemplet gatewayen körs och är redo att ta trafik. 

> [!NOTE]
> Programgatewayen är konfigurerad att ta emot trafik på den konfigurerade ILB-slutpunkten för 10.0.0.10 i det här exemplet.

```powershell
Get-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   : 
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {10.0.0.10}
DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net
```

## <a name="next-steps"></a>Nästa steg
Om du vill ha mer information om belastningsutjämningsalternativ i allmänhet läser du:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

