---
title: Konfigurera timeout för inaktivitet för Load Balancer TCP i Azure
titlesuffix: Azure Load Balancer
description: Konfigurera tidsgräns vid inaktivitet Load Balancer TCP
services: load-balancer
documentationcenter: na
author: kumudd
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0c57eec4d739da13d98099a6b2f01fbf0ad0051c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57857546"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurera inställningar för TCP-tidsgräns vid inaktivitet för Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Med standardkonfigurationen har Azure Load Balancer en timeout för inaktivitet på 4 minuter. Om en period av inaktivitet är längre än timeout-värdet, finns det ingen garanti som hålls TCP eller HTTP-session mellan klienten och din molntjänst.

När anslutningen är stängd kan klientprogrammet få följande felmeddelande visas: ”Den underliggande anslutningen stängdes: En anslutning som förväntades hållas aktiv stängdes av servern ”.

En vanlig metod är att använda en keep-alive TCP. Den här övningen håller anslutningen aktiv under en längre period. Mer information finns i dessa [.NET-exempel](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Med keep-alive aktiverat, paket skickas under perioder av inaktivitet på anslutningen. Paketen keep-alive Kontrollera att värdet för tidsgränsen för inaktivitet nås aldrig och anslutningen bevaras under lång tid.

Den här inställningen fungerar för inkommande anslutningar. För att undvika att förlora anslutningen kan du konfigurera TCP keep-alive med ett intervall som är mindre än inställningen för timeout för inaktivitet eller öka värdet för timeout för inaktivitet. Vi har lagt till stöd för en konfigurerbar timeout för inaktivitet för att stödja sådana scenarier. Du kan nu ange det under en period på 4 till 30 minuter.

TCP keep-alive fungerar bra för scenarier där batteritid inte är en begränsning. Det rekommenderas inte för mobila program. Med hjälp av en TCP keep-alive i en hanteringsprincip för mobilprogram kan förbrukar enhet snabbare.

![Timeout för TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

I följande avsnitt beskrivs hur du ändrar inställningarna för timeout för inaktivitet i virtuella datorer och molntjänster.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurera TCP-tidsgräns för din offentliga IP på instansnivå till 15 minuter

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` är valfritt. Om den inte är inställd är på standardvärdet för timeout 4 minuter. Godkända timeout-intervall är 4 till 30 minuter.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Ange tidsgränsen för inaktivitet när du skapar en Azure-slutpunkt på en virtuell dator

Om du vill ändra timeout-inställningen för en slutpunkt, använder du följande:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Om du vill hämta din konfiguration för timeout för inaktivitet, använder du följande kommando:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Ange TCP-tidsgränsen på en belastningsutjämnad slutpunktsuppsättning

Om slutpunkter är en del av en belastningsutjämnad slutpunktsuppsättning, måste TCP-tidsgräns anges för den belastningsutjämnade slutpunktsuppsättning. Exempel:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Ändra inställningarna för timeout för cloud services

Du kan använda Azure SDK för att uppdatera din molntjänst. Du kan göra slutpunktsinställningarna för molntjänster i .csdef-filen. Uppdatering av TCP-tidsgränsen för distribution av en molnbaserad tjänst kräver en distributionsuppgradering av. Ett undantag är om TCP-tidsgräns anges endast för en offentlig IP-adress. Offentliga IP-inställningar finns i .cscfg-filen. Du kan uppdatera dem via uppdatering av programdistribution och uppgradering.

.Csdef ändringarna inställningarna för slutpunkten är:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

.Cscfg ändringarna för timeout-inställningen på offentliga IP-adresser är:

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

## <a name="rest-api-example"></a>REST API-exempel

Du kan konfigurera TCP-tidsgränsen för inaktivitet med hjälp av API för tjänsthantering. Se till att den `x-ms-version` huvud har angetts till version `2014-06-01` eller senare. Uppdatera konfigurationen av de angivna belastningsutjämnade slutpunkterna för indata på alla virtuella datorer i en distribution.

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

[Översikt över intern belastningsutjämnare](load-balancer-internal-overview.md)

[Kom igång med att konfigurera en internetuppkopplad belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)

[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)
