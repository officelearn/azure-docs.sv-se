---
title: Diagnostisera och Felsök problem när du använder Azure Cosmos DB .NET SDK
description: Använd funktioner som loggning på klient sidan och andra verktyg från tredje part för att identifiera, diagnostisera och felsöka Azure Cosmos DB problem när du använder .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137962"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostisera och Felsök problem när du använder Azure Cosmos DB .NET SDK
Den här artikeln beskriver vanliga problem, lösningar, diagnostiska steg och verktyg när du använder [.NET SDK](sql-api-sdk-dotnet.md) med Azure Cosmos DB SQL API-konton.
.NET SDK tillhandahåller logisk representation på klient sidan för att få åtkomst till Azure Cosmos DB SQL API. I den här artikeln beskrivs verktyg och metoder för att hjälpa dig om du stöter på problem.

## <a name="checklist-for-troubleshooting-issues"></a>Check lista för fel söknings problem:
Överväg följande check lista innan du flyttar ditt program till produktion. Genom att använda check listan förhindrar du flera vanliga problem som du kan se. Du kan också snabbt diagnostisera när ett problem uppstår:

*    Använd den senaste [SDK: n](sql-api-sdk-dotnet-standard.md). Preview SDK: er bör inte användas för produktion. Detta förhindrar att kända problem som redan har åtgärd ATS visas.
*    Granska [prestanda tipsen](performance-tips.md)och följ rekommendationerna. Detta bidrar till att förhindra skalning, svars tid och andra prestanda problem.
*    Aktivera SDK-loggning för att hjälpa dig att felsöka ett problem. Att aktivera loggning kan påverka prestanda, så det är bäst att aktivera det bara när du felsöker problem. Du kan aktivera följande loggar:
    *    [Logga mått](monitor-accounts.md) med hjälp av Azure Portal. Portal mått visar Azure Cosmos DB telemetri, vilket är användbart för att avgöra om problemet motsvarar Azure Cosmos DB eller om det kommer från klient sidan.
    *    Logga den [diagnostiska strängen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) i v2 SDK eller [diagnostik](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) i v3 SDK från åtgärds svar för punkt.
    *    Logga [SQL-frågans mått](sql-api-query-metrics.md) från alla fråge svar 
    *    Följ konfigurationen för [SDK-loggning]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Ta en titt på avsnittet [vanliga problem och lösningar](#common-issues-workarounds) i den här artikeln.

Se [avsnittet GitHub-problem](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) som är aktivt övervakat. Kontrol lera om det finns liknande problem med en lösning som redan har arkiverats. Om du inte hittar någon lösning kan du ange ett GitHub-problem. Du kan öppna ett support Tick för brådskande problem.


## <a name="common-issues-workarounds"></a>Vanliga problem och lösningar

### <a name="general-suggestions"></a>Allmänna förslag
* Kör din app i samma Azure-region som ditt Azure Cosmos DB-konto, närhelst det är möjligt. 
* Du kan stöta på problem med anslutning/tillgänglighet på grund av bristande resurser på klient datorn. Vi rekommenderar att du övervakar CPU-belastningen på noder som kör Azure Cosmos DB-klienten och skalar upp/ut om de körs vid hög belastning.

### <a name="check-the-portal-metrics"></a>Kontrol lera Portal måtten
Genom att kontrol lera [Portal måtten](monitor-accounts.md) kan du avgöra om det är problem med klient sidan eller om det är problem med tjänsten. Om måtten t. ex. innehåller en hög frekvens av avgiftsbelagda begär Anden (HTTP-statuskod 429), vilket innebär att begäran får en begränsning, kontrollerar du i avsnittet om [Begäran kostar för stor] . 

### <a name="request-timeouts"></a>Timeout för begär Anden
RequestTimeout sker vanligt vis när du använder direkt/TCP, men kan ske i Gateway-läge. Dessa fel är vanliga kända orsaker och förslag på hur du kan åtgärda problemet.

* PROCESSOR användningen är hög, vilket ger svars tid och/eller begär timeout. Kunden kan skala upp värddatorn för att ge den fler resurser, eller så kan belastningen distribueras på fler datorer.
* Tillgängligheten för socket/port kan vara låg. När du kör i Azure kan klienter som använder .NET SDK nå ut till PAT-port (Azure SNAT). Du kan minska risken för att använda det här problemet genom att använda den senaste versionen 2. x eller 3. x av .NET SDK. Detta är ett exempel på varför det rekommenderas att alltid köra den senaste SDK-versionen.
* Att skapa flera DocumentClient-instanser kan leda till problem med anslutningens innehåll och timeout. Följ [prestanda tipsen](performance-tips.md)och Använd en enda DocumentClient-instans i hela processen.
* Användare kan ibland se förhöjd latens eller begära timeout eftersom deras samlingar har allokerats på ett otillräckligt sätt, begränsningar för backend-begäranden och klienten försöker igen internt. Kontrol lera [Portal måtten](monitor-accounts.md).
* Azure Cosmos DB distribuerar det övergripande allokerade data flödet jämnt över fysiska partitioner. Kontrol lera Portal måtten för att se om arbets belastningen påträffar en [nyckel](partition-data.md)för en snabb partition. Detta leder till att det sammanlagda förbrukade data flödet (RU/s) visas under den etablerade ru: er, men en enda partition som används för förbrukat data flöde (RU/s) överskrider det etablerade data flödet. 
* Dessutom lägger 2,0-SDK till kanal-semantik till direkta/TCP-anslutningar. En TCP-anslutning används för flera begär Anden samtidigt. Detta kan leda till två problem i vissa fall:
    * En hög grad av samtidighet kan leda till konkurrens på kanalen.
    * Stora begär Anden eller svar kan leda till att det finns en rad olika block i kanal-och exacerbate-konkurrens, även med en relativt låg grad av samtidighet.
    * Om ärendet är i någon av dessa två kategorier (eller om hög CPU-användning misstänks vara misstänkt), så är det möjligt att minska problemen:
        * Försök att skala programmet upp/ut.
        * Dessutom kan SDK-loggar samlas in via [spårnings lyssnare](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) för att få mer information.

### <a name="high-network-latency"></a>Hög nätverks fördröjning
Hög nätverks fördröjning kan identifieras med hjälp av den [diagnostiska strängen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) i v2 SDK eller [diagnostik](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) i v3 SDK.

Om det inte finns några [tids gränser](#request-timeouts) och diagnostiken visar enskilda förfrågningar där den höga svars tiden är tydlig på skillnaden mellan `ResponseTime` och `RequestStartTime`, t. ex. (> 300 millisekunder i det här exemplet):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Den här fördröjningen kan ha flera orsaker:

* Ditt program körs inte i samma region som ditt Azure Cosmos DB-konto.
* Din [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) -eller [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) -konfiguration är felaktig och försöker ansluta till en annan region där programmet körs för tillfället.
* Det kan finnas en Flask hals i nätverks gränssnittet på grund av hög trafik. Om programmet körs på Azure Virtual Machines finns det möjliga lösningar:
    * Överväg att använda en [virtuell dator med accelererat nätverk aktiverat](../virtual-network/create-vm-accelerated-networking-powershell.md).
    * Aktivera [accelererat nätverk på en befintlig virtuell dator](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Överväg att använda en [högre slut virtuell dator](../virtual-machines/windows/sizes.md).

### <a name="snat"></a>Port överbelastning för Azure SNAT (PAT)

Om din app distribueras på [azure Virtual Machines utan en offentlig IP-adress](../load-balancer/load-balancer-outbound-connections.md#defaultsnat), upprättar [Azure SNAT-portar](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) som standard anslutningar till en slut punkt utanför den virtuella datorn. Antalet anslutningar som tillåts från den virtuella datorn till Azure Cosmos DB slut punkten begränsas av [Azure SNAT-konfigurationen](../load-balancer/load-balancer-outbound-connections.md#preallocatedports). Den här situationen kan leda till anslutningens begränsning, anslutningens slut för ande, eller de [tids gränser](#request-timeouts)som anges ovan.

 Azure SNAT-portar används endast när den virtuella datorn har en privat IP-adress som ansluter till en offentlig IP-adress. Det finns två lösningar för att undvika begränsning av Azure SNAT (förutsatt att du redan använder en enda klient instans i hela programmet):

* Lägg till din Azure Cosmos DB tjänst slut punkt i under nätet för ditt Azure Virtual Machines virtuella nätverk. Mer information finns i [Azure Virtual Network Service-slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md). 

    När tjänstens slut punkt Aktiver ATS skickas inte längre begär Anden från en offentlig IP-adress till Azure Cosmos DB. I stället skickas det virtuella nätverket och under nätets identitet. Den här ändringen kan leda till att brand väggen släpper om bara offentliga IP-adresser är tillåtna. Om du använder en brand vägg, när du aktiverar tjänstens slut punkt, lägger du till ett undernät i brand väggen med hjälp av [Virtual Network ACL: er](../virtual-network/virtual-networks-acl.md).
* Tilldela en [offentlig IP-adress till din virtuella Azure-dator](../load-balancer/load-balancer-outbound-connections.md#assignilpip).

### <a name="http-proxy"></a>HTTP-proxy
Om du använder en HTTP-proxy kontrollerar du att den har stöd för antalet anslutningar som kon figurer ATS i SDK-`ConnectionPolicy`.
Annars är det problem med ansikts anslutning.

### <a name="request-rate-too-large"></a>Begäran kostar för stor
"Begär ande frekvens för stor" eller felkod 429 anger att dina förfrågningar begränsas, eftersom det förbrukade data flödet (RU/s) har överskridit det [etablerade data flödet](set-throughput.md). SDK: n kommer automatiskt att försöka utföra begär anden igen baserat på den angivna [återförsöks principen](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Om du får det här felet ofta bör du överväga att öka data flödet för samlingen. Kontrol lera [portalens mått](use-metrics.md) för att se om du får 429 fel. Granska din [partitionsnyckel](partitioning-overview.md#choose-partitionkey) för att säkerställa att den resulterar i en jämn fördelning av lagrings utrymme och begär ande volym. 

### <a name="slow-query-performance"></a>Långsam frågans prestanda
[Frågans mått](sql-api-query-metrics.md) hjälper till att avgöra var frågan kostar det mesta av tiden. Från frågans mått kan du se hur mycket av det som ägnas åt Server delen jämfört med klienten.
* Om backend-frågan returnerar snabbt och ägnar en lång tid på klienten kontrollerar du belastningen på datorn. Det beror förmodligen på att det inte finns tillräckligt med resurser och att SDK väntar på att resurser ska kunna hantera svaret.
* Om Server dels frågan är långsam försöker du [optimera frågan](optimize-cost-queries.md) och titta på den aktuella [indexerings principen](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Begäran kostar för stor]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


