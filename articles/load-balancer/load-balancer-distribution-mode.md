---
title: Konfigurera Distributionsläge för Azure Load Balancer | Microsoft Docs
description: Så här konfigurerar du Distributionsläge för Azure Load Balancer som stöd för käll-IP-tilldelning.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jpconnock
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2018
ms.author: kumud
ms.openlocfilehash: 776621f9ef95867c6e3c25dd11c656d451b6730e
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018136"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurera Distributionsläge för belastningsutjämnare i Azure

## <a name="hash-based-distribution-mode"></a>Hash-baserade Distributionsläge

Standardläget för distribution för Azure Load Balancer är en 5-tuppel-hash. Parets består av käll-IP, källport, mål-IP, målport och protokolltypen. Hash-algoritmen som används för att mappa trafik till de tillgängliga servrarna och algoritmen ger varaktighet endast inom en transportsession. Paket som är i samma session dirigeras till samma datacenter IP (DIP) instans bakom Utjämning av nätverksbelastning-slutpunkten. När klienten startar en ny session från samma käll-IP, källport ändras och gör trafik att gå till en annan DIP-slutpunkt.

![5-tuppel-hash-baserade Distributionsläge](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Tillhörighetsläge för käll-IP

Belastningsutjämnare kan också konfigureras med hjälp av Distributionsläge för IP-tillhörighet källa. Den här Distributionsläge kallas även sessionstillhörighet eller klient-IP-tilldelning. Läget använder en 2-tuppel (käll-IP och mål-IP) eller 3-tuppel (käll-IP, mål-IP och protokoll typen) hash om du vill mappa trafik till de tillgängliga servrarna. Med hjälp av käll-IP-tilldelning, går anslutningar som initieras från samma klientdator du till samma DIP-slutpunkt.

Följande bild visar en 2-tuppel-konfiguration. Observera hur 2-tuppel körs via belastningsutjämnaren till den virtuella datorn 1 (VM1). VM1 är sedan backas upp av VM2 och VM3.

![2-tuppel sessionsläge tillhörighet distribution](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Käll-IP-tillhörighetsläge löser inkompatibilitet mellan Azure Load Balancer och fjärrskrivbordsgateway (RD Gateway). Genom att använda det här läget kan skapa du en RD Gateway-servergrupp i en enda molntjänst.

En annan användningsfall är media ladda upp. Ladda upp data sker via UDP, men kontrollplanet uppnås via TCP:

* En klient initierar en TCP-session till den offentliga adressen för Utjämning av nätverksbelastning och dirigeras till en specifik DIP. Kanalen är fortfarande aktiv för att övervaka hälsotillståndet för anslutningen.
* En ny UDP-session från samma klientdator initieras på samma Utjämning av nätverksbelastning offentliga slutpunkten. Anslutningen dirigeras till samma DIP-slutpunkt som den tidigare TCP-anslutningen. Media-överföringen kan utföras med högt dataflöde samtidigt som en kontrollkanal via TCP.

> [!NOTE]
> När en belastningsutjämnad uppsättning ändras genom att ta bort eller lägga till en virtuell dator, recomputed fördelningen av klientbegäranden. Du kan lita på nya anslutningar från befintliga klienter till slutar vid samma server. Dessutom använder käll-IP tillhörighetsläge distribution kan det orsaka en ojämn fördelning av trafik. Klienter som körs bakom proxyservrar kan ses som en unik klientprogram.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurera inställningar för datakälla IP-tillhörighet

För virtuella datorer som distribueras med Resource Manager, ändra distributionsinställningar för load balancer på belastningsutjämningsregel för belastningsutjämnaren med hjälp av PowerShell.  Detta uppdaterar Distributionsläge för en befintlig regel för belastningsutjämnaren:

```powershell 
$lb = Get-AzureRmLoadBalancer -Name MyLb -ResourceGroupName MyLbRg 
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp' 
Set-AzureRmLoadBalancer -LoadBalancer $lb 
``` 

För klassiska virtuella datorer, använder du Azure PowerShell för att ändra inställningar för distribution. Lägg till en Azure-slutpunkt för en virtuell dator och konfigurera Distributionsläge för belastningsutjämnare:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ange värdet för den `LoadBalancerDistribution` element för in önskad mängd Utjämning av nätverksbelastning. Ange sourceIP för belastningsutjämning för 2-tuppel (käll-IP och mål-IP). Ange sourceIPProtocol för 3-tuppel (käll-IP, mål-IP och protokoll typen) belastningsutjämning. Ange ingen för standardbeteendet för 5-tuppel Utjämning av nätverksbelastning.

Hämta en slutpunkt distribution läge konfigurationen för belastningsutjämnaren med hjälp av de här inställningarna:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

När den `LoadBalancerDistribution` element finns inte, Azure Load Balancer använder standard-5-tuppel-algoritmen.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurera Distributionsläge på belastningsutjämnad slutpunktsuppsättning

När slutpunkterna är en del av en belastningsutjämnad slutpunktsuppsättning, måste Distributionsläge konfigureras på belastningsutjämnad slutpunktsuppsättning:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurera Distributionsläge för Cloud Services-slutpunkter

Använd Azure SDK för .NET 2.5 för att uppdatera din molntjänst. Slutpunktsinställningarna för molntjänster görs i .csdef-filen. Om du vill uppdatera Distributionsläge för belastningsutjämnare för en Cloud Services-distribution, krävs en distributionsuppgradering av.

Här är ett exempel på .csdef ändringar av inställningarna för slutpunkten:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
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

## <a name="api-example"></a>API-exempel

I följande exempel visas hur du konfigurerar om Distributionsläge för belastningsutjämnare för en angiven mängd med Utjämning av nätverksbelastning i en distribution. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Ändra Distributionsläge för distribuerade belastningsutjämnad uppsättning

Använd den klassiska distributionsmodellen för att ändra en befintlig distributionskonfiguration. Lägg till den `x-ms-version` rubrik och ange version 2014-09-01 eller senare.

#### <a name="request"></a>Förfrågan

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Som tidigare beskrivna, ange den `LoadBalancerDistribution` elementet mot sourceIP för 2-tuppel-tillhörighet, sourceIPProtocol för 3-tuppel tillhörighet eller ingen för ingen tillhörighet (5-tuppel-tillhörighet).

#### <a name="response"></a>Svar

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure intern belastningsutjämnare](load-balancer-internal-overview.md)
* [Kom igång med att konfigurera en internetuppkopplad belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)
