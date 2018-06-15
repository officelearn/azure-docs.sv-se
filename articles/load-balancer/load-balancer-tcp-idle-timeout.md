---
title: Konfigurera Load Balancer TCP timeout för inaktivitet | Microsoft Docs
description: Konfigurera timeout för inaktivitet Load Balancer TCP
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 4625c6a8-5725-47ce-81db-4fa3bd055891
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: f19ac77f7c7f7d4ab8909d628f9dcce08c07c928
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855231"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurera inställningar för TCP-tidsgränsen för inaktivitet för Azure belastningsutjämnare

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

I standardkonfigurationen har Azure belastningsutjämnare en timeout för inaktivitet inställning för 4 minuter. Om en period av inaktivitet är längre än timeout-värde, finns det inga garantier som hålls TCP eller HTTP-session mellan klienten och Molntjänsten.

När anslutningen är stängd klientprogrammet får följande felmeddelande ”: den underliggande anslutningen stängdes: en anslutning som förväntades hållas aktiv stängdes av servern”.

En vanlig metod är att använda ett keep-alive TCP. Den här övningen håller anslutningen aktiv under en längre period. Mer information finns i dessa [.NET exempel](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Med keep-alive aktiverat, paket skickas under perioder av inaktivitet för anslutningen. Dessa keep alive-paket Kontrollera att värdet för tidsgränsen för inaktivitet nåddes och anslutningen bibehålls under lång tid.

Den här inställningen fungerar för inkommande anslutningar. Om du vill undvika att förlora anslutningen, måste du konfigurera TCP keep-alive med ett intervall som är mindre än inställningen för inaktiv eller öka värdet för timeout för inaktivitet. Vi har lagt till stöd för en konfigurerbar timeout vid inaktivitet för att stödja dessa scenarier. Du kan nu ange en varaktighet på 4 till 30 minuter.

TCP keep-alive fungerar bra för scenarier där batterilivslängd inte är en begränsning. Det rekommenderas inte för mobila program. Med hjälp av ett keep-alive i ett mobilt program TCP kan förbrukar enheten snabbare.

![Tidsgränsen för TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

I följande avsnitt beskrivs hur du ändrar inställningar för timeout för inaktivitet i virtuella datorer och molntjänster.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurera TCP-tidsgränsen för din offentliga IP instansnivå till 15 minuter

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` är valfritt. Om det inte har angetts är är standardvärdet för timeout 4 minuter. Godkända timeout-intervallet är 4 till 30 minuter.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Ange tidsgränsen för inaktivitet när du skapar en Azure slutpunkt på en virtuell dator

Om du vill ändra timeout-inställningen för en slutpunkt, använder du följande:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

För att hämta konfigurationen av tidsgränsen för inaktivitet, använder du följande kommando:

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

Om slutpunkter är en del av en belastningsutjämnad slutpunktsuppsättning, måste du ange den TCP-tidsgränsen på belastningsutjämnad slutpunktsuppsättning. Exempel:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Ändra timeout-inställningar för molntjänster

Du kan använda Azure SDK för att uppdatera Molntjänsten. Du gör slutpunktsinställningar för för molntjänster i .csdef-filen. Uppdatering av TCP-tidsgränsen för distribution av en molnbaserad tjänst kräver en uppgradering av distributionen. Ett undantag är om TCP-tidsgränsen anges endast för en offentlig IP-adress. Den offentliga IP-inställningar finns i .cscfg-filen och du kan uppdatera dem via uppdatering av programdistribution och uppgradering.

.Csdef ändringarna för slutpunktsinställningar är:

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

Du kan konfigurera TCP-tidsgränsen för inaktivitet med hjälp av service management API. Se till att den `x-ms-version` huvud har angetts till version `2014-06-01` eller senare. Uppdatera konfigurationen av de angivna belastningsutjämnade inkommande slutpunkterna på alla virtuella datorer i en distribution.

### <a name="request"></a>Förfrågan

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Svar

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

[Översikt över interna belastningsutjämnare](load-balancer-internal-overview.md)

[Kom igång med att konfigurera en Internetriktade belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)
