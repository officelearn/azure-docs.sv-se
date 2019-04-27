---
title: Diagnostisera och felsöka problem när du använder Azure Cosmos DB .NET SDK
description: Använda funktioner som loggning på klientsidan och andra tredjepartsverktyg för att identifiera, diagnostisera och felsöka problem med Azure Cosmos DB när du använder .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 01/19/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7f969ab6059140ec32c9c5bf5045c546602a3c15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404731"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Diagnostisera och felsöka problem när du använder Azure Cosmos DB .NET SDK
Den här artikeln beskriver vanliga problem, lösningar, diagnos och verktyg när du använder den [.NET SDK](sql-api-sdk-dotnet.md) med Azure Cosmos DB SQL API-konton.
.NET SDK innehåller klientsidan logisk representation för att komma åt Azure Cosmos DB SQL API. Den här artikeln beskriver verktyg och metoder för att hjälpa dig om du stöter på problem.

## <a name="checklist-for-troubleshooting-issues"></a>Checklista för att felsöka problem:
Överväg följande checklista innan du flyttar ditt program till produktion. Med hjälp av checklistan hindrar flera vanliga problem som kan uppstå. Du kan också snabbt diagnostisera när ett problem inträffar:

*   Senaste [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Förhandsversion av SDK: er ska inte användas för produktion. Det förhindrar att träffa kända problem som redan har åtgärdats.
*   Granska den [prestandatips](performance-tips.md), och följ de föreslagna metoderna. På så sätt förhindra skalning, svarstid och andra problem med prestanda.
*   Aktivera loggning för SDK för att felsöka ett problem. Aktivera loggning kan påverka prestanda så att det är bäst att aktivera det endast när du felsöker problem. Du kan aktivera följande loggar:
    *   [Logga mått](monitor-accounts.md) med hjälp av Azure portal. Portalen mätningar visar ett Azure Cosmos DB-telemetri, vilket är användbart för att fastställa om problemet motsvarar Azure Cosmos DB eller om det vore från klientsidan.
    *   Loggen i [diagnostik sträng](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) från hanteringsplatsens åtgärden svar.
    *   Loggen i [SQL-fråga mått](sql-api-query-metrics.md) från alla frågesvar 
    *   Följer inställningarna för [SDK-loggning]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Ta en titt på de [vanliga problem och lösningar](#common-issues-workarounds) i den här artikeln.

Kontrollera den [GitHub utfärdar avsnittet](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) som övervakas aktivt. Kontrollera om alla liknande problem med en lösning redan har arkiverats. Om du inte hittar en lösning, sedan filens ett GitHub-ärende. Du kan öppna en support skalstreck för brådskande problem.


## <a name="common-issues-workarounds"></a>Vanliga problem och lösningar

### <a name="general-suggestions"></a>Allmänna förslag
* Kör din app i samma Azure-region som Azure Cosmos DB-kontot, när det är möjligt. 
* Du kanske stöter på problem med anslutning/tillgänglighet på grund av bristande resurser på klientdatorn. Vi rekommenderar CPU-belastning på noder som kör Azure Cosmos DB-klienten och skala upp/ut om de körs vid hög belastning.

### <a name="check-the-portal-metrics"></a>Kontrollera måtten i portalen
Kontrollera den [portal mått](monitor-accounts.md) hjälper till att fastställa om det är ett fel på klientsidan eller om det finns ett problem med tjänsten. Till exempel om mått som innehåller en hög andel rate-limited begäranden (HTTP-statuskod 429) vilket innebär att begäran komma begränsas Kontrollera den [frågehastigheten är för stor] avsnittet. 

### <a name="request-timeouts"></a>Timeout för begäranden
RequestTimeout inträffar vanligtvis när du använder Direct/TCP, men kan förekomma i Gateway-läge. Det här är vanliga kända orsaker och förslag om hur du löser problemet.

* CPU-användning är hög, vilket kan orsaka svarstid och/eller timeout. Kunden kan skala upp värddatorn att ge det fler resurser eller belastningen kan distribueras på flera datorer.
* Socket / porttillgänglighet kanske saknas. När du använder .NET SDK: er som publicerades före version 2.0 kan klienter som körs i Azure kan nå den [Portöverbelastning Azure SNAT (PAT)]. Den här ett exempel på varför rekommenderar vi att du alltid köra den senaste versionen av SDK.
* Skapa flera instanser av DocumentClient kan leda till anslutningen konkurrens och timeout-problem. Följ den [prestandatips](performance-tips.md), och använda en enda instans av DocumentClient över hela processen.
* Användarna ser ibland upphöjd tidsgränser för svarstid eller begäran eftersom deras samlingar har etablerats otillräckligt, backend-begränsar begärandena och klienten försöker internt utan att visa detta till anroparen. Kontrollera den [portal mått](monitor-accounts.md).
* Azure Cosmos DB distribuerar det övergripande dataflöden jämnt över fysiska partitioner. Kontrollera portalen mått för att se om arbetsbelastningen är påträffar en varm [partitionsnyckel](partition-data.md). Detta innebär att den sammanställda förbrukade dataflöden (RU/s) verkar vara under de etablerade ru: er, men en enskild partition förbrukat dataflöde (RU/s) kommer att överskrida det etablerade dataflödet. 
* Dessutom 2.0 SDK lägger till channel semantik direct/TCP-anslutningar. En TCP-anslutning används för flera begäranden samtidigt. Detta kan leda till två problem under särskilda fall:
    * En hög grad av samtidighet kan leda till konkurrensen på kanalen.
    * Stora begäranden kan leda till head of line blockerar på kanalen och förvärra konkurrens, även med en relativt låg grad av samtidighet.
    * Om fallet hamnar i någon av dessa två kategorier (eller om du misstänker att hög CPU-användning), de är möjliga åtgärder:
        * Prova att skala programmet in/ut.
        * Dessutom SDK-loggar samlas in via [Trace Listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) att få mer information.

### <a name="connection-throttling"></a>Anslutningsbegränsning
Anslutningsbegränsning kan inträffa på grund av en anslutningsgräns på en värddator. Före 2.0 klienter som körs i Azure kan nå den [Portöverbelastning Azure SNAT (PAT)].

### <a name="snat"></a>Portöverbelastning Azure SNAT (PAT)

Om din app distribueras på Azure virtuella datorer utan en offentlig IP-adress som standard [Azure SNAT portar](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) upprätta anslutningar till valfri slutpunkt utanför den virtuella datorn. Antalet tillåtna anslutningar från den virtuella datorn till Azure Cosmos DB-slutpunkten är begränsat av den [Azure SNAT configuration](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT portar används endast när den virtuella datorn har en privat IP-adress och en process från den virtuella datorn försöker ansluta till en offentlig IP-adress. Det finns två sätt att undvika Azure SNAT begränsning:

* Lägga till ditt Azure Cosmos DB-tjänstslutpunkt i undernätet för det virtuella nätverket i Azure Virtual Machines. Mer information finns i [Azure Virtual Network-tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    När tjänsteslutpunkt är aktiverat skickas inte längre begäranden från en offentlig IP-adress till Azure Cosmos DB. Istället skickas det virtuella nätverket och undernätet identitet. Den här ändringen kan resultera i brandväggen släpper om endast offentliga IP-adresser tillåts. Om du använder en brandvägg, när du aktiverar tjänstslutpunkten, lägga till ett undernät i brandväggen med hjälp av [ACL: er med virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Tilldela en offentlig IP-adress till den virtuella Azure-datorn.

### <a name="http-proxy"></a>HTTP-proxy
Om du använder en HTTP-proxy, kontrollera att den har stöd för antalet anslutningar som konfigurerats i SDK `ConnectionPolicy`.
Annars kan du står inför problem med anslutningen.

### Frågehastigheten är för stor<a name="request-rate-too-large"></a>
'Förfrågningsfrekvens är för stor, eller felkod 429 anger att dina begäranden har begränsats, eftersom den förbrukade dataflöden (RU/s) har överskridit det etablerade dataflödet. SDK försöker automatiskt förfrågningar baserat på den angivna [återförsöksprincip](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Om du får det här felet ofta kan du överväga att öka dataflödet i samlingen. Kontrollera den [portalens mått](use-metrics.md) att se om du får 429-fel. Granska dina [partitionsnyckel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) att se till att det resulterar i en jämn fördelning av lagring och begäran volym. 

### <a name="slow-query-performance"></a>Prestanda för långsamma frågor
Den [frågar om mått](sql-api-query-metrics.md) hjälper till att fastställa där frågan är utgifter i de flesta fall. På frågan mått, kan du se hur många av dem som ägnats åt jämfört med backend-klienten.
* Kontrollera belastningen på datorn om backend-frågan returnerar snabbt och är en stor tid på klienten. Det är troligt att det finns inte tillräckligt med resurser och SDK: N väntar på att resurser ska kunna hantera svaret.
* Om backend-frågan är långsam försök [optimera frågan](optimize-cost-queries.md) och titta på aktuellt [indexeringsprincip](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Frågehastigheten är för stor]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Portöverbelastning Azure SNAT (PAT)]: #snat
[Production check list]: #production-check-list


