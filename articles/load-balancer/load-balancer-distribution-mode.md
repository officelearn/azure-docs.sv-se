---
title: Konfigurera distributionsläge för Azure Load Balancer
titleSuffix: Azure Load Balancer
description: I den här artikeln kommer du igång med att konfigurera distributionsläget för Azure Load Balancer för att stödja käll-IP-tillhörighet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023539"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurera distributionsläget för Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Hash-baserat distributionsläge

Standarddistributionsläget för Azure Load Balancer är en hash med fem tupplar. 

Tuppeln består av:
* **Käll-IP**
* **Källa port**
* **Destination IP**
* **Destinationsport**
* **Protokolltyp**

Hash används för att mappa trafik till tillgängliga servrar. Algoritmen ger klibbighet endast inom en transportsession. Paket som finns i samma session dirigeras till samma datacenter-IP bakom den belastningsbalanserade slutpunkten. När klienten startar en ny session från samma käll-IP ändras källporten och gör att trafiken går till en annan datacenterslutpunkt.

![Fem-tuppel hash-baserat distributionsläge](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Läge för käll-IP-tillhörighet

Belastningsutjämnaren kan också konfigureras med hjälp av käll-IP-tillhörighetsdistributionsläget. Det här distributionsläget kallas även för sessionstillhörighet eller klientens IP-tillhörighet. Läget använder en två-tuppel (källa IP och destination IP) eller tre-tuppel (källa IP, mål IP och protokolltyp) hash för att mappa trafik till tillgängliga servrar. Genom att använda käll-IP-tillhörighet går anslutningar som startas från samma klientdator till samma datacenterslutpunkt.

Följande bild illustrerar en konfiguration med två tuppel. Lägg märke till hur två-tuppel löper genom belastningsutjämnaren till virtuell dator 1 (VM1). VM1 backas sedan upp av VM2 och VM3.

![Distributionsläge för två tuppelsessionstillhörighet](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Käll-IP-tillhörighetsläge löser en inkompatibilitet mellan Azure Load Balancer och Remote Desktop Gateway (RD Gateway). Genom att använda det här läget kan du skapa en servergrupp för fjärrskrivbordsgateway i en enda molntjänst.

Ett annat användningsscenario är medieuppladdning. Dataöverföringen sker via UDP, men kontrollplanet uppnås via TCP:

* En klient startar en TCP-session till den belastningsbalanserade offentliga adressen och dirigeras till en specifik DIP. Kanalen lämnas aktiv för att övervaka anslutningens hälsotillstånd.
* En ny UDP-session från samma klientdator startas till samma belastningsbalanserade offentliga slutpunkt. Anslutningen dirigeras till samma DIP-slutpunkt som den tidigare TCP-anslutningen. Medieöverföringen kan utföras med högt dataflöde samtidigt som en kontrollkanal upprätthålls via TCP.

> [!NOTE]
> När en belastningsbalanserad uppsättning ändras genom att ta bort eller lägga till en virtuell dator, beräknas fördelningen av klientbegäranden om. Du kan inte vara beroende av nya anslutningar från befintliga klienter för att hamna på samma server. Dessutom kan användning av käll-IP-tillhörighetsdistributionsläge orsaka en ojämlik distribution av trafik. Klienter som körs bakom proxyservrar kan ses som ett unikt klientprogram.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurera inställningar för käll-IP-tillhörighet

### <a name="azure-portal"></a>Azure Portal

Du kan ändra distributionslägets konfiguration genom att ändra belastningsutjämningsregeln i portalen.

1. Logga in på Azure-portalen och leta reda på resursgruppen som innehåller den belastningsutjämnare som du vill ändra genom att klicka på **Resursgrupper**.
2. I översiktsskärmen för belastningsutjämnare klickar du på **Belastningsutjämningsregler** under **Inställningar**.
3. I skärmen belastningsutjämningsregler klickar du på den belastningsutjämningsregel som du vill ändra distributionsläget.
4. Distributionsläget ändras genom att listrutan **Sessions persistens** ändras.  Följande alternativ är tillgängliga:
    
    * **Ingen (hash-baserad)** - Anger att på varandra följande begäranden från samma klient kan hanteras av vilken virtuell dator som helst.
    * **Klient-IP (källa IP affinity 2-tuppel)** - Anger att efterföljande begäranden från samma klient-IP-adress ska hanteras av samma virtuella dator.
    * **Klient-IP och protokoll (källa IP affinity 3-tuppel)** - Anger att på varandra följande begäranden från samma klient-IP-adress och protokollkombination ska hanteras av samma virtuella dator.

5. Välj distributionsläge och klicka sedan på **Spara**.

### <a name="azure-powershell"></a>Azure PowerShell

För virtuella datorer som distribueras med Resource Manager använder du PowerShell för att ändra belastningsutjämningsfördelningsinställningarna på en befintlig belastningsutjämningsregel. Följande kommando uppdaterar distributionsläget: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

För klassiska virtuella datorer använder du Azure PowerShell för att ändra distributionsinställningarna. Lägg till en Azure-slutpunkt på en virtuell dator och konfigurera belastningsutjämnad distributionsläge:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ange värdet för `LoadBalancerDistribution` elementet för den mängd belastningsutjämning som krävs. Ange sourceIP för belastningsutjämning med två tuppel (källa IP och mål-IP). Ange sourceIPProtocol för belastningsutjämning med tre tuppel (käll-IP, mål-IP och protokolltyp). Ange ingen för standardbeteendet för utjämning av fem tuppelbelastning.

Hämta en distributionsläge för slutpunktsbelastningsutjämning med hjälp av följande inställningar:

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

När `LoadBalancerDistribution` elementet inte finns använder Azure Load Balancer standardalgoritmen för fempelarmar.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurera distributionsläge för belastningsbalanserad slutpunktsuppsättning

När slutpunkter ingår i en belastningsbalanserad slutpunktsuppsättning måste distributionsläget konfigureras på den belastningsbalanserade slutpunktsuppsättningen:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurera distributionsläge för molntjänstslutpunkter

Använd Azure SDK för .NET 2.5 för att uppdatera molntjänsten. Slutpunktsinställningarna för Molntjänster görs i CSDEF-filen. För att uppdatera belastningsutjämningsdistributionsläget för en Cloud Services-distribution krävs en distributionsuppgradering.

Här är ett exempel på .csdef-ändringar för slutpunktsinställningar:

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

## <a name="api-example"></a>EXEMPEL PÅ API

I följande exempel visas hur du konfigurerar om belastningsutjämnad fördelningsläge för en angiven belastningsbalanserad uppsättning i en distribution. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Ändra distributionsläge för distribuerad belastningsbalanserad uppsättning

Använd azure classic deployment model för att ändra en befintlig distributionskonfiguration. Lägg `x-ms-version` till rubriken och ange värdet till version 2014-09-01 eller senare.

#### <a name="request"></a>Förfrågan

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

Som tidigare beskrivits `LoadBalancerDistribution` anger du elementet till sourceIP för två-tuppel affinitet, sourceIPProtocol för tre-tuppel tillhörighet, eller ingen för ingen tillhörighet (fem-tuppel affinitet).

#### <a name="response"></a>Svar

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Intern belastningsutjämnare](load-balancer-internal-overview.md)
* [Komma igång med att konfigurera en internetinriktad belastningsutjämnare](quickstart-create-standard-load-balancer-powershell.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)
