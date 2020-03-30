---
title: Diagnostisera och felsöka problem med Azure Cosmos DB .NET SDK
description: Använd funktioner som loggning på klientsidan och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Cosmos DB-problem när du använder .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137962"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostisera och felsöka problem med Azure Cosmos DB .NET SDK
Den här artikeln innehåller vanliga problem, lösningar, diagnostiksteg och verktyg när du använder [.NET SDK](sql-api-sdk-dotnet.md) med Azure Cosmos DB SQL API-konton.
.NET SDK ger logisk representation på klientsidan för åtkomst till Azure Cosmos DB SQL API. I den här artikeln beskrivs verktyg och metoder för att hjälpa dig om du stöter på problem.

## <a name="checklist-for-troubleshooting-issues"></a>Checklista för felsökningsproblem:
Överväg följande checklista innan du flyttar programmet till produktion. Om du använder checklistan förhindras flera vanliga problem som kan du se. Du kan också snabbt diagnostisera när ett problem uppstår:

*    Använd den senaste [SDK](sql-api-sdk-dotnet-standard.md). Förhandsgranska SDK:er bör inte användas för produktion. Detta förhindrar att du slår till mot kända problem som redan är åtgärdade.
*    Granska [resultattipsen](performance-tips.md)och följ de föreslagna metoderna. Detta förhindrar skalning, svarstid och andra prestandaproblem.
*    Aktivera SDK-loggningen så att du kan felsöka ett problem. Om du aktiverar loggningen kan prestanda påverkas så det är bäst att bara aktivera den vid felsökning av problem. Du kan aktivera följande loggar:
    *    [Logga mått](monitor-accounts.md) med hjälp av Azure-portalen. Portalmått visar Azure Cosmos DB-telemetrin, vilket är användbart för att avgöra om problemet motsvarar Azure Cosmos DB eller om det är från klientsidan.
    *    Logga [diagnostiksträngen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) i V2 SDK eller [diagnostik](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) i V3 SDK från punktåtgärdssvaren.
    *    Logga [SQL Query-måtten](sql-api-query-metrics.md) från alla frågesvar 
    *    Följ inställningarna för [SDK-loggning]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Ta en titt på avsnittet [Vanliga problem och lösningar](#common-issues-workarounds) i den här artikeln.

Kontrollera [avsnittet GitHub-problem](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) som övervakas aktivt. Kontrollera om något liknande problem med en lösning redan har arkiverats. Om du inte hittade någon lösning arkiverar du ett GitHub-problem. Du kan öppna en supportbock för brådskande problem.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Vanliga fel och lösningar

### <a name="general-suggestions"></a>Allmänna förslag
* Kör din app i samma Azure-region som ditt Azure Cosmos DB-konto, när det är möjligt. 
* Du kan stöta på anslutnings-/tillgänglighetsproblem på grund av brist på resurser på klientdatorn. Vi rekommenderar att du övervakar din CPU-användning på noder som kör Azure Cosmos DB-klienten och skala upp/ut om de körs med hög belastning.

### <a name="check-the-portal-metrics"></a>Kontrollera portalmåtten
Genom att kontrollera [portalmåtten](monitor-accounts.md) kan du avgöra om det är ett problem på klientsidan eller om det finns ett problem med tjänsten. Om måtten till exempel innehåller en hög hastighet av hastighetsbegränsade begäranden(HTTP-statuskod 429), vilket innebär att begäran begränsas, kontrollera sedan avsnittet [Begär hastighet för stort.] 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Begär timeout
RequestTimeout inträffar vanligtvis när direct/tcp används, men kan ske i gateway-läge. Dessa fel är de vanligaste kända orsakerna och förslag på hur du åtgärdar problemet.

* CPU-användningen är hög, vilket kommer att orsaka svarstid och/eller begärandetidskopiering. Kunden kan skala upp värddatorn för att ge den mer resurser, eller så kan belastningen fördelas över fler datorer.
* Tillgängligheten för socket/port kan vara låg. När du kör i Azure kan klienter som använder .NET SDK träffa Utmattning av Azure SNAT (PAT). Om du vill minska risken för att problemet ska gå till använder du den senaste versionen 2.x eller 3.x av .NET SDK. Detta är ett exempel på varför det rekommenderas att alltid köra den senaste SDK-versionen.
* Om du skapar flera DocumentClient-instanser kan det leda till problem med anslutningskonkurrens och timeout. Följ [prestandatipsen](performance-tips.md)och använd en enda DocumentClient-instans över en hel process.
* Användare ser ibland förhöjda svarstider eller tidsutgångar för begäran eftersom deras samlingar inte har etablerats tillräckligt, begäranden om backend-begränsning och klienten försöker igen internt. Kontrollera [portalmåtten](monitor-accounts.md).
* Azure Cosmos DB distribuerar det övergripande etablerade dataflödet jämnt över fysiska partitioner. Kontrollera portalmått för att se om arbetsbelastningen stöter på en frekvent [partitionsnyckel](partition-data.md). Detta medför att det sammanlagda förbrukade dataflödet (RU/s) verkar vara under de etablerade ru:erna, men ett enda dataflöde för förbrukad partition (RU/s) kommer att överstiga det etablerade dataflödet. 
* Dessutom lägger 2.0 SDK till kanalsenmantik till direkt/TCP-anslutningar. En TCP-anslutning används för flera begäranden samtidigt. Detta kan leda till två frågor under särskilda fall:
    * En hög grad av samtidighet kan leda till konkurrens på kanalen.
    * Stora förfrågningar eller svar kan leda till head-of-line blockering på kanalen och förvärra stridigheter, även med en relativt låg grad av samtidighet.
    * Om fallet faller i någon av dessa två kategorier (eller om hög CPU-användning misstänks), dessa är möjliga mildrande åtgärder:
        * Försök att skala upp/ut programmet.
        * Dessutom kan SDK-loggar fångas in via [Trace Listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) för att få mer information.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Hög nätverksfördröjning
Hög nätverksfördröjning kan identifieras med hjälp av [diagnostiksträngen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) i V2 SDK eller [diagnostik](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) i V3 SDK.

Om det inte finns några [timeout och](#request-timeouts) diagnostiken visar enstaka begäranden `ResponseTime` `RequestStartTime`där den höga latensen är tydlig på skillnaden mellan och , så där (>300 millisekunder i det här exemplet):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Den här svarstiden kan ha flera orsaker:

* Ditt program körs inte i samma region som ditt Azure Cosmos DB-konto.
* [Konfigurationen PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) eller [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) är felaktig och försöker ansluta till en annan region dit programmet körs på.
* Det kan finnas en flaskhals i nätverksgränssnittet på grund av hög trafik. Om programmet körs på virtuella Azure-datorer finns det möjliga lösningar:
    * Överväg att använda en [virtuell dator med accelererat nätverk aktiverat](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Aktivera [accelererat nätverk på en befintlig virtuell dator](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Överväg att använda en [virtuell dator i den övre delen](../virtual-machines/windows/sizes.md).

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Utmattning av Azure SNAT(PAT) port

Om din app distribueras på [Virtuella Azure-datorer utan en offentlig IP-adress](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) [upprättas](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) anslutningar till valfri slutpunkt utanför den virtuella datorn som standard används. Antalet anslutningar som tillåts från den virtuella datorn till Azure Cosmos DB-slutpunkten begränsas av [Azure SNAT-konfigurationen](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Den här situationen kan leda till anslutningsbegränsning, anslutningsstängning eller ovannämnda [tidsutgångar för begäran](#request-timeouts).

 Azure SNAT-portar används endast när den virtuella datorn har en privat IP-adress ansluter till en offentlig IP-adress. Det finns två lösningar för att undvika Azure SNAT-begränsning (förutsatt att du redan använder en enskild klientinstans i hela programmet):

* Lägg till din Azure Cosmos DB-tjänstslutpunkt i undernätet i ditt virtuella Azure-datorer. Mer information finns i [Slutpunkter för Azure Virtual Network-tjänsten](../virtual-network/virtual-network-service-endpoints-overview.md). 

    När tjänstslutpunkten är aktiverad skickas begärandena inte längre från en offentlig IP till Azure Cosmos DB. I stället skickas det virtuella nätverket och undernätsidentiteten. Den här ändringen kan resultera i att brandväggen sjunker om endast offentliga IPs tillåts. Om du använder en brandvägg lägger du till ett undernät i brandväggen när du aktiverar tjänstens slutpunkt med hjälp av [ACL:er för virtuellt nätverk](../virtual-network/virtual-networks-acl.md).
* Tilldela en [offentlig IP till din Virtuella Azure-dator](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>HTTP-proxy
Om du använder en HTTP-proxy kontrollerar du att den kan stödja `ConnectionPolicy`antalet anslutningar som konfigurerats i SDK .
Annars står du inför anslutningsproblem.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Begärsfrekvensen är för stor
"Begäranden är för stor" eller felkod 429 anger att dina begäranden begränsas, eftersom det förbrukade dataflödet (RU/s) har överskridit det [etablerade dataflödet](set-throughput.md). SDK försöker automatiskt om begäranden baserat på den angivna [återförsöksprincipen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Om du får det här felet ofta kan du överväga att öka dataflödet i samlingen. Kontrollera [portalens mått](use-metrics.md) för att se om du får 429 fel. Granska [partitionsnyckeln](partitioning-overview.md#choose-partitionkey) för att säkerställa att den resulterar i en jämn distribution av lagring och begäran volym. 

### <a name="slow-query-performance"></a>Långsam frågeprestanda
[Frågemåtten](sql-api-query-metrics.md) hjälper dig att avgöra var frågan spenderar större delen av tiden. Från frågemåtten kan du se hur mycket av det som spenderas på backend kontra klienten.
* Om backend-frågan returneras snabbt och tillbringar mycket tid på klienten kontrollera belastningen på datorn. Det är troligt att det inte finns tillräckligt med resurser och SDK väntar på att resurser ska vara tillgängliga för att hantera svaret.
* Om backend-frågan är långsam kan du försöka [optimera frågan](optimize-cost-queries.md) och titta på den aktuella [indexeringsprincipen](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Begärsfrekvensen är för stor]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


