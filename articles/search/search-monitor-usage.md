---
title: Övervaka åtgärder och aktivitet
titleSuffix: Azure Cognitive Search
description: Aktivera loggning, Hämta aktivitets statistik, resursanvändning och andra system data från en Azure Kognitiv sökning-tjänst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: c4a787362089dabf9c4eda9681358e7a70d8e78a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210557"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Övervaka åtgärder och aktiviteter i Azure Kognitiv sökning

Den här artikeln introducerar övervakning på tjänste nivå (resurs) på arbets belastnings nivå (frågor och indexering) och föreslår ett ramverk för övervakning av användar åtkomst.

I spektrumet använder du en kombination av inbyggda infrastruktur-och grund tjänster som Azure Monitor, samt tjänst-API: er som returnerar statistik, antal och status. Att förstå möjligheterna med funktioner hjälper dig att konfigurera eller skapa ett effektivt kommunikations system för proaktiva svar på problem när de visas.

## <a name="use-azure-monitor"></a>Använda Azure Monitor

Många tjänster, inklusive Azure Kognitiv sökning, utnyttjar [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) för aviseringar, mått och loggning av diagnostikdata. För Azure Kognitiv sökning används den inbyggda övervaknings infrastrukturen främst för övervakning på resurs nivå (tjänst hälsa) och [övervakning av frågor](search-monitor-queries.md).

På följande skärm bild kan du hitta Azure Monitor funktioner i portalen.

+ Fliken **övervakning** , som finns på huvud sidan för översikt, visar viktiga mått på ett ögonblick.
+ **Aktivitets logg**, precis nedan – översikt, rapporter om åtgärder på resurs nivå: meddelanden om tjänstens hälso tillstånd och API-nyckel.
+ **Övervakning**, mer ned i listan, innehåller konfigurerbara aviseringar, Mät värden och diagnostiska loggar. Skapa dem när du behöver dem. När data har samlats in och lagrats kan du fråga eller visualisera informationen för insikter.

