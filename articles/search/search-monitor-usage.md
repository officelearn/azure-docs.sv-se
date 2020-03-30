---
title: Övervaka operationer och aktivitet
titleSuffix: Azure Cognitive Search
description: Aktivera loggning, hämta frågeaktivitetsmått, resursanvändning och andra systemdata från en Azure Cognitive Search-tjänst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462334"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Övervaka åtgärder och aktivitet i Azure Cognitive Search

Den här artikeln introducerar övervakning på tjänstnivå (resurs)nivå, på arbetsbelastningsnivå (frågor och indexering) och föreslår ett ramverk för övervakning av användaråtkomst.

Över hela spektrumet använder du en kombination av inbyggd infrastruktur och grundläggande tjänster som Azure Monitor, samt tjänst-API:er som returnerar statistik, antal och status. Att förstå olika funktioner kan hjälpa dig att skapa en feedbackloop så att du kan lösa problem när de uppstår.

## <a name="use-azure-monitor"></a>Använda Azure Monitor

Många tjänster, inklusive Azure Cognitive Search, utnyttjar [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) för aviseringar, mått och loggning av diagnostikdata. För Azure Cognitive Search används den inbyggda övervakningsinfrastrukturen främst för övervakning på resursnivå (tjänsthälsa) och [frågeövervakning](search-monitor-queries.md).

Följande skärmbild hjälper dig att hitta Azure Monitor-funktioner i portalen.

+ **Övervakningsfliken,** som finns på huvudöversiktssidan, visar viktiga mått med ett ögonkast.
+ **Aktivitetslogg**, strax under Översikt, rapporter om åtgärder på resursnivå: tjänsthälsa och API-nyckelbegäranmeddelanden.
+ **Övervakning**, längre ner i listan, ger konfigurerbara aviseringar, mått och diagnostikloggar. Skapa dessa när du behöver dem. När data har samlats in och lagrats kan du fråga eller visualisera informationen efter insikter.

