---
title: Optimera lagringskostnaden i Azure Cosmos DB
description: I den här artikeln beskrivs hur du hanterar lagringskostnader för data som lagras i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 1508adda761fcba7ba70df3bb212d3eb4e32f242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754947"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Optimera lagringskostnaden i Azure Cosmos DB

Azure Cosmos DB erbjuder obegränsad lagring och dataflöde. Till skillnad från dataflöde, som du måste etablera/konfigurera på dina Azure Cosmos-behållare eller databaser, faktureras lagringen baserat på en förbrukningsbasis. Du faktureras endast för den logiska lagring du förbrukar och du behöver inte reservera någon lagring i förväg. Lagring skalas automatiskt upp och ned baserat på de data som du lägger till eller tar bort till en Azure Cosmos-behållare.

## <a name="storage-cost"></a>Lagringskostnad

Lagring faktureras med enheten för GB. Lokal SSD-stödd lagring används av dina data och indexering. Den totala lagring som används är lika med den lagring som krävs av data och index som används i alla regioner där du använder Azure Cosmos DB. Om du replikerar ett Azure Cosmos-konto globalt i tre regioner betalar du för den totala lagringskostnaden i var och en av dessa tre regioner. Information om hur du beräknar ditt lagringsbehov finns i [verktyget för kapacitetsplanerare.](https://www.documentdb.com/capacityplanner) Kostnaden för lagring i Azure Cosmos DB är $0.25 GB/månad, se [Prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/) för senaste uppdateringarna. Du kan ställa in aviseringar för att bestämma lagring som används av din Azure Cosmos-behållare, för att övervaka ditt lagringsutrymme, se [Övervaka Azure Cosmos DB](monitor-accounts.md)) artikel.

## <a name="optimize-cost-with-item-size"></a>Optimera kostnad med artikelstorlek

Azure Cosmos DB förväntar sig att artikelstorleken är 2 MB eller mindre för optimal prestanda och kostnadsfördelar. Om du behöver ett objekt för att lagra större än 2 MB data bör du överväga att omforma objektschemat. I den sällsynta händelsen att du inte kan omforma schemat kan du dela upp objektet i underobjekt och länka dem logiskt med en gemensam identifierare(ID). Alla Azure Cosmos DB-funktioner fungerar konsekvent genom att förankra den logiska identifieraren.

## <a name="optimize-cost-with-indexing"></a>Optimera kostnad med indexering

Som standard indexeras data automatiskt, vilket kan öka det totala lagringsutrymmet som förbrukas. Du kan dock använda anpassade indexprinciper för att minska den här omkostnaderna. Automatisk indexering som inte har justerats genom principen är cirka 10-20% av artikelstorleken. Genom att ta bort eller anpassa indexprinciper betalar du inte extra kostnad för skrivningar och kräver inte ytterligare dataflödeskapacitet. Se [Indexering i Azure Cosmos DB](indexing-policies.md) för att konfigurera anpassade indexeringsprinciper. Om du har arbetat med relationsdatabaser tidigare, kanske du tror att "index allt" betyder fördubbling av lagring eller högre. Men i Azure Cosmos DB, i medianfallet, det är mycket lägre. I Azure Cosmos DB är lagringsomkostnaderna för indexet vanligtvis låga (10–20 %) även med automatisk indexering, eftersom den är utformad för ett lågt lagringsutrymme. Genom att hantera indexeringsprincipen kan du styra avvägningen av indexavtryck och frågeprestanda på ett mer finkornigt sätt.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimera kostnaden med tid att leva och ändra flöde

När du inte längre behöver data kan du smidigt ta bort dem från ditt Azure Cosmos-konto med tiden [för att live,](time-to-live.md) [ändra feed](change-feed.md) eller så kan du migrera gamla data till ett annat datalager som Azure blob storage eller Azure-datalager. Med tid att leva eller TTL ger Azure Cosmos DB möjligheten att ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Som standard kan du ange tid för att leva på behållarnivå och åsidosätta värdet per artikel. När du har angett TTL på en behållare eller på en artikelnivå tar Azure Cosmos DB automatiskt bort dessa objekt efter tidsperioden sedan den tid de senast ändrades. Genom att använda ändringsfeed kan du migrera data till antingen en annan behållare i Azure Cosmos DB eller till ett externt datalager. Migreringen tar noll ned tid och när du är klar migrera, kan du antingen ta bort eller konfigurera tid att leva för att ta bort källan Azure Cosmos behållare.

## <a name="optimize-cost-with-rich-media-data-types"></a>Optimera kostnaden med multimediadatatyper 

Om du vill lagra multimediatyper, till exempel videor, avbildningar osv. Ett alternativ är att lagra dessa multimediatyper som Azure Cosmos-objekt. Det finns en gräns på 2 MB per objekt och du kan undvika den här gränsen genom att länka dataobjektet till flera underobjekt. Eller så kan du lagra dem i Azure Blob-lagring och använda metadata för att referera till dem från dina Azure Cosmos-objekt. Det finns ett antal fördelar och nackdelar med detta tillvägagångssätt. Den första metoden ger dig bästa prestanda när det gäller svarstid, dataflöde SLA plus nyckelfärdiga globala distributionsfunktioner för de omfattande mediedatatyperna utöver dina vanliga Azure Cosmos-objekt. Men stödet är tillgängligt till ett högre pris. Genom att lagra media i Azure Blob-lagring kan du sänka dina totala kostnader. Om svarstiden är kritisk kan du använda premiumlagring för multimediafiler som refereras från Azure Cosmos-objekt. Detta integreras inbyggt med CDN för att betjäna avbildningar från kanten servern till lägre kostnad för att kringgå geo-begränsning. Nackdelen med det här scenariot är att du måste hantera två tjänster - Azure Cosmos DB och Azure Blob-lagring, vilket kan öka driftskostnaderna. 

## <a name="check-storage-consumed"></a>Kontrollera att lagringsutrymmet förbrukats

Om du vill kontrollera lagringsförbrukningen för en Azure Cosmos-behållare kan du `x-ms-request-quota` köra `x-ms-request-usage` en HEAD- eller GET-begäran på behållaren och inspektera rubrikerna och rubrikerna. När du arbetar med .NET SDK kan du också använda egenskaperna [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))och [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) för att få lagringsutrymmet förbrukat.

## <a name="using-sdk"></a>Använda SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta att lära dig mer om kostnadsoptimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att förstå din Azure Cosmos DB-faktura](understand-your-bill.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [att optimera kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)

