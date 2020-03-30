---
title: Tidigare prestandanivåer för Azure Cosmos DB
description: Lär dig mer om prestandanivåerna S1, S2 och S3 som tidigare var tillgängliga i Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 40735f91e2ca58cc42f723c7993686d92f0e5ff0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623331"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Dra tillbaka prestandanivåerna S1, S2 och S3

> [!IMPORTANT] 
> Prestandanivåerna S1, S2 och S3 som beskrivs i den här artikeln dras tillbaka och är inte längre tillgängliga för nya Azure Cosmos DB-konton.
>

Den här artikeln innehåller en översikt över prestandanivåerna S1, S2 och S3 och beskriver hur samlingarna som använder dessa prestandanivåer kan migreras till enskilda partitionerade samlingar. När du har läst den här artikeln kan du svara på följande frågor:

- [Varför är S1, S2 och S3 prestandanivåer pensioneras?](#why-retired)
- [Hur jämför enskilda partitionssamlingar och partitionerade samlingar med prestandanivåerna S1, S2, S3?](#compare)
- [Vad behöver jag göra för att säkerställa oavbruten åtkomst till mina data?](#uninterrupted-access)
- [Hur ändras min samling efter migreringen?](#collection-change)
- [Hur ändras min fakturering när jag har migrerats till enskilda partitionssamlingar?](#billing-change)
- [Vad händer om jag behöver mer än 20 GB lagringsutrymme?](#more-storage-needed)
- [Kan jag ändra mellan prestandanivåerna S1, S2 och S3 innan den planerade migreringen?](#change-before)
- [Hur migrerar jag från prestandanivåerna S1, S2, S3 till enskilda partitionssamlingar på egen hand?](#migrate-diy)
- [Hur påverkas jag om jag är EA-kund?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Varför är S1, S2 och S3 prestandanivåer dras tillbaka?

Prestandanivåerna S1, S2 och S3 erbjuder inte den flexibilitet som standardprogrammet för Azure Cosmos DB ger. Med prestandanivåerna S1, S2, S3, S3, var både dataflödet och lagringskapaciteten förinställda och erbjöd inte elasticitet. Azure Cosmos DB erbjuder nu möjligheten att anpassa ditt dataflöde och lagring, vilket ger dig mycket mer flexibilitet i din förmåga att skala när dina behov förändras.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Hur jämför enskilda partitionssamlingar och partitionerade samlingar med prestandanivåerna S1, S2, S3?

I följande tabell jämförs de dataflödes- och lagringsalternativ som är tillgängliga i enskilda partitionssamlingar, partitionerade samlingar och prestandanivåer för S1, S2, S3. Här är ett exempel för US East 2 region:

|   |Partitionerad samling|Samling av en partition|S1|S2|S3|
|---|---|---|---|---|---|
|Maximalt dataflöde|Unlimited|10K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Minsta dataflöde|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Maximal lagring|Unlimited|20 GB|20 GB|20 GB|20 GB|
|Pris (månadsvis)|Genomströmning: $6 / 100 RU/s<br><br>Lagring: $0.25/GB|Genomströmning: $6 / 100 RU/s<br><br>Lagring: $0.25/GB|$25 USD|$50 USD|$100 USD|

Är du EA-kund? Om så är fallet, se [Hur påverkas jag om jag är en EA-kund?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>Vad behöver jag göra för att säkerställa oavbruten åtkomst till mina data?

Om du har en S1-, S2- eller S3-samling bör du migrera samlingen till en enda partitionssamling programmässigt [med hjälp av .NET SDK](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Hur ändras min samling efter migreringen?

Om du har en S1-samling kan du migrera dem till en enda partitionssamling med 400 RU/s dataflöde. 400 RU/s är det lägsta dataflödet som finns med enpartitionssamlingar. Kostnaden för 400 RU/s i en enda partitionssamling är dock ungefär densamma som du betalade med din S1-kollektion och 250 RU/s – så du betalar inte för de extra 150 RU/s som är tillgängliga för dig.

Om du har en S2-samling kan du migrera dem till en enda partitionssamling med 1 K RU/s. Du kommer inte att se någon förändring av din dataflödesnivå.

Om du har en S3-samling kan du migrera dem till en enda partitionssamling med 2,5 K RU/s. Du kommer inte att se någon förändring av din dataflödesnivå.

I vart och ett av dessa fall, när du har migrerat samlingen, kan du anpassa dataflödesnivån eller skala upp och ned efter behov för att ge användarna åtkomst med låg latens. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Hur ändras min fakturering när jag har migrerat till enskilda partitionssamlingar?

Förutsatt att du har 10 S1 samlingar, 1 GB lagringsutrymme för varje, i regionen USA East, och du migrerar dessa 10 S1 samlingar till 10 enskilda partitionsamlingar på 400 RU/sek (miniminivån). Din faktura ser ut så här om du behåller de 10 enskilda partitionssamlingarna under en hel månad:

![Hur S1 prissättning för 10 samlingar kan jämföras med 10 samlingar med prissättning för en enda partition samling](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-20-gb-of-storage"></a>Vad händer om jag behöver mer än 20 GB lagringsutrymme?

Oavsett om du har en samling med S1-, S2- eller S3-prestandanivå, eller har en enda partitionssamling, som alla har 20 GB lagringsutrymme tillgängligt, kan du använda Azure Cosmos DB Data Migration-verktyget för att migrera dina data till en partitionerad samling med praktiskt taget obegränsad lagring. Information om fördelarna med en partitionerad samling finns [i Partitionering och skalning i Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Kan jag ändra mellan prestandanivåerna S1, S2 och S3 innan den planerade migreringen?

Endast befintliga konton med S1-, S2- och S3-prestanda kan ändras och ändra prestandanivånivåer programmässigt [med hjälp av .NET SDK](#migrate-diy). Om du ändrar från S1, S3 eller S3 till en enda partitionssamling kan du inte återgå till prestandanivåerna S1, S2 eller S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Hur migrerar jag från prestandanivåerna S1, S2, S3 till enskilda partitionssamlingar på egen hand?

Du kan migrera från prestandanivåerna S1, S2 och S3 till enpartitionssamlingar programmässigt [med hjälp av .NET SDK](#migrate-diy). Du kan göra detta på egen hand innan den planerade migreringen kan dra nytta av de flexibla dataflödesalternativen som är tillgängliga med enskilda partitionssamlingar.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrera till enskilda partitionssamlingar med hjälp av .NET SDK

Det här avsnittet omfattar endast ändring av en samlings prestandanivå med [SQL .NET API](sql-api-sdk-dotnet.md), men processen är liknande för våra andra SDK:er.

Här är ett kodavsnitt för att ändra samlingsdataflödet till 5 000 begärandeenheter per sekund:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Besök [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) för att se ytterligare exempel och lära dig mer om våra erbjudandemetoder:

* [**LäsOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ErsättOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**Skapa Erbjudandekry**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Hur påverkas jag om jag är EA-kund?

EA-kunder kommer att prisskyddas fram till slutet av sitt nuvarande kontrakt.

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om prissättning och hantering av data med Azure Cosmos DB kan du utforska följande resurser:

1.  [Partitionering av data i Cosmos DB](sql-api-partition-data.md). Förstå skillnaden mellan enpartitionsbehållare och partitionerade behållare, samt tips om hur du implementerar en partitioneringsstrategi för att skala sömlöst.
2.  [Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/). Läs mer om kostnaden för att etablera dataflöde och förbruka lagring.
3.  [Begär enheter](request-units.md). Förstå förbrukningen av dataflöde för olika operationstyper, till exempel Läs, Skriv, Fråga.