![Azure Monitor-integrering i en söktjänst](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor-integrering i en söktjänst")

### <a name="precision-of-reported-numbers"></a>Precision av rapporterade tal

Portalsidor uppdateras med några minuters mellanrum. Som sådan är nummer som rapporteras i portalen ungefärliga, avsedda att ge dig en allmän uppfattning om hur väl ditt system är serviceförfrågningar. Faktiska mått, till exempel frågor per sekund (QPS) kan vara högre eller lägre än det antal som visas på sidan.

## <a name="activity-logs-and-service-health"></a>Aktivitetsloggar och tjänsthälsa

[**Aktivitetsloggen**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) samlar in information från Azure Resource Manager och rapporter om ändringar i tjänstens hälsotillstånd. Du kan övervaka aktivitetsloggen för kritiska, fel- och varningsvillkor som är relaterade till tjänstens hälsotillstånd.

För tjänster i tjänst – till exempel frågor, indexering eller att skapa objekt – visas allmänna informationsmeddelanden som *Hämta administratörsnyckel* och *Hämta frågenycklar* för varje begäran, men inte själva åtgärden. Om du vill ha information om det här kornet måste du konfigurera diagnostikloggning.

Du kan komma åt **aktivitetsloggen** från det vänstra navigeringsfönstret, eller från Meddelanden i kommandofältet i det övre fönstret, eller från sidan **Diagnostisera och lösa problem.**

## <a name="monitor-storage"></a>Övervaka lagring

Flikar sidor inbyggda i rapporten Översiktssida om resursanvändning. Den här informationen blir tillgänglig så fort du börjar använda tjänsten, utan att någon konfiguration krävs, och sidan uppdateras med några minuters mellanrum. 

Om du slutför beslut om [vilken nivå som ska användas för produktionsarbetsbelastningar](search-sku-tier.md)eller om du vill justera antalet aktiva [repliker och partitioner](search-capacity-planning.md)kan dessa mått hjälpa dig med dessa beslut genom att visa hur snabbt resurser förbrukas och hur väl den aktuella konfigurationen hanterar den befintliga belastningen.

Aviseringar relaterade till lagring är för närvarande inte tillgängliga. lagringsförbrukningen aggregeras eller loggas inte in i **AzureMetrics-tabellen** i Azure Monitor. Du måste [skapa en anpassad lösning](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating) som avger resursrelaterade meddelanden, där koden söker efter lagringsstorlek och hanterar svaret. Mer information om lagringsmått finns i [Hämta servicestatistik](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response).

För visuell övervakning i portalen visar fliken **Användning** resurstillgänglighet i förhållande till aktuella [gränser](search-limits-quotas-capacity.md) som anges av tjänstnivån. 

Följande illustration är för den kostnadsfria tjänsten, som är begränsad till 3 objekt av varje typ och 50 MB lagringsutrymme. En Basic- eller Standard-tjänst har högre gränser, och om du ökar antalet partitioner ökar maximal lagring proportionellt.

![Användningsstatus i förhållande till nivågränser](./media/search-monitor-usage/usage-tab.png
 "Användningsstatus i förhållande till nivågränser")

## <a name="monitor-workloads"></a>Övervaka arbetsbelastningar

Loggade händelser inkluderar de som är relaterade till indexering och frågor. Tabellen **AzureDiagnostics** i Log Analytics samlar in driftdata relaterade till frågor och indexering.

De flesta av de loggade data är för skrivskyddade åtgärder. För andra åtgärder för att skapa-uppdatera och ta bort som inte tas i loggen kan du fråga söktjänsten efter systeminformation.

| OperationName | Beskrivning |
|---------------|-------------|
| ServiceStats (ServiceStats) | Den här åtgärden är ett rutinanrop till [Hämta servicestatistik](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), antingen anropas direkt eller implicit för att fylla i en portalöversiktssida när den läses in eller uppdateras. |
| Fråga.Sök |  Frågebegäranden mot ett index Se [Övervakarefrågor](search-monitor-queries.md) för information om loggade frågor.|
| Indexering.Index  | Den här åtgärden är ett anrop till [Lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| Index. Prototyp | Det här är ett index som skapats av guiden Importera data. |
| Indexers.Skapa | Skapa en indexerare explicit eller implicit via guiden Importera data. |
| Indexers.Get | Returnerar namnet på en indexerare när indexeraren körs. |
| Indexerare.Status | Returnerar status för en indexerare när indexeraren körs. |
| DataSources.Hämta | Returnerar namnet på datakällan när en indexerare körs.|
| Indexes.Get | Returnerar namnet på ett index när en indexerare körs. |

### <a name="kusto-queries-about-workloads"></a>Kusto frågar om arbetsbelastningar

Om du har aktiverat loggning kan du fråga **AzureDiagnostics** för en lista över åtgärder som kördes på din tjänst och när. Du kan också korrelera aktivitet för att undersöka förändringar i prestanda.

#### <a name="example-list-operations"></a>Exempel: Liståtgärder 

Returnera en lista över åtgärder och en räkning på var och en.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exempel: Korrelera operationer

Korrelera frågebegäran med indexeringsåtgärder och återge datapunkterna över ett tidsschema för att se åtgärder sammanfalla.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Använda sök-API:er

Både AZURE Cognitive Search REST API och .NET SDK ger programmatisk åtkomst till tjänstmått, index- och indexinformation och dokumentantal.

+ [FÅ Servicestatistik](/rest/api/searchservice/get-service-statistics)
+ [HÄMTA indexstatistik](/rest/api/searchservice/get-index-statistics)
+ [HÄMTA antal dokument](/rest/api/searchservice/count-documents)
+ [HÄMTA indexerarstatus](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Övervaka användaråtkomst

Eftersom sökindex är en komponent i ett större klientprogram finns det ingen inbyggd metod för att kontrollera eller övervaka åtkomsten per användare till ett index. Begäranden antas komma från ett klientprogram, för antingen administratörs- eller frågebegäranden. Admin läs-och skrivåtgärder inkluderar att skapa, uppdatera, ta bort objekt över hela tjänsten. Skrivskyddade åtgärder är frågor mot dokumentsamlingen, begränsade till ett enda index. 

Det du ser i aktivitetsloggarna är därför referenser till samtal med hjälp av administratörsnycklar eller frågenycklar. Lämplig nyckel ingår i begäranden som kommer från klientkoden. Tjänsten är inte utrustad för att hantera identitetstoken eller personifiering.

När affärskrav finns för auktorisering per användare är rekommendationen integrering med Azure Active Directory. Du kan använda $filter- och användaridentiteter för att [trimma sökresultat](search-security-trimming-for-azure-search-with-aad.md) för dokument som en användare inte bör se. 

Det går inte att logga den här informationen separat från frågesträngen som innehåller parametern $filter. Se [Övervaka frågor](search-monitor-queries.md) för information om rapportering av frågesträngar.

## <a name="next-steps"></a>Nästa steg

Flytande med Azure Monitor är viktigt för övervakning av alla Azure-tjänster, inklusive resurser som Azure Cognitive Search. Om du inte är bekant med Azure Monitor tar du dig tid att granska artiklar som är relaterade till resurser. Förutom handledning, är följande artikel ett bra ställe att börja.

> [!div class="nextstepaction"]
> [Övervaka Azure-resurser med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
