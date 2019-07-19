---
title: Konfigurera Load Balancer TCP timeout för inaktivitet i Azure
titlesuffix: Azure Load Balancer
description: Konfigurera Load Balancer timeout för TCP-inaktivitet
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: b3df1ead7a3164ffd9a4b4acf8820d0f5b82cee3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274169"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurera timeout-inställningar för TCP-inaktivitet för Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

I standard konfigurationen har Azure Load Balancer en timeout-inställning på 4 minuter. Om en period av inaktivitet är längre än timeout-värdet finns det ingen garanti för att TCP-eller HTTP-sessionen upprätthålls mellan klienten och moln tjänsten.

När anslutningen är stängd kan klient programmet få följande fel meddelande: "Den underliggande anslutningen stängdes: En anslutning som förväntades hållas aktiv stängdes av servern. "

En vanlig metod är att använda en TCP Keep-Alive. Den här metoden håller anslutningen aktiv under en längre period. Mer information finns i dessa [.net-exempel](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). När Keep-Alive är aktiverat skickas paketen under perioder av inaktivitet på anslutningen. Dessa Keep-Alive-paket ser till att timeout-värdet för inaktivitet aldrig nås och att anslutningen upprätthålls under en längre period.

Den här inställningen fungerar endast för inkommande anslutningar. För att undvika att anslutningen bryts måste du konfigurera TCP Keep-Alive med ett intervall som är lägre än tids gränsen för inaktivitet eller öka tids gränsen för inaktivitet. För att stödja sådana scenarier har vi lagt till stöd för en konfigurerbar tids gräns för inaktivitet. Du kan nu ange den som en varaktighet på 4 till 30 minuter.

TCP Keep-Alive fungerar bra för scenarier där batteri tiden inte är en begränsning. Det rekommenderas inte för mobila program. Genom att använda en TCP Keep-Alive i ett mobilt program kan du tömma enhetens batteri snabbare.

![TCP-tidsgräns](./media/load-balancer-tcp-idle-timeout/image1.png)

I följande avsnitt beskrivs hur du ändrar inställningarna för tids gräns för inaktivitet i virtuella datorer och moln tjänster.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurera TCP-tidsgräns för din offentliga IP-adress på instans nivå till 15 minuter

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` är valfritt. Om den inte har angetts är standard tids gränsen 4 minuter. Det acceptabla tids gräns intervallet är 4 till 30 minuter.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Ange tids gräns för inaktivitet när du skapar en Azure-slutpunkt på en virtuell dator

Om du vill ändra timeout-inställningen för en slut punkt använder du följande:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Använd följande kommando för att hämta timeout-konfigurationen för inaktivitet:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Ange TCP-tidsgräns för en belastningsutjämnad slut punkts uppsättning

Om slut punkter är en del av en belastningsutjämnad slut punkts uppsättning måste TCP-tidsgräns anges i den belastningsutjämnade slut punkts uppsättningen. Exempel:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Ändra timeout-inställningar för moln tjänster

Du kan använda Azure SDK för att uppdatera din moln tjänst. Du gör slut punkts inställningarna för moln tjänster i. csdef-filen. Uppdatering av TCP-timeout för distribution av en moln tjänst kräver en distributions uppgradering. Ett undantag är om TCP-tidsgräns endast anges för en offentlig IP-adress. Offentliga IP-inställningar finns i. cscfg-filen och du kan uppdatera dem via distributions uppdatering och uppgradering.

Ändringarna av. csdef för slut punkts inställningar är:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Ändringarna i. cscfg-filen för timeout-inställningen på offentliga IP-adresser är:

```xml
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
    </InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="rest-api-example"></a>REST API exempel

Du kan konfigurera timeout för TCP-inaktivitet med hjälp av Service Management-API: et. Kontrol lera `x-ms-version` att sidhuvudet är inställt `2014-06-01` på version eller senare. Uppdatera konfigurationen av de angivna belastningsutjämnade slut punkterna för indatakälla på alla virtuella datorer i en distribution.

### <a name="request"></a>Förfrågan

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Svar

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Nästa steg

[Översikt över intern belastnings utjämning](load-balancer-internal-overview.md)

[Kom igång med att konfigurera en Internet-riktad belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)

[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)