![Azure Monitor integrering i en Sök tjänst](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integrering i en Sök tjänst")

### <a name="precision-of-reported-numbers"></a>Precision för rapporterade siffror

Portal sidor uppdateras en gång i minuten. Därför är tal som rapporteras i portalen ungefärliga, för att ge dig en allmän uppfattning om hur väl systemet hanterar begär Anden. Faktiska mått, till exempel frågor per sekund (frågor per sekund) kan vara högre eller lägre än det tal som visas på sidan.

## <a name="activity-logs-and-service-health"></a>Aktivitets loggar och service hälsa

[**Aktivitets loggen**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) samlar in information från Azure Resource Manager och rapporter om ändringar av tjänstens hälsa. Du kan övervaka aktivitets loggen för kritiska, fel och varnings villkor relaterade till tjänstens hälsa.

För tjänst uppgifter – till exempel frågor, indexering eller skapande av objekt – visas allmänna informations meddelanden som *Hämta administratörs nyckel* och *Hämta frågeinställningar* för varje begäran, men inte själva åtgärden. För information om denna kornig het måste du konfigurera diagnostisk loggning.

Du kan komma åt **aktivitets loggen** i det vänstra navigerings fönstret eller från meddelanden i det övre fönstrets kommando fält eller på sidan **diagnosticera och lösa problem** .

## <a name="monitor-storage"></a>Övervaka lagring

Tabbade sidor som är inbyggda i översikts sidan rapporterar om resursanvändning. Den här informationen blir tillgänglig så snart du börjar använda tjänsten, utan konfiguration krävs, och sidan uppdateras med några minuters mellanrum. 

Om du slutför beslut om [vilken nivå som ska användas för produktions arbets belastningar](search-sku-tier.md), eller om du vill [Justera antalet aktiva repliker och partitioner](search-capacity-planning.md), kan dessa mått hjälpa dig med dessa beslut genom att visa hur snabbt resurser förbrukas och hur väl den aktuella konfigurationen hanterar den befintliga belastningen.

Aviseringar relaterade till lagring är inte tillgängliga för närvarande. lagrings förbrukningen är inte aggregerad eller inloggad i **AzureMetrics**. Du måste skapa en anpassad lösning för att få resurs-relaterade aviseringar.

I portalen visar fliken **användning** resurs tillgänglighet i förhållande till de aktuella [begränsningar](search-limits-quotas-capacity.md) som angetts av tjänst nivån. 

Följande bild är avsedd för den kostnads fria tjänsten som är ett tak på 3 objekt av varje typ och 50 MB lagrings utrymme. En Basic-eller standard-tjänst har högre gränser, och om du ökar antalet partitioner, hamnar det maximala lagrings utrymmet proportionellt.

![Användnings status relativt nivå gränser](./media/search-monitor-usage/usage-tab.png
 "Användnings status relativt nivå gränser")

## <a name="monitor-workloads"></a>Övervaka arbets belastningar

Loggade händelser inkluderar de som är relaterade till indexering och frågor. **Azure-diagnostik** -tabellen i Log Analytics samlar in användnings data relaterade till frågor och indexering.

De flesta av de loggade data är för skrivskyddade åtgärder. För andra åtgärder för att skapa-uppdatera-ta bort som inte har registrerats i loggen kan du söka efter system information i Sök tjänsten.

| OperationName | Beskrivning |
|---------------|-------------|
| ServiceStats | Den här åtgärden är ett rutin anrop för att [Hämta tjänst statistik](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics), antingen kallat direkt eller implicit för att fylla i en portal översikts sida när den läses in eller uppdateras. |
| Fråga. search |  Fråga begär Anden mot ett index se [övervaka frågor](search-monitor-queries.md) för information om loggade frågor.|
| Indexering. index  | Den här åtgärden är ett anrop för att [lägga till, uppdatera eller ta bort dokument](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). |
| index. Prototyp | Det här är ett index som skapats av guiden Importera data. |
| Indexerare. skapa | Skapa en indexerare explicit eller implicit via guiden Importera data. |
| Indexerare. Hämta | Returnerar namnet på en indexerare när indexeraren körs. |
| Indexerare. status | Returnerar status för en indexerare när indexeraren körs. |
| Data källor. get | Returnerar namnet på data källan när en indexerare körs.|
| Index. get | Returnerar namnet på ett index när en indexerare körs. |

### <a name="kusto-queries-about-workloads"></a>Kusto frågor om arbets belastningar

Om du har aktiverat loggning kan du fråga **AzureDiagnostics** efter en lista över åtgärder som kördes på din tjänst och när. Du kan också korrelera aktiviteter för att undersöka prestanda ändringar.

#### <a name="example-list-operations"></a>Exempel: list åtgärder 

Returnera en lista över åtgärder och antal var och en.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exempel: korrelera åtgärder

Korrelera förfrågan med indexerings åtgärder och återge data punkter i ett tids diagram för att se att åtgärderna är sammanfallna.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>Använd Sök-API: er

Både Azure Kognitiv sökning-REST API och .NET SDK ger programmerings åtkomst till tjänst statistik, index-och index information och dokument antal.

+ [Hämta tjänst statistik](/rest/api/searchservice/get-service-statistics)
+ [Hämta index statistik](/rest/api/searchservice/get-index-statistics)
+ [Hämta dokument antal](/rest/api/searchservice/count-documents)
+ [Hämta indexerings status](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>Övervaka användar åtkomst

Eftersom Sök index är en komponent i ett större klient program finns det ingen inbyggd metod för att kontrol lera åtkomsten till ett index. Förfrågningar antas komma från ett klient program, för antingen administratörs-eller fråge förfrågningar. Administratörens Läs-och skriv åtgärder omfattar att skapa, uppdatera och ta bort objekt i hela tjänsten. Skrivskyddade åtgärder är frågor mot dokument samlingen som är begränsade till ett enda index. 

Därför visas i loggarna referenser till anrop med hjälp av administratörs nycklar eller frågeinställningar. Lämplig nyckel ingår i begär Anden som härstammar från klient koden. Tjänsten är inte utrustad för att hantera identitets-tokens eller personifiering.

När det finns affärs krav för auktorisering per användare, är rekommendationen integrerad med Azure Active Directory. Du kan använda $filter-och användar identiteter för att [trimma Sök Resultat](search-security-trimming-for-azure-search-with-aad.md) för dokument som en användare inte bör se. 

Det finns inget sätt att logga den här informationen separat från frågesträngen som innehåller parametern $filter. Se [övervaka frågor](search-monitor-queries.md) för information om rapportering av frågesträngar.

## <a name="next-steps"></a>Nästa steg

Fluency med Azure Monitor är viktigt för att kunna ta hänsyn till alla Azure-tjänster, inklusive resurser som Azure Kognitiv sökning. Om du inte är bekant med Azure Monitor tar du tid att granska artiklar som är relaterade till resurser. Förutom självstudierna är följande artikel en bra plats att börja med.

> [!div class="nextstepaction"]
> [Övervaka Azure-resurser med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
