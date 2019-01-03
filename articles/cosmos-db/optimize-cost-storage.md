---
title: Optimera kostnaden för lagring i Azure Cosmos DB
description: Den här artikeln förklarar hur du hanterar kostnader för lagring för data som lagras i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 618be936ef0326a1b9f5db426d4c37bb2bd1c760
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535666"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optimera kostnaden för lagring i Azure Cosmos DB

Azure Cosmos DB erbjuder obegränsad lagring och dataflöde. Till skillnad från dataflöde, vilket du måste etablera/konfigurera på din Azure Cosmos-behållare eller databaser, debiteras lagringen baserat på förbrukning. Du debiteras bara för den logiska lagring som du använder och du behöver reservera all lagring i förväg. Storage automatiskt skalar upp eller ner baserat på de data som du lägger till eller ta bort till en Azure Cosmos DB-behållare.

## <a name="storage-cost"></a>Lagringskostnad

Lagring debiteras med GB-enhet. Lokal SSD-uppbackad lagring är används av dina data och indexering. Det totala lagringsutrymmet som används är lika med den lagring som behövs för data och index som används i alla regioner där du använder Azure Cosmos DB. Om du replikerar globalt ett Azure Cosmos-konto i tre regioner, betalar du för den totala lagringskostnaden i var och en av dessa tre regioner. Du kan beräkna din lagringskrav, se [kapacitetsplaneringsverktyget](https://www.documentdb.com/capacityplanner) verktyget. Kostnaden för lagring i Azure Cosmos DB är $0.25 GB/månad, se [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/) för senaste uppdateringarna. Du kan ställa in aviseringar för att fastställa lagringsutrymme som används av din Azure Cosmos-behållare för att övervaka din lagring, se [övervaka Azure Cosmos DB](monitor-accounts.md)) artikeln.

## <a name="optimize-cost-with-item-size"></a>Optimera kostnader med objektets storlek

Azure Cosmos DB förväntar sig objektets storlek till 2 MB eller mindre för optimala prestanda och kostnadsfördelar. Om du behöver en post för att lagra större än 2 MB av data kan du överväga att göra om schemat för objektet. Om du inte göra om schemat, kan du dela objektet i underobjekt och länka dem logiskt med en gemensam identifier(ID). Alla Azure Cosmos DB-funktioner arbetar konsekvent med inställningen till den logiska identifieraren.

## <a name="optimize-cost-with-indexing"></a>Optimera kostnader med indexering

Som standard indexeras data automatiskt, vilket kan öka det totala lagringsutrymmet som förbrukas. Du kan dock använda anpassade index principer för att minska det här arbetet. Automatisk indexering som inte har aktiverat via Grupprincip är cirka 10-20% av storleken på objektet. Genom att ta bort eller anpassa indexet principer kan du inte betalar extra kostnad för skrivningar och kräver inte ytterligare dataflödeskapacitet. Se [indexering i Azure Cosmos DB](indexing-policies.md) att konfigurera anpassade principer för indexering. Om du har arbetat med relationella databaser tidigare, kan du föreställa dig att ”indexera allt” innebär en fördubbling av lagring eller högre. I Azure Cosmos DB är median om det dock mycket lägre. I Azure Cosmos DB är indexlagring index vanligtvis låg (10-20%) även med automatisk indexering, eftersom det har utformats för ett låga storage-fotavtryck. Du kan styra tillkomsten av index fotavtryck och fråga på ett mer detaljerad sätt genom att hantera indexeringsprincipen.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimera kostnader med live- och ändringsflödet

När du behöver inte längre data du kan smidigt ta bort det från ditt Azure Cosmos-konto med hjälp av [livslängd](time-to-live.md), [ändringsflödet](change-feed.md) eller du kan migrera gamla data till ett annat datalager, till exempel Azure blob-lagring eller Azure data warehouse. TTL-värde eller TTL kan kan Azure Cosmos DB du ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Du kan ange tiden för live på behållarenivån och åsidosättningsvärde på basis av per objekt som standard. När du ställer in TTL-Perioden på en behållare eller på en nivå, tar Azure Cosmos DB automatiskt bort dessa objekt efter hur lång tid sedan du de senast ändrades. Med ändringsflödet kan migrera du data till antingen en annan behållare i Azure Cosmos DB eller till en extern datalager. Migreringen tar noll driftstopp och när du är klar migrerar, du kan ta bort eller konfigurera livslängd för att ta bort källa Azure Cosmos-behållaren.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optimera kostnader med rikt medieinnehåll datatyper 

Om du vill lagra omfattande medietyper, till exempel videor, bilder, osv., har ett antal alternativ i Azure Cosmos DB. Ett alternativ är att lagra dessa omfattande medietyper som Azure Cosmos-objekt. Det finns en gräns på 2 MB per objekt och du kan undvika den här gränsen genom länkning dataobjektet till flera underobjekt. Eller så kan du lagra dem i Azure Blob storage och använda metadata för att referera till dem från dina Azure Cosmos-objekt. Det finns ett antal- och nackdelar med den här metoden. Den första metoden får du bästa möjliga prestanda när det gäller svarstid, dataflöde serviceavtal plus nyckelfärdig global distribution funktioner för datatyper rik media förutom din vanliga Azure-Cosmos-objekt. Men stödet är tillgänglig på en högre kostnad. Du kan sänka de totala kostnaderna genom att lagra media i Azure Blob storage. Om svarstiden är viktiga, kunde du använda premium storage för filer som refereras till från Azure Cosmos-objekt. Detta är internt med CDN för att hantera avbildningar från edge-servern till en lägre kostnad för att kringgå geo-begränsningen. På sidan med det här scenariot är att du måste hantera två tjänster – Azure Cosmos DB och Azure Blob storage, som kan öka driftskostnaderna. 

## <a name="check-storage-consumed"></a>Kontrollera förbrukad lagring

Om du vill kontrollera lagringsanvändningen för ett Azure Cosmos-behållare, du kan köra en huvud- eller GET-begäran för behållaren, och inspektera de `x-ms-request-quota` och `x-ms-request-usage` rubriker. När du arbetar med .net SDK, du kan också använda den [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)), och [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) egenskaper för att hämta förbrukad lagring.

## <a name="using-sdk"></a>Med SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta till mer information om kostnadsoptimeringar i Azure Cosmos DB i följande artiklar:

* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [förstå din faktura för Azure Cosmos DB](understand-your-bill.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [optimera kostnaden för Azure Cosmos-konton för flera regioner](optimize-cost-regions.md)

